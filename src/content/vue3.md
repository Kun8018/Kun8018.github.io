---
title: Vue.js前端框架(三)
date: 2020-10-02 21:40:33
categories: IT
tags:
    - IT，Web,Vue
toc: true
thumbnail: https://cdn.kunkunzhang.top/vue3.png
---

　　vue新版本解析，代号”one-piece“

<!--more-->

## 其他组件

clipboard-polyfill xgplayer qrcode

### vue-i18n-next

https://github.com/intlify/vue-i18n-next

Vue i18n解决方案



### 拖动组件

vue-draggable-resizable-gorkys

vue-draggable-resizable



### vue-echarts

安装

```shell
npm install echarts vue-echarts
```

在vue2中使用还需要安装compostion包

```shell
npm i -D @vue/composition-api
```

如果在NuxtJS中使用还需要安装另外一个包

```shell
npm i -D @nuxtjs/composition-api
```

并且在nuxt.config.js中添加配置

```javascript
@nuxtjs/composition-api/module
```

在vue3中使用

```javascript
import { createApp } from 'vue'
import ECharts from 'vue-echarts'
import { use } from "echarts/core"

// import ECharts modules manually to reduce bundle size
import {
  CanvasRenderer
} from 'echarts/renderers'
import {
  BarChart
} from 'echarts/charts'
import {
  GridComponent,
  TooltipComponent
} from 'echarts/components'

use([
  CanvasRenderer,
  BarChart,
  GridComponent,
  TooltipComponent
])

const app = createApp(...)

// register globally (or you can do it locally)
app.component('v-chart', ECharts)

app.mount(...)
```

在vue2中使用

```javascript
import Vue from 'vue'
import ECharts from 'vue-echarts'
import { use } from 'echarts/core'

// import ECharts modules manually to reduce bundle size
import {
  CanvasRenderer
} from 'echarts/renderers'
import {
  BarChart
} from 'echarts/charts'
import {
  GridComponent,
  TooltipComponent
} from 'echarts/components'

use([
  CanvasRenderer,
  BarChart,
  GridComponent,
  TooltipComponent
]);

// register globally (or you can do it locally)
Vue.component('v-chart', ECharts)

new Vue(...)
```



### 轮播图

安装包

```js
npm install vue-awesome-swiper --save
```

引入

```js
//全局引入
import VueAwesomeSwiper from 'vue-awesome-swiper'

import 'swiper/dist/css/swiper.css'
Vue.use(VueAwesomeSwiper)
//组件引入
import 'swiper/dist/css/swiper.css'

import {Swiper,swiperSlide} from 'vue-awesome-swiper'
```



修改swiper属性，自动播放与延时

```js

```

### vue-starport

提供页面与页面/组件与组件之间切换的动画

安装

```shell
npm i vue-starport
```

使用

```vue
<script setup>
import { StarportCarrier } from 'vue-starport'
</script>

<template>
  <StarportCarrier> <!-- here -->
    <RouterView />
  </StarportCarrier>
</template>
```



### volar

https://github.com/johnsoncodehk/volar



### 验证码

https://www.npmjs.com/package/vue2-verify



### 多级菜单

递归组件实现

子组件,MenuItem 是一个 li 标签和 slot 插槽，允许往里头加入各种元素

```vue
// Menuitem
<template>
  <li class="item">
    <slot />
  </li>
</template>
```

父组件Menu,Menu中有两种情况需要做判断，一种是 item 没有 children 属性，直接在 MenuItem 的插槽加入一个 span 元素渲染 item 的 title 即可；另一种是包含了 children 属性的 item 这种情况下，不仅需要渲染 title 还需要再次引入 Menu 做递归操作，将 item.children 作为路由传入到 router prop

```vue
<!-- Menu -->

<template>
  <ul class="wrapper">
    <!-- 遍历 router 菜单数据 -->
    <menuitem :key="index" v-for="(item, index) in router">
      <!-- 对于没有 children 子菜单的 item -->
      <span class="item-title" v-if="!item.children">{{item.name}}</span>

      <!-- 对于有 children 子菜单的 item -->
      <template v-else>
        <span @click="handleToggleShow">{{item.name}}</span>
        <!-- 递归操作 -->
        <menu :router="item.children" v-if="toggleShow"></menu>
      </template>
    </menuitem>
  </ul>
</template>

<script>
  import MenuItem from "./MenuItem";

  export default {
    name: "Menu",
    props: ["router"], // Menu 组件接受一个 router 作为菜单数据
    components: { MenuItem },
    data() {
      return {
        toggleShow: false // toggle 状态
      };
    },
    methods: {
      handleToggleShow() {
        // 处理 toggle 状态的是否展开子菜单 handler
        this.toggleShow = !this.toggleShow;
      }
    }
  };
</script>
```

路由数据

```javascript
[
  {
    name: "About",
    path: "/about",
    children: [
      {
        name: "About US",
        path: "/about/us"
      },
      {
        name: "About Comp",
        path: "/about/company",
        children: [
          {
            name: "About Comp A",
            path: "/about/company/A",
            children: [
              {
                name: "About Comp A 1",
                path: "/about/company/A/1"
              }
            ]
          }
        ]
      }
    ]
  },
  {
    name: "Link",
    path: "/link"
  }
];
```

### markdown编辑器

mavon-editor

```js
npm install mavon-editor --save
```

在app.js引入

```js
import mavonEditor from 'mavon-editor'
Vue.use(mavonEditor)
```

在页面引入

```vue
<template>
<div>
    <mavon-editor v-model='content'/>
    </div>
</template>
<script>
export default{
    data(){
        return{
            content:'',
        }
    },
}
</script>
```





### 直播



播放器使用的是 [vue-video-player](https://github.com/surmon-china/vue-video-player)，其实就是 [video.js](https://github.com/videojs/video.js) 集成到 vue 中，后台主要输出 RTMP 和 HLS 的直播流

1. 如果需要播放 HLS 流，需要安装 [videojs-contrib-hls](https://github.com/videojs/videojs-contrib-hls) 插件，非原生支持的浏览器，直播服务端需要开启 CORS（后面会讲到）
2. 如果需要播放 RTMP 流，需要安装 [videojs-flash](https://github.com/videojs/videojs-flash) 插件
3. 如果两个流都需要播放，flash 插件需要安装到 hls 插件之前

兼容性：

1. RTMP: 上面说了 RTMP 是 Adobe 公司研发的协议，目前主要的直播服务都主推 RTMP 流，它延时小，但是需要 flash 插件的支持，也需要的上面提到的安装 `videojs-flash` 的插件。但是在 MAC 下对 flash 插件支持不友好，而且 MAC 下的 flash 插件 firefox 浏览器和 chrome 还是两个插件。。这就很尴尬。
2. HLS: 这个协议兼容性较好，但是最大的缺点是延迟较高，大概 20s 左右，所以只能当做备选方案。

说 HLS 兼容性较好，主要是指可以通过 JS 让用户免配置（不必安装flash），可以在 caniuse 看下 HLS 的支持程度

最后我们使用的方案是。优先使用 RTMP 流，如果不支持，就切换到 HLS 流。好在这个切换过程 video.js 会自动替我们做。下面贴一下相关配置代码。

https://segmentfault.com/a/1190000011346597



### 循环播放插件

Vue-seamless-scroll

安装

```shell
npm install vue-seamless-scroll --save
```

在文件中引入

```javascript
import vueSeamlessScroll from 'vue-seamless-scroll'

export default {
  data(){
    return {
     components:{
       vueSeamlessScroll
     }
    }
  }
}
```

在页面中使用

```vue
<template>
  <vue-seamless-scroll :data="Array" :class-option="classOption">
     	<ul>
      	<li>
          
				</li>
  		</ul>
  </vue-seamless-scroll>
</template>
<script>
export default {
  computed:{
    classOption () {
      return {
        //上下滚动时父容器指定height和overflow：hidden，左右滚动时指定width
        step:0.2 //数值越大，滚动速度越快
        limitMoveNum: 2 //开始无缝滚动的数据量
        hoverStop: true //是否开启鼠标悬停stop
        direction: 0 //0向下，1向上，2向左，3向右
      }
    }
  }
}
</script>
```







### vue-auth

安装包

```js
npm install @websnaova/vue-auth
```



### vue-pdf



### 输出导入excel文件

使用sheetjs库

安装

```shell
npm install xlsx
```



### 输出打印

vue-print-nb

安装

```shell
npm install --save vue-print-nb
```

在main.js中全局引入

```javascript
import Print from 'vue-print-nb'

Vue.use(Print)
```

通过id打印

```vue
<tr id="printInfo">
  
</tr>
<el-button 
   type = "primary"
   key = "printInfo"
   v-print = "'#printInfo'">
</el-button>
```

要打印的组件必须渲染出来，否则会报错。



### 地图

leafletjs

leaflet是一个对移动端优化的交互地图并且开源的JavaScript库，是一个十分轻量级的WebGIS库。

安装

```shell
npm install leaflet --save
```

引入 



vue-amap

安装包

```npm
npm install -S vue-amap // 可在package.json查看是否安装
```

在入口文件main.js引入

```javascript
    // 引入高德地图
    // 高德地图组件使用
    import VueAMap from 'vue-amap'
    
    Vue.config.productionTip = false
    Vue.use(VueAMap);
    VueAMap.initAMapApiLoader({
      key: 'your amap key', 
      plugin: [
      'AMap.Autocomplete', 
      'AMap.PlaceSearch', // POI搜索插件
      'AMap.Scale', // 右下角缩略图插件 比例尺
      'AMap.OverView', 
      'AMap.ToolBar', // 地图工具条
      'AMap.MapType', 
      'AMap.PolyEditor', 
      'AMap.CircleEditor',// 圆形编辑器插件
      'AMap.Geolocation'// 定位控件，用来获取和展示用户主机所在的经纬度位置
      ],
      // 默认高德 sdk 版本为 1.4.4
      v: '1.4.4'
    });
```

实例

```vue
<div class="getlocation">
  定位
</div>
<el-amap vid="amap" :plugin="plugin" class="amap-demo" :center="center">		</el-amap>
<div class="toolbar">
  <span v-if="loaded">location: lng = {{ lng }} lat = {{ lat }}</span>
	<span v-else>正在定位</span>
</div>
<script>
  export default {
    data() {
      let self = this;
      return {
        center: [121.59996, 31.197646],
        lng: 0, 
        lat: 0,
        loaded: false,
        plugin: [
          {
            pName: "Geolocation", //定位
            events: {
              init(o) {
                // o 是高德地图定位插件实例
                o.getCurrentPosition((status, result) => {
                  if (result && result.position) {
                    console.log(status, result);
                    self.lng = result.position.lng; //设置经度
                    self.lat = result.position.lat; //设置维度
                    self.center = [self.lng, self.lat]; //设置中心坐标
                    self.loaded = true;
                    self.$nextTick();
                  }
                });
              }
            }
          }
        ]
      };
    },
</script>    
<style>
  .getlocation{
    margin-left:4rem;
    font-size:15px;
    font-weight: 500;
    margin-top:0.3rem;
  }
  .amap-demo {
    height: 15rem;
    margin-top: 0.3rem;
  }
</style>
```

百度地图

安装

```node
npm i --save vue-baidu-map
```

初始化

```js
import Vue from 'vue'
import BaiduMap from 'vue-baidu-map'

Vue.use(BaiduMap, {
  /* Visit http://lbsyun.baidu.com/apiconsole/key for details about app key. */
  ak: 'YOUR_APP_KEY'
})
```



```vue
<template>
  <baidu-map class="map">
  </baidu-map>
</template>

<style>
/* The container of BaiduMap must be set width & height. */
.map {
  width: 100%;
  height: 300px;
}
</style>
```



### 导航

使用ip定位，需要在head标签里面引入js

```html
<script src="https://pv.sohu.com/cityjson?ie=utf-8"></script>
```

在vue中使用

```vue
<script>
  mounted () {
    this.loadBaiduMapAsync()
  },
  methods: {
    /**
     * 初始化百度地图并定位用户当前位置
     */
    loadBaiduMapAsync() {
      // 加载百度地图js
      let script = document.createElement('script')
      script.src = 'https://api.map.baidu.com/api?v=2.0&ak=申请的key&callback=initMap'
      document.body.appendChild(script)
      script.onload = (data) => { // 地图js加载成功
        console.log('百度地图JS加载成功，开始定位')
        window.initMap = this.startLocate
      }
      script.onerror = (data) => { // 地图js加载失败
        console.log('百度地图JS加载失败，无法定位')
        common.showConfirm('温馨提示','地图加载出错，请重试！', () => { this.loadBaiduMapAsync() }, null, '重新加载')
      }
    },
    /**
     * 获取用户当前定位 -- APP端端
     */
    startLocate() {
      if (this.isApp) { // APP端
          this.getPositionByAPP().then(point => {
            console.log('APP定位成功--位置:', point);
            this.currPos = point
            this.showMapAndGuide() // 展示地图及导航
          }).catch(msg => {
            this.status = 0
            console.log(msg);
            // common.showToast(msg, CONSTANTS.ERR_TOAST_TIME)
            this.showDestination() // 展示地图和目的地
          })
      } else { // 浏览器端
        this.getPositionByH5().then(point => {
          console.log('浏览器定位成功--位置:', point);
          this.currPos = point
          this.showMapAndGuide() // 展示地图及导航
        }).catch(msg => {
          this.status = 0
          console.log(msg);
          // common.showToast(msg, CONSTANTS.ERR_TOAST_TIME)
          this.showDestination() // 展示地图和目的地
        })
      }
    },
    /**
     * 获取用户当前定位 -- APP端端
     */
    getPositionByAPP() {
      return new Promise(function(resolve, reject) {
        const cb = (err, data) => {
          if (err) {
            if (err.code === '001') { // 未开启定位服务
              reject('请检查您的定位服务是否开启')
            } else if (err.code === '002') { // 未授权应用访问定位功能
              reject('请授权APP访问您的位置信息')
            } else {
              console.warn(err);
              reject('定位失败,位置信息不可用')
            }
          } else { // app定位成功
            let point = {
              longitude: data.Longitude,
              latitude: data.Latitude
            }
            if (this.isiOS) { // IOS坐标转换
              console.log('IOS坐标转换')
              const convertor = new BMap.Convertor()
              convertor.translate([new BMap.Point(point.longitude, point.latitude)], 1, 5, res => { // 经纬度转换，否则会定位不准
                if (res.status === 0) {
                  point.latitude = res.points[0].lat
                  point.longitude = res.points[0].lng
                  resolve(point)
                }
              })
            } else { // Andorid坐标
              resolve(point)
            }
          }
        }
        app.getCurrentLocation(cb)
      })
    },
    /**
     * 获取用户当前定位 -- 浏览器端
     */
    getPositionByH5() {
      return new Promise(function(resolve, reject) {
        const locateByIP = function () {
          if (window.returnCitySN && window.returnCitySN.cip) { // 根据IP，通过百度api获得经纬度
            console.log('returnCitySN:', window.returnCitySN);
            $.getJSON("https://api.map.baidu.com/location/ip?callback=?", {
              'ak' : '申请的百度地图key',
              'coor' : 'bd09ll', // 百度经纬度坐标
              'ip' : window.returnCitySN.cip // {cip: "116.77.145.35", cid: "440300", cname: "广东省深圳市"}
            }, function(data) {
              if (data && data.content) {
                resolve({
                  longitude: data.content.point.x,
                  latitude: data.content.point.y
                })
              } else {
                reject('定位失败,位置信息不可用')
              }
            })
          }
        }

        if (navigator.geolocation) {
          navigator.geolocation.getCurrentPosition(function(pos) {
            resolve({
              longitude: pos.coords.longitude,
              latitude: pos.coords.latitude
            })
          }, function(error) {
            let msg = ''
            switch(error.code) {
                case error.PERMISSION_DENIED:
                    msg="用户拒绝对获取地理位置的请求。"
                    break;
                case error.POSITION_UNAVAILABLE:
                    msg="位置信息是不可用的。"
                    break;
                case error.TIMEOUT:
                    msg="请求用户地理位置超时。"
                    break;
                case error.UNKNOWN_ERROR:
                    msg="未知错误。"
                    break;
            }
            reject(msg)
            // console.warn('浏览器端(geolocation定位失败)-根据IP定位');
            // locateByIP()
          }, {
            enableHighAccuracy: true, // 指示浏览器获取高精度的位置，默认为false
            timeout: 5000, // 指定获取地理位置的超时时间，默认不限时，单位为毫秒
            maximumAge: 2000 // 最长有效期，在重复获取地理位置时，此参数指定多久再次获取位置。
          })
        } else {
          reject('定位失败,当前浏览器不支持定位！')
          // console.warn('浏览器端(geolocation定位不支持)-根据IP定位');
          // locateByIP()
        }
      })

    },
    /**
     * 展示目标位置
     */
    showDestination() {
      if (Number.isNaN(this.lng)|| Number.isNaN(this.lat)) {
        common.showAlert('地址参数错误！', CONSTANTS.ERR_TOAST_TIME)
        return
      }
      // 展示地图
      const map = new BMap.Map("allmap")
      const endPos = new BMap.Point(this.urlParams.lng, this.urlParams.lat)
      // 展示目标位置
      this.status = 2
      map.centerAndZoom(endPos, 16)
      map.addOverlay(new BMap.Marker(endPos))
      map.enableScrollWheelZoom(true)
      // 在右上角添加缩放控件
      const zoom = new BMap.NavigationControl({
        anchor: BMAP_ANCHOR_TOP_RIGHT,
        type: BMAP_NAVIGATION_CONTROL_LARGE,
        enableGeolocation: true
      })
      map.addControl(zoom)
    },
    /**
     * 展示地图并导航
     */
    showMapAndGuide() {
      if (Number.isNaN(this.lng)|| Number.isNaN(this.lat)) {
        common.showAlert('页面地址参数错误！', CONSTANTS.ERR_TOAST_TIME)
        return
      }
      // 展示地图
      const map = new BMap.Map("allmap")
      const startPos = new BMap.Point(this.currPos.longitude, this.currPos.latitude) // {lng: 114.02597366, lat: 22.54605355}
      const endPos = new BMap.Point(this.lng, this.lat)

      // 逆地址解析用户当前所在地址
      const geoc = new BMap.Geocoder()
      geoc.getLocation(startPos, res => {
        const currCity = res.addressComponents.city // 用户当前定位所在城市
        console.log(`定位城市：${currCity} -- 球场地址：${this.address}`);
        if (this.cityName && this.cityName.indexOf(currCity) > -1) { // 用户和目标球场在同一城市
          this.status = 1
          map.centerAndZoom(startPos, 16)
          this.driveRoute(map, startPos, endPos, true)
        } else { // 用户和目标球场不在同一城市
          this.status = 2
          map.centerAndZoom(endPos, 16)
          map.addOverlay(new BMap.Marker(endPos))
          // this.driveRoute(map, startPos, endPos, true)
        }
      })

      map.enableScrollWheelZoom(true)
      // 在右上角添加缩放控件
      const zoom = new BMap.NavigationControl({
        anchor: BMAP_ANCHOR_TOP_RIGHT,
        type: BMAP_NAVIGATION_CONTROL_LARGE,
        enableGeolocation: true
      })
      map.addControl(zoom)
    },
    /**
     * 计算驾驶路线
     * @param startPos BMap.Point 起点位置
     * @param endPos BMap.Point 终点位置
     * @param show Boolean
     */
    driveRoute(map, startPos, endPos, show) {
      const DRIVE = new BMap.DrivingRoute(map, {
        renderOptions: {
          map: map,
          autoViewport: show,
        },
        onSearchComplete: res => {
          let plan = res.getPlan(0)
          console.warn('查询驾车方案结果:', plan);
          if (plan) {
            this.driveDistance = plan.getDistance(true)
            this.driveTime = plan.getDuration(true)
          }
        },
        onMarkersSet: routes => {
          // map.removeOverlay(routes[0].marker)
          // map.removeOverlay(routes[1].marker)
          // 解决百度地图起始点图标重叠问题
          const eles = $('.BMap_Marker img')
          if (eles.length > 0) {
            eles.forEach(v => {
              v.style.maxWidth = 'none'
              v.style.width = '94px'
            })
          }
        }
      })
      DRIVE.search(startPos, endPos)
    }
  }
}
</script>
```

### vue-tour

网站导航包

使用

```shell
npm install vue-tour
```

全局导入

```javascript
import Vue from 'vue'
import App from './App.vue'
import VueTour from 'vue-tour'

require('vue-tour/dist/vue-tour.css')

Vue.use(VueTour)

new Vue({
  render: h => h(App)
}).$mount('#app')
```

使用

```vue
<template>
  <div>
    <div id="v-step-0">A DOM element on your page. The first step will pop on this element because its ID is 'v-step-0'.</div>
    <div class="v-step-1">A DOM element on your page. The second step will pop on this element because its ID is 'v-step-1'.</div>
    <div data-v-step="2">A DOM element on your page. The third and final step will pop on this element because its ID is 'v-step-2'.</div>

    <v-tour name="myTour" :steps="steps"></v-tour>
  </div>
</template>

<script>
  export default {
    name: 'my-tour',
    data () {
      return {
        steps: [
          {
            target: '#v-step-0',  // We're using document.querySelector() under the hood
            header: {
              title: 'Get Started',
            },
            content: `Discover <strong>Vue Tour</strong>!`
          },
          {
            target: '.v-step-1',
            content: 'An awesome plugin made with Vue.js!'
          },
          {
            target: '[data-v-step="2"]',
            content: 'Try it, you\'ll love it!<br>You can put HTML in the steps and completely customize the DOM to suit your needs.',
            params: {
              placement: 'top' // Any valid Popper.js placement. See https://popper.js.org/popper-documentation.html#Popper.placements
            }
          }
        ]
      }
    },
    mounted: function () {
      this.$tours['myTour'].start()
    }
  }
</script>
```



### Element-plus



### petite-vue

轻量级的vue

```vue
<script type="module">
  import { createApp } from 'https://unpkg.com/petite-vue?module'

  createApp({
    // exposed to all expressions
    count: 0,
    // getters
    get plusOne() {
      return this.count + 1
    },
    // methods
    increment() {
      this.count++
    }
  }).mount()
</script>

<!-- v-scope value can be omitted -->
<div v-scope>
  <p>{{ count }}</p>
  <p>{{ plusOne }}</p>
  <button @click="increment">increment</button>
</div>
```



### vue-pure-admin

https://github.com/pure-admin/vue-pure-admin
