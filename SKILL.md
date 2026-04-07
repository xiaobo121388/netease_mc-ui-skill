---
name: netease-mc-ui
description: "网易我的世界 JSON UI 和 Python 制作指南，包含基础知识、控件、数据绑定、事件、原生UI修改及接口参考。"
license: Complete terms in LICENSE.txt
---

# 网易我的世界 JSON UI 和 Python 制作指南

本 Skill 提供了在网易我的世界中开发自定义 UI 和修改原生 UI 的全面指南。内容基于官方文档整理，涵盖了从 JSON 编写到 Python 逻辑绑定的完整工作流。

## 1. 基础知识

在开始编写 UI 之前，必须了解 JSON 文件的组织结构和基本概念。

- **文件与命名**：每个 UI 都需要一个 JSON 文件，必须包含 `namespace` 字段。
- **主界面与命名空间**：每个 UI 必须定义 `namespace`，都应该有一个`screen`控件。
- **继承与变量**：使用 `@` 符号继承模板控件，使用 `$` 定义和引用控件变量。
- **全局变量与 _ui_defs**：在`_global_variables.json`中定义全局变量， 在 `_ui_defs.json` 中定义全局可用的模板和变量。
- **属性使用**：ui高级属性介绍，如`use_anchored_offset`、`property_bag`、`variables`等。
- **界面创建流程及生命周期**：UI 的创建在 `UiInitFinished` 事件后开始，涉及注册、实例化、获取实例和生命周期方法（Create, OnActive, OnDeactive, Destroy）。销毁 UI 可以通过调用 `SetRemove` 或 `PopScreen` 实现。

详细内容请参考：[基础知识](./references/基础知识.md)

## 2. UI 控件

JSON UI 提供了丰富的控件类型，用于构建各种界面元素。

- **基础控件**：`screen` (画布), `panel` (面板), `label` (文本), `image` (图片), `button` (按钮), `switch_toggle` (开关), `slider` (滑动条), `progress_bar` (进度条), `text_edit_box` (输入框)。
- **布局与容器**：`stack_panel` (栈式面板), `grid` (网格), `scroll_view` (滚动视图), `input_panel` (输入面板，支持拖拽和模态)。
- **高级控件**：`paper_doll` / `netease_paper_doll` (纸娃娃模型), `item_renderer` (物品渲染), `selection_wheel` (轮盘), `netease_combo_box` (下拉框), `mini_map` (小地图)。

详细内容请参考：[UI 控件](./references/UI控件.md)，并且大部分控件都有Python接口调整对应的值或者回调，具体在[全部自定义UI接口](./references/全部自定义UI接口.md)通过搜索控件名称可以找到对应的接口。

## 3. 数据绑定与按钮映射

数据绑定是连接 JSON 视图和 Python 逻辑的核心机制。

- **绑定类型**：支持布尔值、整数、浮点数、字符串、颜色以及各种点击和输入事件的绑定。
- **单个绑定**：使用 `@ViewBinder.binding` 装饰器将 Python 函数绑定到 JSON 中的变量。
- **集合绑定**：使用 `@ViewBinder.binding_collection` 为 `Grid` 等列表容器中的元素绑定数据。
- **按钮映射 (Button Mappings)**：将物理按键或特定交互映射到控件事件，常用于开启 `InputPanel` 拖拽或自定义按钮点击。

详细内容请参考：[数据绑定与按钮映射](./references/数据绑定.md)

## 4. UI 事件

通过监听客户端事件，可以在合适的时机创建 UI 或响应全局交互。

- **生命周期事件**：`UiInitFinished` (UI 系统初始化完成，适合注册和创建 UI), `PushScreenEvent` (界面入栈), `PopScreenEvent` (界面出栈)。
- **控件特定事件**：如 `GridComponentSizeChangedClientEvent`，用于处理滚动列表中的网格复用。
- **输入事件**：如 `ClientJumpButtonPressDownEvent`，用于拦截或响应底层输入。

详细内容请参考：[UI 事件](./references/UI事件.md)

## 5. 原生 UI 修改

除了自定义 UI，开发者经常需要修改游戏原生的界面。

- **JSON Modifications (静态修改)**：在同名 JSON 文件中使用 `modifications` 数组，通过 `insert_front`, `insert_back`, `replace`, `remove` 等操作增删改原生控件。
- **Python 代理 (动态修改)**：在 `PushScreenEvent` 中获取原生 UI 的 `ScreenNode` 实例，通过代理类动态修改控件属性或绑定新事件。

详细内容请参考：[原生 UI 修改](./references/原生UI修改.md) 
原版UI参考：[原生UI](./templates/vanilla_ui)

## 6. 接口 (API) 参考

Python API 提供了操作 UI 的各种方法。

- **ClientSystem API**：`RegisterUI`, `CreateUI`, `GetUI`, `PushScreen`, `PopScreen`。
- **ScreenNode API**：`GetBaseUIControl`, `GetAllChildrenPath`, `SetVisible`, `SetPosition`, `AddTouchEventHandler`, `SetUiItem`, `RenderPaperDoll` 等。

详细内容请参考：[核心接口参考](./references/核心接口.md) 和 [全部自定义UI接口](./references/全部自定义UI接口.md)。
