# ArcGIS JS Skill

ArcGIS Maps SDK for JavaScript 开发辅助技能，为 AI 编程助手提供 ArcGIS JS API 的参考知识、示例代码和最佳实践指导。

## 功能覆盖

- **入门基础**: 2D/3D 地图视图创建，天地图底图初始化，组件化模式初始化
- **图层系统**: FeatureLayer、SceneLayer、IntegratedMeshLayer、VoxelLayer、GraphicsLayer 等
- **数据可视化**: ClassBreaksRenderer、UniqueValueRenderer、Visual Variables
- **空间分析**: 视域分析、高程剖面、通视分析、阴影分析
- **常用组件**: Search、Legend、Sketch、Editor、Popup 等

## 技能结构

```
arcgis-js/
├── SKILL.md              # 技能主文件（触发条件、功能模块、使用流程）
├── references/
│   └── api-guide.md      # API 参考指南（详细代码示例）
├── scripts/              # 辅助脚本目录
└── README.md             # 本文件
```

## 使用方式

在 CodeBuddy 中安装此技能后，当用户提出以下需求时会自动触发：

- 创建 2D 地图或 3D 场景应用
- 实现 Mesh 自定义数据渲染
- 使用各种图层（IntegratedMeshLayer、FeatureLayer、SceneLayer 等）
- 实现 RenderNode 自定义 WebGL 渲染
- 进行空间分析（视域分析、高程剖面、通视分析等）
- 使用 API 组件和工具

## 技术栈

- ArcGIS Maps SDK for JavaScript (4.34+)
- `@arcgis/core` 包
- 天地图底图资源（通过 openlayers.vip 提供的辅助工具）

## 许可

仅供学习和开发参考使用。
