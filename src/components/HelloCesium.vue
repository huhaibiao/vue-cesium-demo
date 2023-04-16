<!--
 * @Author: error: git config user.name && git config user.email & please set dead value or install git
 * @Date: 2022-10-01 23:25:47
 * @LastEditors: error: git config user.name && git config user.email & please set dead value or install git
 * @LastEditTime: 2022-10-06 17:42:04
 * @FilePath: /vue-cesium-demo/src/components/HelloCesium.vue
 * @Description: 
 * 
 * Copyright (c) 2022 by error: git config user.name && git config user.email & please set dead value or install git, All Rights Reserved. 
-->
<template>
  <div class="layer-btns">
    <div class="earth0" @click="backOrgin" :class="{ selected: selectBtn === -1 }">原图</div>
    <div
      v-for="(item, index) of ['冬', '夏', '春', '秋', '夜']"
      :key="item + index"
      @click="addImagery(index)"
      :class="{ selected: selectBtn === index }"
    >
      {{ `${item}` }}
    </div>
  </div>
</template>
<script lang="ts" setup>
import * as Cesium from 'cesium'
import { ref } from 'vue'
const cesiumToken =
  'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJqdGkiOiI1OTExZWNjYS03MGRhLTQ4YjAtODYyNS00MjA3YmRmNGZiYWQiLCJpZCI6NDU1MDUsImlhdCI6MTYxNTE4NDI2N30.YAQTkSDcHMRnxyDGwX1-EZNeicY3_m0bN-L1063G-nc'

// Cesium.Ion.defaultAccessToken = cesiumToken

const initViewer = () => {
  Cesium.Camera.DEFAULT_VIEW_RECTANGLE = Cesium.Rectangle.fromDegrees(90, -20, 110, 90)
  Cesium.Camera.DEFAULT_VIEW_FACTOR = 1.5

  const viewer = new Cesium.Viewer('cesium', {
    animation: false, //是否创建动画小器件，左下角仪表
    baseLayerPicker: false, //是否显示图层选择器
    fullscreenButton: false, //是否显示全屏按钮
    vrButton: false, // 用于切换 VR 模式的单个按钮小部件。
    geocoder: false, // //是否显示geocoder小器件，右上角查询按钮
    homeButton: false, //是否显示Home按钮
    infoBox: false, //是否显示信息框
    sceneModePicker: false, //是否显示3D/2D选择器
    selectionIndicator: false, //是否显示选取指示器组件
    timeline: false, //是否显示时间轴
    navigationHelpButton: false, //是否显示右上角的帮助按钮
    navigationInstructionsInitiallyVisible: false,
    scene3DOnly: false, //如果设置为true，则所有几何图形以3D模式绘制以节约GPU资源
    shouldAnimate: false, // 初始化是否开始动画
    clockViewModel: undefined, // 一个视图模型，它为用户界面提供 Clock
    selectedImageryProviderViewModel: undefined, //当前图像图层的显示模型，仅baseLayerPicker设为true有意义
    selectedTerrainProviderViewModel: undefined, //当前地形图层的显示模型，仅baseLayerPicker设为true有意义
    skyAtmosphere: new Cesium.SkyAtmosphere(), // 围绕提供的椭球体边缘绘制的大气
    fullscreenElement: document.body, //全屏时渲染的HTML元素,
    useDefaultRenderLoop: true, //如果需要控制渲染循环，则设为true
    targetFrameRate: undefined, //使用默认render loop时的帧率
    showRenderLoopErrors: false, //如果设为true，将在一个HTML面板中显示错误信息
    automaticallyTrackDataSourceClocks: false, //自动追踪最近添加的数据源的时钟设置
    contextOptions: {}, //传递给Scene对象的上下文参数（scene.options）
    sceneMode: Cesium.SceneMode.SCENE3D, //初始场景模式
    mapProjection: new Cesium.WebMercatorProjection(), //地图投影体系
    globe: undefined, // 在场景中渲染的地球仪，包括其地形 ( Globe#terrainProvider ) 和图像图层 ( Globe#imageryLayers )
    orderIndependentTranslucency: true,
    dataSources: new Cesium.DataSourceCollection(), //需要进行可视化的数据源的集合
    projectionPicker: undefined, //ProjectionPicker 是用于在透视和正交投影之间切换的单按钮小部件。
    // imageryProviderViewModels: Cesium.createDefaultImageryProviderViewModels(), //图层选择器,可供BaseLayerPicker选择的图像图层ProviderViewModel数组
    // terrainProviderViewModels: Cesium.createDefaultTerrainProviderViewModels(), //地形选择器,可供BaseLayerPicker选择的地形图层ProviderViewModel数组
    // imageryProvider: new Cesium.OpenStreetMapImageryProvider({
    //   credit: "",
    //   url: "Custom url",
    // }), //图像图层提供者，仅baseLayerPicker设为false有意义
    imageryProvider: new Cesium.ArcGisMapServerImageryProvider({
      url: `https://services.arcgisonline.com/ArcGIS/rest/services/World_Imagery/MapServer`
    }),
    terrainProvider: new Cesium.EllipsoidTerrainProvider(), //地形图层提供者，仅baseLayerPicker设为false有意义
    skyBox: new Cesium.SkyBox({
      sources: {
        positiveX: './skyBox/00h+00.jpg',
        negativeX: './skyBox/12h+00.jpg',
        positiveY: './skyBox/06h+00.jpg',
        negativeY: './skyBox/18h+00.jpg',
        positiveZ: './skyBox/06h+90.jpg',
        negativeZ: './skyBox/06h-90.jpg'
      }
    })
    // imageryProvider: new Cesium.SingleTileImageryProvider({
    //   //   url: '/earth_layer/EarthNight8K.jpg'
    //   url: '/earth_layer/earth-4.jpg'
    // })
  })
  //@ts-ignore
  viewer.cesiumWidget.creditContainer.style.opacity = 0
  //使用太阳作为光源，可以照亮地球。
  //   viewer.scene.globe.enableLighting = true
  //关闭地面大气效果，（默认为开启状态）
  viewer.scene.globe.showGroundAtmosphere = true

  viewer.scene.postProcessStages.fxaa.enabled = true
  //   icon.style.display = 'none'
  const entities = viewer.entities

  const stripeMaterial = new Cesium.StripeMaterialProperty({
    evenColor: Cesium.Color.WHITE.withAlpha(0.5),
    oddColor: Cesium.Color.BLUE.withAlpha(0.5),
    repeat: 5.0
  })
  let cyl = entities.add({
    // position: Cesium.Cartesian3.fromDegrees(-70.0, 40.0, 2000000.0),
    position: Cesium.Cartesian3.fromDegrees(-179, 0, 1050000),
    cylinder: {
      length: 1550000.0,
      topRadius: 0.0,
      bottomRadius: 200000.0,
      material: new Cesium.Color(0.2, 0.2, 0.7, 0.8471)
    }
  })

  viewer.scene.preRender.addEventListener(() => {})
  // viewer.zoomTo(cyl)
  return viewer
}
const viewer = initViewer()

let selectBtn = ref(-1)
let layCollections = new Cesium.ImageryLayerCollection()
let orginalLayer = viewer.imageryLayers.get(0)
const addImagery = (index: number) => {
  if (selectBtn.value === index) return
  selectBtn.value = index
  viewer.imageryLayers.removeAll(true)
  let url = `./earth_layer/earth-${index + 1}.jpg`
  if (index + 1 === 5) {
    url = './earth_layer/EarthNight8K.jpg'
  }
  viewer.imageryLayers.addImageryProvider(
    new Cesium.SingleTileImageryProvider({
      //   url: '/earth_layer/EarthNight8K.jpg'
      url
    })
  )
}

const backOrgin = () => {
  //   viewer.imageryLayers.removeAll(true)
  //   console.log('🚀 ~ file: HelloCesium.vue ~ line 130 ~ backOrgin ~ orginalLayer', orginalLayer)
  //   viewer.imageryLayers.add(orginalLayer, 0)
}

const addCircle = (viewer: Cesium.Viewer) => {
  const start = Cesium.JulianDate.fromDate(new Date(2022, 9, 10))
  const stop = Cesium.JulianDate.addSeconds(start, 360, new Cesium.JulianDate())
  function computeCirclularFlight() {
    const property = new Cesium.SampledPositionProperty()
    let tmpPosition = Cesium.Cartesian3.fromDegrees(-180, 0, 1550000),
      p = 0
    var targetPosition = tmpPosition
    // viewer.entities.add({
    //   name: '雷达四凌锥体',
    //   position: targetPosition,
    //   //@ts-ignore
    //   orientation: Cesium.Transforms.headingPitchRollQuaternion(
    //     targetPosition,
    //     new Cesium.HeadingPitchRoll(Cesium.Math.toRadians(0), Cesium.Math.toRadians(0), 0)
    //   ),
    //   ellipsoid: {
    //     radii: new Cesium.Cartesian3(600000, 600000, 600000),
    //     innerRadii: new Cesium.Cartesian3(10000, 10000, 10000),
    //     minimumClock: Cesium.Math.toRadians(-15),
    //     maximumClock: Cesium.Math.toRadians(-15),
    //     minimumCone: Cesium.Math.toRadians(75),
    //     maximumCone: Cesium.Math.toRadians(105),
    //     material: new Cesium.PolylineGlowMaterialProperty({
    //       glowPower: 0.1,
    //       color: Cesium.Color.YELLOW
    //     }),
    //     outline: true
    //   }
    // })
    property.addSample(Cesium.JulianDate.addSeconds(start, 0, new Cesium.JulianDate()), tmpPosition)

    for (let i = 45; i <= 360; i += 45) {
      const time = Cesium.JulianDate.addSeconds(start, i, new Cesium.JulianDate())
      const position = Cesium.Cartesian3.fromDegrees(-180 + i, 0, 1550000)
      property.addSample(time, position)
      const redLine = viewer.entities.add({
        name: 'Red line on terrain',
        polyline: {
          positions: Cesium.Cartesian3.fromDegreesArrayHeights([-180 + p, 0, 1750000, -180 + i, 0, 1750000]),
          width: 2,
          material: new Cesium.PolylineOutlineMaterialProperty({
            color: new Cesium.Color(0, 255, 21, 0.8471)
          })
        }
      })
      tmpPosition = position
      p = i
    }
    return property
  }

  const position = computeCirclularFlight()
  const satellite = viewer.entities.add({
    availability: new Cesium.TimeIntervalCollection([
      new Cesium.TimeInterval({
        start: start,
        stop: stop
      })
    ]),
    position,
    point: {
      pixelSize: 10,
      color: Cesium.Color.YELLOW
    },
    path: {
      resolution: 1,
      material: new Cesium.PolylineGlowMaterialProperty({
        glowPower: 0.1,
        color: Cesium.Color.YELLOW
      }),
      width: 10
    }
  })
  //@ts-ignore
  satellite.position.setInterpolationOptions({
    interpolationDegree: 5,
    interpolationAlgorithm: Cesium.LagrangePolynomialApproximation
  })
  viewer.clock.startTime = start.clone()
  viewer.clock.stopTime = stop.clone()
  viewer.clock.currentTime = start.clone()
  // viewer.clock.currentTime = start.clone()
  viewer.clock.clockRange = Cesium.ClockRange.LOOP_STOP
  viewer.clock.multiplier = 10
  viewer.clock.shouldAnimate = true
}
addCircle(viewer)
</script>
<style scoped>
.layer-btns {
  height: 50px;
  width: 100vw;
  display: flex;
  position: fixed;
  align-items: center;
  justify-content: center;
  z-index: 1;
  bottom: 20px;
  left: 0;
  background-color: rgba(240, 248, 255, 0.048);
  /* opacity: 0.75; */
}

.layer-btns > div {
  height: 30px;
  width: 60px;
  background-color: rgba(250, 235, 215, 0.326);
  margin: 0 10px;
  line-height: 30px;
  opacity: 0.9;
  border-radius: 10px;
  /* border: 1px solid rgba(0, 145, 255, 0.428); */
  border: 1px solid rgba(249, 250, 251, 0.428);
  color: #fff;
  font-family: PingFang SC-Medium, PingFang SC;
}
div.selected {
  border: 1px solid rgba(0, 255, 21, 0.847);
  /* border: 1px solid rgb(0, 255, 21); */
}
</style>
