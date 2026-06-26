# ArcGIS Maps SDK for JavaScript API 参考指南

## 版本信息

- 当前推荐版本: 4.34+
- 包名: `@arcgis/core` (新架构) 或 `arcgis-js-api` (传统)

## 核心模块导入

```javascript
// 4.34+ 新架构
import Map from "@arcgis/core/Map";
import MapView from "@arcgis/core/views/MapView";
import SceneView from "@arcgis/core/views/SceneView";
import FeatureLayer from "@arcgis/core/layers/FeatureLayer";
import Graphic from "@arcgis/core/Graphic";
import GraphicsLayer from "@arcgis/core/layers/GraphicsLayer";
```

## 地图和视图

### Map 类

```javascript
import Map from "@arcgis/core/Map";

// 创建地图
const map = new Map({
  basemap: "streets-navigation",  // 底图类型
  // basemap: "satellite", "topo", "osm", "dark-gray"
  ground: "world-elevation",       // 3D 地形
  layers: []                       // 初始图层
});

// 添加图层
map.add(featureLayer);
map.addMany([layer1, layer2]);

// 移除图层
map.remove(featureLayer);
```

### MapView (2D)

```javascript
import MapView from "@arcgis/core/views/MapView";

const view = new MapView({
  map: map,
  container: "containerId",
  center: [lng, lat],              // 中心点坐标
  zoom: 10,                       // 缩放级别 (0-22)
  extent: {                       // 视图范围
    xmin, ymin, xmax, ymax,
    spatialReference: ...
  },
  rotation: 0,                    // 旋转角度
  ui: {                           // UI 组件
    components: ["zoom", "compass"]
  }
});

// 视图事件
view.on("click", (event) => {
  const { mapPoint, screenPoint } = event;
});
```

### SceneView (3D)

```javascript
import SceneView from "@arcgis/core/views/SceneView";

const view = new SceneView({
  map: map,
  container: "containerId",
  camera: {
    position: [lng, lat, height], // 相机位置
    heading: 0,                   // 航向角 (0-360)
    tilt: 0                       // 倾斜角 (0-90)
  },
  viewingMode: "local",           // local 或 global
  environment: {                  // 环境设置
    atmosphereEnabled: true,
    starsEnabled: true
  }
});

// 相机控制
view.goTo({
  target: graphic,
  heading: 180,
  tilt: 60,
  duration: 2000
});
```

## 图层系统

### FeatureLayer

```javascript
import FeatureLayer from "@arcgis/core/layers/FeatureLayer";

// 从服务创建
const layer = new FeatureLayer({
  url: "https://services.arcgis.com/.../ArcGIS/rest/services/.../FeatureServer/0",
  title: "My Layer",
  opacity: 0.8,
  visible: true
});

// 从 Portal Item 创建
const layer = new FeatureLayer({
  portalItem: {
    id: "item-id"
  }
});

// 属性设置
layer.outFields = ["*"];
layer.where = "population > 10000";
layer.orderByFields = ["name ASC"];

// 渲染器
layer.renderer = {
  type: "simple",
  symbol: {
    type: "fill",
    color: [255, 0, 0, 0.5],
    outline: { color: [255, 255, 0], width: 2 }
  }
};

// 查询
const query = layer.createQuery();
query.where = "type = 'residential'";
query.outFields = ["*"];
const results = await layer.queryFeatures(query);
```

### GraphicsLayer

```javascript
import GraphicsLayer from "@arcgis/core/layers/GraphicsLayer";
import Graphic from "@arcgis/core/Graphic";

const graphicsLayer = new GraphicsLayer({ title: "Graphics" });

// 创建图形
const graphic = new Graphic({
  geometry: {
    type: "point",
    longitude: -118.2437,
    latitude: 34.0522
  },
  symbol: {
    type: "marker",
    color: "blue",
    size: 12
  },
  attributes: {
    name: "Location A"
  }
});

graphicsLayer.add(graphic);
map.add(graphicsLayer);
```

### SceneLayer (3D)

```javascript
import SceneLayer from "@arcgis/core/layers/SceneLayer";

const sceneLayer = new SceneLayer({
  url: "https://services.arcgis.com/.../SceneServer",
  title: "Buildings",
  renderer: {
    type: "simple",
    symbol: {
      type: "mesh",
      material: { color: [200, 200, 200] }
    }
  }
});
```

### VoxelLayer (体素)

```javascript
import VoxelLayer from "@arcgis/core/layers/VoxelLayer";

const voxelLayer = new VoxelLayer({
  url: "https://services.arcgis.com/.../VoxelServer",
  title: "体素数据",
  voxelOrdering: "postorder",
  layerId: 0
});

// 获取体素变量
const variables = voxelLayer.variables;
```

## 可视化渲染

### SimpleRenderer

```javascript
layer.renderer = {
  type: "simple",
  symbol: {
    type: "marker",           // marker, line, fill, mesh
    color: [255, 128, 0],
    size: 10,
    outline: { color: "white", width: 1 }
  }
};
```

### ClassBreaksRenderer (分级)

```javascript
layer.renderer = {
  type: "class-breaks",
  field: "density",
  classBreakInfos: [
    {
      minValue: 0,
      maxValue: 25,
      symbol: { type: "fill", color: [255, 255, 204] }
    },
    {
      minValue: 25,
      maxValue: 75,
      symbol: { type: "fill", color: [255, 237, 160] }
    },
    {
      minValue: 75,
      maxValue: 150,
      symbol: { type: "fill", color: [254, 178, 76] }
    },
    {
      minValue: 150,
      maxValue: 400,
      symbol: { type: "fill", color: [253, 141, 60] }
    }
  ]
};
```

### UniqueValueRenderer (唯一值)

```javascript
layer.renderer = {
  type: "unique-value",
  field: "type",
  defaultSymbol: { type: "fill", color: [128, 128, 128] },
  uniqueValueInfos: [
    {
      value: "residential",
      symbol: { type: "fill", color: [230, 230, 230] }
    },
    {
      value: "commercial",
      symbol: { type: "fill", color: [179, 179, 179] }
    },
    {
      value: "industrial",
      symbol: { type: "fill", color: [128, 128, 128] }
    }
  ]
};
```

### Visual Variables (可视化变量)

```javascript
// 颜色可视化变量
layer.renderer = {
  type: "simple",
  symbol: { type: "marker" },
  visualVariables: [{
    type: "color",
    field: "temperature",
    stops: [
      { value: 0, color: [255, 255, 255] },
      { value: 100, color: [255, 0, 0] }
    ]
  }]
};

// 大小可视化变量
{
  type: "size",
  field: "population",
  minSize: 5,
  maxSize: 30,
  minDataValue: 0,
  maxDataValue: 100000
};

// 透明度可视化变量
{
  type: "opacity",
  field: "density",
  stops: [
    { value: 0, opacity: 0.1 },
    { value: 100, opacity: 1 }
  ]
};
```

## 空间分析

### ViewshedAnalysis

```javascript
import ViewshedAnalysis from "@arcgis/core/analysis/ViewshedAnalysis";

const viewshed = new ViewshedAnalysis({
  view: view,
  color: [255, 0, 0, 0.5],
  observerLocation: {
    type: "point",
    longitude: -118.2437,
    latitude: 34.0522,
    z: 100 // 观察者高度
  },
  targetDistance: 1000
});

viewshed.run();
```

### ElevationProfile

```javascript
import ElevationProfile from "@arcgis/core/analysis/ElevationProfile";

const profile = new ElevationProfile({
  view: view,
  unit: "meters",
  visibleElements: {
    legend: true,
    chart: true
  }
});

profile.input = [
  {
    type: "polyline",
    paths: [[[lng1, lat1], [lng2, lat2]]]
  }
];
```

## 常用组件

### Popup

```javascript
view.popup = {
  autoOpenEnabled: true,
  dockEnabled: true,
  dockOptions: {
    position: "bottom-left",
    buttonEnabled: false
  },
  highlightEnabled: true,
  collapseEnabled: true
};

view.popup.open({
  title: "Feature Info",
  content: "Description here...",
  location: mapPoint
});
```

### Sketch Widget

```javascript
import Sketch from "@arcgis/core/widgets/Sketch";

const sketch = new Sketch({
  view: view,
  layer: graphicsLayer,
  creationMode: "update"
});

view.ui.add(sketch, "top-right");
```

### Editor Widget

```javascript
import Editor from "@arcgis/core/widgets/Editor";

const editor = new Editor({
  view: view,
  layerInfos: [{
    layer: featureLayer,
    enabled: true,
    addEnabled: true,
    updateEnabled: true,
    deleteEnabled: true
  }]
});
```

### Legend Widget

```javascript
import Legend from "@arcgis/core/widgets/Legend";

const legend = new Legend({
  view: view,
  layerInfos: [{
    layer: featureLayer,
    title: "My Layer"
  }]
});
```

## 常用工具

### Query

```javascript
const query = layer.createQuery();
query.where = "population > 50000";
query.outFields = ["name", "population"];
query.returnGeometry = true;
query.num = 100;
query.start = 0;
query.orderByFields = ["population DESC"];

const results = await layer.queryFeatures(query);
results.features.forEach(feature => {
  console.log(feature.attributes);
});
```

### Geometry Engine

```javascript
import geometryEngine from "@arcgis/core/geometry/geometryEngine";

// 缓冲分析
const buffer = geometryEngine.buffer(point, 1000, "meters");

// 交集分析
const intersection = geometryEngine.intersect(polygon1, polygon2);

// 距离计算
const distance = geometryEngine.distance(point1, point2, "meters");

// 包含判断
const contains = geometryEngine.contains(polygon, point);
```

## 资源链接

- API Reference: https://developers.arcgis.com/javascript/latest/api-reference/
- Sample Code: https://developers.arcgis.com/javascript/latest/sample-code/
- Guide Docs: https://developers.arcgis.com/javascript/latest/guide/
- GitHub Samples: https://github.com/Esri/jsapi-resources