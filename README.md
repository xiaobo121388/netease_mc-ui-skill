# NetEase MC UI Skill

这是一个为《我的世界》网易版（NetEase Minecraft）UI 开发提供支持的 Copilot Skill 知识库。本仓库包含了丰富的 JSON UI 模板、API 接口参考以及针对网易版特性的开发指南，旨在帮助开发者更高效地创建和修改游戏内 UI。

## 目录结构

- `SKILL.md`: 技能的入口描述文件。
- `netease-mc-ui.skill`: 技能定义文件。
- `references/`: 参考文档目录，包含：
  - `基础知识.md`: JSON UI 及 Python 脚本结合的基础概念。
  - `全部自定义UI接口.md`: 完整的自定义 UI 接口列表。
  - `核心接口.md`: 经常使用的核心 API 详细说明。
  - `数据绑定.md`: UI 数据绑定机制和用法。
  - `原生UI修改.md`: 修改游戏原生 UI 的方法和注意事项。
  - `UI控件.md`: 各类 UI 控件的属性与用法。
  - `控件属性动画.md`: JSON 属性动画的完整配置与 Python 控制接口。
  - `UI事件.md`: 各种 UI 事件及其响应处理。
- `templates/`: UI 模板示例，包含 `vanilla_ui` 等各种原生预置模板文件，方便快速参考和二次开发。

## 核心特性

* **详尽的 API 文档**: 梳理了客户端与服务端的 UI 相关 API，明确 API 边界与命名规范。
* **丰富的代码示例**: 包括常见的 UI 数据绑定、控件配置及事件交互代码。
* **原生 UI 参考**: 整理了大量原生 UI 的 JSON 定义（如 `inventory_screen.json`, `hud_screen.json` 等），便于重载和修改。

## 贡献与完善

欢迎基于本知识库添加更多的 UI 开发技巧、踩坑经验记录或者新的 UI 组件模板。

## 许可证 (License)

本项目采用 [MIT License](LICENSE) 开源协议。
