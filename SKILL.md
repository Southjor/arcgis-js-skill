---
name: arcgis-js
description: 提供 ArcGIS Maps SDK for JavaScript 开发辅助，涵盖 2D/3D 地图视图、图层管理、数据可视化、空间分析等功能模块的参考和示例指导。
disable: false
allowed-tools: 
---

# ArcGIS Maps SDK for JavaScript Skill

本 skill 提供 ArcGIS JS API 的开发辅助，适用于需要创建 Web 地图应用、3D 场景可视化和空间分析功能的开发任务。

## 触发条件

使用本 skill 当用户请求：
- 创建 2D 地图或 3D 场景应用，但是以 3D 场景为主，除非用户要求 2D 地图
- 创建地图时，使用天地图资源作为底图，详情可见完整示例
- 实现 Mesh 自定义数据（顶点、索引、uv、法线等）渲染（https://developers.arcgis.com/javascript/latest/references/core/geometry/Mesh/#Mesh，https://developers.arcgis.com/javascript/latest/sample-code/geometry-mesh-primitives/）。
- 使用各种图层（IntegratedMeshLayer、FeatureLayer、SceneLayer、ImageryLayer 等），其中IntegratedMeshLayer、SceneLayer、GraphicLayer优先级更高。
- 实现 RenderNode 自定义 WebGL 渲染（https://developers.arcgis.com/javascript/latest/references/core/views/3d/webgl/RenderNode/，https://developers.arcgis.com/javascript/latest/sample-code/custom-render-node-windmills/）。
- 实现数据可视化和符号化渲染
- 进行空间分析（视域分析、高程剖面、通视分析等）
- 使用 API 组件和工具（Search、Legend、Editor、Sketch 等）

## 功能模块

### 1. 入门基础


**SceneView (3D 场景)**

#### 1.1 使用 tiainditu_view 初始化

```javascript

// 引入修复后的天地图工具类
const getTianditu = await $arcgis.import("https://openlayers.vip/examples/resources/tiainditu_view.js");

const view = new getTianditu.default({
	container: "mapContainer",
	center: [118.805, 34.027],
	zoom: 13,
});


const map = view.map;

view.when(() => console.log("三维地图加载完成！"));

```

#### 1.2 使用 tianditu 作为底图初始化

```javascript

const Map = await $arcgis.import("@arcgis/core/Map.js");

const SceneView = await $arcgis.import("@arcgis/core/views/SceneView.js");
const ElevationLayer = await $arcgis.import("@arcgis/core/layers/ElevationLayer.js");
const getTianditu = await $arcgis.import("https://openlayers.vip/examples/resources/tianditu_layers_local_es6.js");

// 初始矢量底图
const vecLayers = getTianditu.default({ type: "vec_w" });
const map = new Map({
	basemap: { baseLayers: [vecLayers.base, vecLayers.anno] },
	ground: {
		layers: [
			new ElevationLayer({
				url: 'https://www.geosceneonline.cn/image/rest/services/OpenData/ChinaTerrain3D/ImageServer'
			})
		],
	}
});

const view = new SceneView({
	container: "mapContainer",
	map: map,
	center: [118.805, 34.027],
	zoom: 13,
	tilt: 45
});


view.when(() => console.log("地图切换底图示例加载完成！"));


```


#### 1.3 组件化模式（components）初始化

```html

<!doctype html>
<html lang="en">
<head>
    <meta charset="utf-8"/>
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no"/>
    <title>Custom Basemap | Sample | ArcGIS Maps SDK for JavaScript</title>

    <link rel="stylesheet" href="https://js.arcgis.com/5.0/esri/themes/light/main.css"/>
    <script type="module" src="https://js.arcgis.com/5.0/"></script>

    <style>
        html,
        body {
            margin: 0;
            height: 100%;
        }
    </style>
</head>

<body>
<arcgis-scene id="viewDiv"
        camera-position="111.848111, 40.713906, 1990"
        camera-tilt="82.9"
        camera-heading="124.7">
    <arcgis-zoom slot="top-left"></arcgis-zoom>
    <arcgis-navigation-toggle slot="top-left"></arcgis-navigation-toggle>
    <arcgis-compass slot="top-left"></arcgis-compass>
    <arcgis-layer-list slot="top-right"></arcgis-layer-list>

</arcgis-scene>


<script type="module">
    const [IntegratedMeshLayer, Basemap, Map,
        SpatialReference,
    ] = await $arcgis.import([
        "@arcgis/core/layers/IntegratedMeshLayer.js",
        "@arcgis/core/Basemap.js",
        "@arcgis/core/Map.js",
        "@arcgis/core/geometry/SpatialReference.js",
    ]);

    const getTianditu = await $arcgis.import("https://openlayers.vip/examples/resources/tianditu.js");

    // 初始矢量底图
    const vecLayers = getTianditu.default({type: "vec_w"});

    const map = new Map({
        basemap: {baseLayers: [vecLayers.base, vecLayers.anno]},
    });

    // Get the Scene component and set the map and initial camera
    const viewElement = document.querySelector("arcgis-scene");

    const view = viewElement.view;

    view.map = map;

    console.log(view)

    // 图层1，图层处于高海拔地区（大约400米）

    var layer1 = new IntegratedMeshLayer({
        url: "http://3dws.geoscene.cn/server/rest/services/DSM_Mesh_SJZ_ktx2a0/SceneServer",
    });

    map.add(layer1);

    await viewElement.viewOnReady();

    view.extent = layer1.fullExtent;


    const arcgisLayerList = document.querySelector("arcgis-layer-list");
    await arcgisLayerList.componentOnReady();
    console.log("arcgis-layer-list is ready to go!");

</script>
</body>
</html>


```


### 2. 图层系统

**矢量图层**
- FeatureLayer - 要素图层，支持查询和编辑
- CSVLayer - CSV 数据加载
- GeoJSONLayer - GeoJSON 格式支持
- OGCFeatureLayer - OGC 标准服务

**影像图层**
- ImageryLayer - 影像服务图层
- ImageryTileLayer - 影像瓦片
- Multidimensional ImageryTileLayer - 多维时序数据

**3D 图层**
- SceneLayer - 场景图层（建筑物、BIM）
- PointCloudLayer - 点云数据
- IntegratedMeshLayer - 集成网格
- VoxelLayer - 体素图层（体数据可视化）

### 3. 数据可视化

**分级色彩渲染 (ClassBreaksRenderer)**
```javascript
layer.renderer = {
  type: "class-breaks",
  field: "population",
  classBreakInfos: [
    { minValue: 0, maxValue: 10000, symbol: ... },
    { minValue: 10000, maxValue: 50000, symbol: ... }
  ]
};
```

**唯一值渲染 (UniqueValueRenderer)**
```javascript
layer.renderer = {
  type: "unique-value",
  field: "type",
  uniqueValueInfos: [
    { value: "residential", symbol: ... },
    { value: "commercial", symbol: ... }
  ]
};
```

**数据驱动可视化变量**
- Color visual variable - 颜色变量
- Size visual variable - 大小变量
- Opacity visual variable - 透明度变量
- Rotation visual variable - 旋转变量

### 4. 空间分析

- Elevation Profile - 高程剖面分析
- Line of Sight - 通视分析
- Viewshed - 视域分析
- Shadow Cast - 阴影分析
- Aggregate Points - 点聚合统计

### 5. 常用组件

| 组件 | 功能 |
|------|------|
| Search | 地理搜索 |
| Legend | 图例显示 |
| LayerList | 图层管理 |
| Sketch | 草图绘制 |
| Editor | 要素编辑 |
| Popup | 弹出信息 |

### 6. 重要资源

**官方文档**
- API 参考: https://developers.arcgis.com/javascript/latest/references/
- 示例代码: https://developers.arcgis.com/javascript/latest/sample-code/
- 可视化示例代码: https://ralucanicola.github.io/JSAPI_demos/
- 博客: https://community.esri.com/t5/forums/searchpage/tab/message?filter=location&noSynonym=false&location=category:arcgis-api-for-javascript
- questions: https://community.esri.com/t5/arcgis-javascript-maps-sdk-questions/bd-p/arcgis-api-for-javascript-questions

**核心包**
- `@arcgis/core` - 核心 API (4.34+ 版本)
- `@arcgis/map-components` - 地图组件
- `@arcgis/charts-components` - 图表组件
- `@arcgis/ai-components` - AI 组件 (Beta)

## 使用流程

1. **需求分析**: 确定需要的地图类型（2D/3D）和功能模块
2. **环境准备**: 先检查是否引入在线或者本地资源，如果已经引入资源，则不检查环境；若没有引入，检查当前工程是否有环境，若没有，则使用 `@arcgis/core` 包安装 SDK。
3. **组件选择**: 根据功能需求选择对应的图层和组件
4. **代码实现**: 参考 references/api-guide.md 中的模式
5. **性能优化**: 注意图层销毁、事件监听清理、大型数据集的分块加载
6. **测试验证**: 在不同浏览器和设备上测试功能

## 注意事项

- 严格按照官方示例和API，严禁使用不存在的类和工具
- 使用天地图作为底图时，需要申请天地图API密钥
- 3D场景优先考虑 IntegratedMeshLayer、SceneLayer、GraphicLayer
- 实现自定义WebGL渲染时参考RenderNode文档
- 空间分析功能需要相应的分析服务支持
