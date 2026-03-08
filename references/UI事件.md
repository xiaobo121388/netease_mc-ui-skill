# UI 事件指南

在网易我的世界中，UI 事件是客户端与 UI 交互的桥梁。开发者可以通过监听这些事件来执行特定的逻辑，如在 UI 初始化完成后创建自定义界面，或在原生界面弹出时进行拦截和修改。

## 1. 核心生命周期事件

### UiInitFinished
- **描述**：UI 初始化完成事件。
- **触发时机**：当客户端的底层 UI 系统初始化完毕时触发。
- **用途**：这是创建自定义 UI 的最早时机。所有的 `RegisterUI` 和 `CreateUI` / `PushScreen` 操作都应该在这个事件触发之后进行。
- **参数**：无。

### PushScreenEvent
- **描述**：界面入栈事件。
- **触发时机**：当任何一个通过堆栈管理的界面（包括原生界面和通过 `PushScreen` 创建的自定义界面）被推入屏幕堆栈时触发。
- **用途**：常用于监听原生界面的打开（如暂停菜单、背包界面），以便获取其 `ScreenNode` 实例并进行修改。
- **参数**：
  - `screenName` (str): 界面的名称。
  - `screenDef` (str): 界面的全名（命名空间 + 界面名，如 `"pause.pause_screen"`）。

### PopScreenEvent
- **描述**：界面出栈事件。
- **触发时机**：当一个堆栈管理的界面被弹出（关闭）时触发。
- **用途**：用于监听界面的关闭，执行清理逻辑。
- **参数**：
  - `screenName` (str): 界面的名称。

## 2. 控件特定事件

某些复杂的控件在状态发生变化时会抛出特定的客户端事件，开发者可以监听这些事件来更新数据。

### GridComponentSizeChangedClientEvent
- **描述**：网格组件大小改变事件。
- **触发时机**：当 `Grid` 控件与 `ScrollView` 配合使用时，由于滚动导致网格内的子控件被复用或重新排列时触发。
- **用途**：在收到此事件后，开发者需要重新获取 Grid 的子节点路径，并根据索引重新绑定或设置数据，以防止滚动时内容错位。
- **参数**：
  - `path` (str): 触发事件的 Grid 控件的绝对路径。

## 3. 输入与交互事件

除了通过数据绑定（ViewBinder）处理的控件交互外，还有一些全局的输入事件与 UI 密切相关。

### ClientJumpButtonPressDownEvent
- **描述**：客户端跳跃按钮按下事件。
- **触发时机**：当玩家按下跳跃键（空格键或触屏跳跃按钮）时触发。
- **用途**：可用于在特定 UI 打开时拦截跳跃操作，或将跳跃键映射为 UI 的确认操作。

### 鼠标与键盘事件
虽然大部分鼠标点击和键盘输入由 UI 控件内部处理，但开发者可以通过设置 `always_accepts_input` 或 `absorbs_input` 属性来控制 UI 是否拦截这些底层输入事件，防止它们穿透到游戏世界中（例如，在 UI 上点击时不会破坏方块）。

## 4. 监听事件示例

在客户端的 `ClientSystem` 中监听 UI 事件的标准做法如下：

```python
import mod.client.extraClientApi as clientApi
ClientSystem = clientApi.GetClientSystemCls()

class MyClientSystem(ClientSystem):
    def __init__(self, namespace, systemName):
        super(MyClientSystem, self).__init__(namespace, systemName)
        
        # 监听 UI 初始化完成事件
        self.ListenForEvent(clientApi.GetEngineNamespace(), clientApi.GetEngineSystemName(), 
                            "UiInitFinished", self, self.OnUiInitFinished)
                            
        # 监听界面入栈事件
        self.ListenForEvent(clientApi.GetEngineNamespace(), clientApi.GetEngineSystemName(), 
                            "PushScreenEvent", self, self.OnPushScreen)

    def OnUiInitFinished(self, args):
        print("UI System Initialized. Ready to create custom UIs.")
        # 在这里执行 RegisterUI 和 CreateUI

    def OnPushScreen(self, args):
        screen_def = args.get('screenDef', '')
        print("Screen pushed: {}".format(screen_def))
        if screen_def == "pause.pause_screen":
            print("Pause screen opened!")
```
