# 原生 UI 修改指南

在网易我的世界中，除了创建完全自定义的 UI 外，开发者经常需要修改游戏原生的 UI 界面（如主界面、暂停菜单、背包等）。修改原生 UI 主要有两种方式：通过 JSON 文件的 `modifications` 机制进行静态修改，以及通过 Python 代理类进行动态修改。

## 1. JSON UI Modifications (静态修改)

`modifications` 是一种在不完全覆盖原生 JSON 文件的情况下，对原生 UI 控件进行增删改的机制。这种方式兼容性好，能与其他 Mod 的修改共存。

### 基础语法

在你的资源包中，创建一个与要修改的原生 UI 同名的 JSON 文件（例如 `ui/hud_screen.json`）。在文件中使用 `modifications` 数组来定义修改规则。

```json
{
  "namespace": "hud",
  "hud_screen": {
    "modifications": [
      {
        "array_name": "controls",
        "operation": "insert_back",
        "value": {
          "my_custom_panel@my_namespace.my_panel": {}
        }
      }
    ]
  }
}
```

### 操作类型 (operation)

`modifications` 支持以下几种操作：

- **`insert_front`**：在指定的数组（如 `controls`）最前面插入一个元素。常用于将自定义控件放在最底层。
- **`insert_back`**：在指定的数组最后面插入一个元素。常用于将自定义控件放在最顶层（覆盖在原生控件之上）。
- **`insert_after`**：在数组中指定的某个元素之后插入新元素。需要配合 `target` 字段指定目标元素。
- **`insert_before`**：在数组中指定的某个元素之前插入新元素。需要配合 `target` 字段指定目标元素。
- **`replace`**：替换数组中的某个元素。需要配合 `target` 字段。
- **`remove`**：移除数组中的某个元素。需要配合 `target` 字段。

### 示例：移除原生控件并插入自定义控件

```json
{
  "namespace": "pause",
  "pause_screen": {
    "modifications": [
      {
        "array_name": "controls",
        "operation": "remove",
        "target": "resume_button"
      },
      {
        "array_name": "controls",
        "operation": "insert_after",
        "target": "settings_button",
        "value": {
          "my_custom_button@my_namespace.my_button": {}
        }
      }
    ]
  }
}
```

## 2. Python 获取原生 UI 实例 (动态修改)

除了静态的 JSON 修改，开发者还可以通过 Python 脚本在运行时获取原生 UI 的实例，并对其进行动态修改（如隐藏控件、修改文本、绑定新事件等）。

### 获取原生 UI 实例

要修改原生 UI，首先需要获取其 `ScreenNode` 实例。这通常在监听到 `PushScreenEvent` 时进行。

```python
import mod.client.extraClientApi as clientApi

def OnPushScreen(self, args):
    screen_def = args.get('screenDef', '')
    if screen_def == "pause.pause_screen":
        # 获取原生 UI 的 ScreenNode 实例
        pause_ui_node = clientApi.GetTopUI()
```
  
## 3. 使用代理类 (Proxy)

### 注册

```python
NativeScreenManager = clientApi.GetNativeScreenManagerCls()
NativeScreenManager.instance().RegisterScreenProxy(
    screenName,      # 如 "pause.pause_screen"
    proxyClassPath   # 如 "modScripts.proxys.MyProxy.MyProxy"
)
```

### 常用原生界面名称

| 界面 | 名称 |
|------|------|
| 暂停界面 | `"pause.pause_screen"` |
| 背包界面 | `"inventory_screen.inventory_screen"` |
| 聊天界面 | `"chat_screen.chat_screen"` |
| HUD界面 | `"hud_screen.hud_screen"` |
| 开始界面 | `"start_screen.start_screen"` |

### 代理类

```python
CustomUIScreenProxy = clientApi.GetUIScreenProxyCls()

class MyProxy(CustomUIScreenProxy):
    def OnCreate(self):
        """原生界面打开时调用。"""
        screen = self.GetScreenNode()
        # 添加/修改控件

    def OnDestroy(self):
        """原生界面关闭时调用。"""
        pass
    def OnTick(self):
        """每帧调用一次。"""
        pass
    def GetScreenNode(self):
        """获取原生界面的 ScreenNode。"""
        pass
```

### 注意事项

1. **路径获取**：原生 UI 的控件路径可能非常深且复杂。建议在游戏中打开该 UI，然后使用 `GetAllChildrenPath` 接口打印出所有路径或者使用ui调试工具，以便找到目标控件的准确绝对路径。
2. **生命周期**：原生 UI 可能会被频繁地 Push 和 Pop。确保你的代理类能够正确处理这些生命周期事件，避免内存泄漏或重复绑定事件。
3. **数据绑定冲突**：原生 UI 的很多属性（如可见性、文本）是由引擎内部的数据绑定控制的。如果你通过 Python 强制修改了这些属性（如 `SetVisible(False)`），当引擎内部状态改变时，可能会覆盖你的修改。在这种情况下，考虑使用 JSON `modifications` 直接移除该控件，或者修改其父节点的可见性。
