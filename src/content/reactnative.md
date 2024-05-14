---
title: React Native入门
date: 2020-04-02 21:40:33
categories: IT
tags:
    - IT，RN，
toc: true
thumbnail: https://s1.ax1x.com/2020/03/27/G9OXNR.png
---

## 概述

　　React Native是目前移动端跨平台开发的选择之一，另一种是Flutter，目前安卓平台还是以Android原生开发和java为主，IOS端以Object-c和Swift为主，ReactNative作为一种新的跨平台开发方式，目前可使用的包还不多，但是我认为对于个人开发者还是比较方便的，所以这里把我实现的方式（因为真的很烦）

<!--more-->

## 最重要的事

   一定要翻墙 !一定要翻墙!一定要翻墙！

  因为配置的工具比较多，各软件版本一定要对！各软件版本一定要对！各软件版本一定要对！

重要的事情说三遍！重要的事情说三遍！重要的事情说三遍！（所以这句话为什么要说三遍？因为很重要！因为很重要！因为很重要！（禁止套娃））

不然你就会像我一样，装了删，删了重装，关键你还不知道到底是五个工具哪个有问题（往事不要再提)

## 开始安装

### Node



### Python 2.7

python是免费的

### Java JDK 1.8

https://www.oracle.com/java/technologies/javase-jdk8-downloads.html

### Android Studio 最新版





### 配置环境变量



### Android Studio SDK配置



### ReactNative命令行



```shell
npm install -g react-native-cli
```



测试连接设备

```node
adb devices
```



### 启动

```shell
react-native init  [文件名]
cd [文件名]
npx react-native run-android
```



## 第二种安装方式

（如果你看到这里了，无论你看到上面多么复杂，难受，可以忽略了）

目前官方推荐使用第二种

使用Expo方式安装和使用



```node
npm install -g create-react-native-app
```

创建项目

```node
create-react-native-app appName
cd appName
```

启动项目

```node
npm start
```

启动项目后会生成二维码

手机端下载安装App，打开App扫描二维码,等待加载完成，即可热刷新

注意：

- Expo App只能在Google Play中下载，国内的应用商店没有，可以在下方链接下载安装

- 手机和电脑应处于同一WIFI环境下，不需要额外连线

## 导航器

### react-navigation

react-native官方的导航器

```shell
npm install @react-navigation
```

React Navigation带有三种默认的navigator。

- `StackNavigator` - 为应用程序提供了一种页面切换的方法，每次切换时，新的页面会放置在堆栈的顶部
- `TabNavigator` - 用于设置具有多个Tab页的页面
- `DrawerNavigator` - 用于设置抽屉导航的页面

stackNavigator是最常用的一种navigator

```react
import React from 'react';
import { View, Text } from 'react-native';
import { StackNavigator } from 'react-navigation';

const HomeScreen = () => (
  <View style={{ flex: 1, alignItems: 'center', justifyContent: 'center' }}>
    <Text>Home Screen</Text>
  </View>
);

const DetailsScreen = () => (
  <View style={{ flex: 1, alignItems: 'center', justifyContent: 'center' }}>
    <Text>Details Screen</Text>
  </View>
);

const RootNavigator = StackNavigator({
  Home: {
    screen: HomeScreen,
  },
  Details: {
    screen: DetailsScreen,
  },
});

export default RootNavigator;
```

tab导航栏

```react
import React from 'react';
import { View, Text } from 'react-native';
import { TabNavigator } from 'react-navigation'; // 1.0.0-beta.14
import Ionicons from 'react-native-vector-icons/Ionicons'; // Supported builtin module

const HomeScreen = () => (
  <View style={{ flex: 1, alignItems: 'center', justifyContent: 'center' }}>
    <Text>Home Screen</Text>
  </View>
);

const ProfileScreen = () => (
  <View style={{ flex: 1, alignItems: 'center', justifyContent: 'center' }}>
    <Text>Profile Screen</Text>
  </View>
);

const RootTabs = TabNavigator({
  Home: {
    screen: HomeScreen,
    navigationOptions: {
      tabBarLabel: 'Home',
      tabBarIcon: ({ tintColor, focused }) => (
        <Ionicons
          name={focused ? 'ios-home' : 'ios-home-outline'}
          size={26}
          style={{ color: tintColor }}
        />
      ),
    },
  },
  Profile: {
    screen: ProfileScreen,
    navigationOptions: {
      tabBarLabel: 'Profile',
      tabBarIcon: ({ tintColor, focused }) => (
        <Ionicons
          name={focused ? 'ios-person' : 'ios-person-outline'}
          size={26}
          style={{ color: tintColor }}
        />
      ),
    },
  },
});

export default RootTabs;
```

抽屉导航栏

```react
import React from 'react';
import { View, Text } from 'react-native';
import { DrawerNavigator } from 'react-navigation'; // 1.0.0-beta.14
import Ionicons from 'react-native-vector-icons/Ionicons'; // Supported builtin module

const HomeScreen = () => (
  <View style={{ flex: 1, alignItems: 'center', justifyContent: 'center' }}>
    <Text>Home Screen</Text>
  </View>
);

const ProfileScreen = () => (
  <View style={{ flex: 1, alignItems: 'center', justifyContent: 'center' }}>
    <Text>Profile Screen</Text>
  </View>
);


const RootDrawer = DrawerNavigator({
  Home: {
    screen: HomeScreen,
    navigationOptions: {
      drawerLabel: 'Home',
      drawerIcon: ({ tintColor, focused }) => (
        <Ionicons
          name={focused ? 'ios-home' : 'ios-home-outline'}
          size={26}
          style={{ color: tintColor }}
        />
      ),
    },
  },
  Profile: {
    screen: ProfileScreen,
    navigationOptions: {
      drawerLabel: 'Profile',
      drawerIcon: ({ tintColor, focused }) => (
        <Ionicons
          name={focused ? 'ios-person' : 'ios-person-outline'}
          size={26}
          style={{ color: tintColor }}
        />
      ),
    },
  },
});

export default RootDrawer;
```



### 底部导航栏

导入包

```yarn
yarn add @react-navigation/bottom-tabs
```



```react
<Stack.Screen
        name="Home"
        component={Home}
        options={{
          headerTitle: '首页',
          headerLeft: () => (
            <Button
              onPress={() => alert('This is a button!')}
              title="我"
              color="#f56f"
            />
          ),
          headerRight: () => (
            <Button
              onPress={() => alert('This is a button!')}
              title="I扫一扫"
              color="#35ff"
            />
          ),
        }}
        />
```



### 侧边栏

安装包

```shell
yarn add @react-navigation/drawer
```

导入包

```react
import {
  createDrawerNavigator, //创建侧边栏必备
  DrawerContentScrollView, //创建内容
  DrawerItemList,  //创建项目条
} from '@react-navigation/drawer';
```



## 内容

### 获取网络数据

三种方法：fetch、 axios、async await

这里用axios

安装包



### Scrollview（滚动视图）





### 轮播图

安装包

```node
npm i react-native-swiper –save 
```

导入包

```reactnative
import Swiper from ‘react-native-swiper’;
```

github源地址

https://github.com/leecade/react-native-swiper



```node
<View style={styles.swiper}>
                    <Swiper  showsButtons={true} autoplay={true}>//设置自动轮播、轮播按钮
                        <View style={styles.slide1} >
                        <Text style={styles.text}>Hello Swiper</Text>
                        {/* <Image source={{ uri:'http://q6mb9zdoi.bkt.clouddn.com/linux.jpg' }}></Image> */}
                        </View>
                        <View style={styles.slide2}>
                        <Text style={styles.text}>Beautiful</Text>
                        </View>
                        <View style={styles.slide3}>
                        <Text style={styles.text}>And simple</Text>
                        </View>
                    </Swiper>

            </View>
```



### 九宫格





### 摄像头模块

安装包

```yarn
yarn add react-native-image-picker
```

导入包

```node
import ImagePicker from 'react-native-image-picker';
```

使用

Git hub源地址：https://github.com/react-native-community/react-native-image-picker



### 滚轮



https://github.com/lesliesam/react-native-wheel-picker



### 定时器

安装包

```shell
npm i react-timer-mixin --save
```



### react-native-webrtc

react native的webrtc包

安装

```shell
npm install react-native-webrtc --save
```



### Typescript支持

使用RN的typescript模版生成项目

```shell
npx react-native init MyApp --template react-native-template-typescript
```

github 地址：https://github.com/react-native-community/react-native-template-typescript

修改package.json文件

```json
"dependencies": {
    // ...模板自带的包
    // ...
    "@react-native-community/async-storage": "1.9.0",  // 相当于localStorage（可选）
    "@react-native-community/masked-view": "0.1.9",    // 路由所需要的包 (必须)
    "@react-navigation/stack": "5.2.10",    // 路由所需要的包 (必须)
    "react": "16.11.0",
    "react-dom": "16.11.0",
    "react-native": "0.62.2",
    "react-native-gesture-handler": "1.6.0",      // 路由所需要的包，原生手势系统 (必须)
    "react-native-reanimated": "^1.8.0",    // 路由所需要的包 (必须)
    "react-native-safe-area-context": "0.7.3",    // 路由所需要的包 (必须)
    "react-native-screens": "^2.3.0",    // 路由所需要的包 (必须)
    "react-native-webview": "8.0.0",    // RN打开webView容器，原生调用 (可选)
    "react-redux": "5.0.7",
    "redux": "4.0.0",
    "redux-thunk": "2.3.0"
  },
"devDependencies": {
    // ...模板自带的包
    // ...
    "@babel/plugin-transform-runtime": "^7.9.0",    // babel的插件包（必须）
    "@babel/preset-typescript": "^7.9.0",      // babel的编译ts（必须）
    "@react-navigation/core": "3.4.2",        // rn路由在H5里运行的核心包（必须）
    "@react-navigation/web": "1.0.0-alpha.8",  // rn路由在H5里运行的核心包（必须）
    "babel-loader": "^8.1.0",    // webpack loader（必须）
    "file-loader": "3.0.1",          // webpack loader（必须）
    "html-webpack-plugin": "^4.2.0",          // webpack plugin（必须）
    "react-native-web": "0.12.2",      // rn组件映射为WEB的dom（必须）
    "webpack": "4.42.1",      // 打包rn项目到h5（必须）
    "webpack-cli": "3.3.2",    // 打包rn项目到h5（必须）
    "webpack-dev-server": "3.5.1"    // 调试rn项目到h5（必须）
  },
```

https://juejin.cn/post/6844904130851373063#heading-6

## Hooks

### react-native-community/hooks

useAppState

查看app状态是在后台还是打开中

```react
import { useAppState } from '@react-native-community/hooks'

const currentAppState = useAppState()
```



## 打包Apk

### Expo

```node
npm install -g exp 
exp login   （此处需要在expo官网注册账号，然后登录）

配置app.json
{
   "expo": {
    "name": "Your App Name",
    "icon": "./path/to/your/app-icon.png",
    "version": "1.0.0",
    "slug": "your-app-slug", 
    "sdkVersion": "XX.0.0",
    "ios": {
      "bundleIdentifier": "com.yourcompany.yourappname"
    },
    "android": {
      "package": "com.yourcompany.yourappname"
    }
   }
 }
exp build:android 
```

### Reactnative 原生项目打包

原生打包有两种方式，一是React Native内打包，二是Android Studio打包，这里采用React Native打包，Android Studio打包以后更新（我看了好几篇博客，听说AS打包很慢，而且会常常出错。

1.生成Android签名证书

进入JDK/bin目录，在目录下打开cmd窗口执行命令：

```node
keytool -genkey -v -keystore my-release-key.keystore -alias my-key-alias -keyalg RSA -keysize 2048 -validity 10000
```

执行后设置密钥库密码以及发布信息，记住密码，my-release-key是生成apk的名字。

密钥库里应该已经生成了一个单独的密钥，有效期为`10000`天

2.设置Gradle变量

在`/android/gradle.properties`文件末尾加入代码

```node
MYAPP_RELEASE_STORE_FILE=your keystore filename   
MYAPP_RELEASE_KEY_ALIAS=your keystore alias  
MYAPP_RELEASE_STORE_PASSWORD=*****    
MYAPP_RELEASE_KEY_PASSWORD=*****  
```

等号右边的部分用自己的项目替换

3.修改Gradle配置文件

在`/android/app/build.gradle`下添加以下代码



4.签名打包APK

在项目的`android`目录下启动终端，运行命令

`./gradlew assembleRelease`

自动进行打包

5.将APK发送到手机上执行

打包成功的APK文件默认安装在`/[项目名]/android/app/build/outputs/apk/`下，后缀为.apk,

打包博客链接

https://www.jianshu.com/p/1380d4c8b596



https://blog.csdn.net/carry_qi/article/details/87917387?depth_1-utm_source=distribute.pc_relevant.none-task&utm_source=distribute.pc_relevant.none-task

## React Native性能优化

rn 官方的默认推荐方式是**大家使用 rn 开发并作为独立的 app 使用**,但是对于较大体量的 app 考虑到框架迁移的成本以及对技术保持怀疑的态度 都不会直接使用 rn 开发一个独立的 app;反而更多的是将 rn 作为内部的一些独立模块/页面.

熟悉 rn 的同学应该都清楚,想要 rn 页面在 app 中启动需要内置其一堆底层框架,或者 rn 的 jsbundle; 而内置 rn 的 jsbundle 又分为两种,其优缺点也都较为明显

jsbundle 全量内置

- 优点: 启动速度快
- 缺点: 每次更新 rn 页面都需要跟随 app 发版本,特别是针对页面出现重大 bug 的情况无法做到及时的更新;

jsbundle 部分内置

- 优点: 内置通用的 jsbundle,动态下发业务 bundle;可以动态更新页面
- 缺点: 加载速度相比内置会多一个下载时间,启动速度会有少许的缓慢;

采用**jsbundle 部分内置,资源动态下发**的方式,我们简单介绍一下其整体的思路:

1. rn 框架启动
2. 解析并执行公共 jsbundle, 并行下载业务 jsbundle;
3. 解析并执行业务 jsbundle

我们主要从如下几个方面考虑做性能优化:

1. **优化 bundle 下载时间**: 由于数据的隐私性,暂时只能提供整体优化后的首屏时间: **从 2046ms 到 1176ms**
2. **优化 bundle 解析时间**
3. **优化首屏数据**

rn 的 bundle 由哪些部分组成: **图片资源 + bundle**;

而一个 bundle 又由: **全局变量声明+polyfill+模块定义+require**四部分组成

优化的思路大致有如下几个方向:

1. **减少图片资源,图片地址 cdn 化**: 图片资源并非和 bundle 一起下发而是使用 cdn 的方式加载;
2. **抽离公共包**: 将业务通用的资源/代码进行抽离,比如 react,react-native,redux,以及一些业务通用的包内置到 app; 这样上面介绍的 bundle 就不会存在**var 声明层,polyfill 层,部分 modules**
3. **module tree shaking**: 将无用的代码移除掉
4. **module diff**: 将前后两次代码进行 diff 比较,只动态下发 diff 部分;

抽离公共包

使用 metro 的`processModuleFilter`过滤掉哪些我们不需要打包到业务 bundle 的模块

```javascript
const commonModules = ["react", "react-native", "redux", "自己的业务模块"];
// 一个简单的例子
function processModuleFilter(module) {
  //过滤掉path为base-modules的一些模块（基础包内已有）
  if (module["path"].indexOf("base-modules") >= 0) {
    return false;
  }
  //过滤掉node_modules内的一些通用模块（基础包内已有）
  for (const ele of commonModules) {
    if (module["path"].indexOf("node_modules" + ele) > 0) {
      return false;
    }
  }
  //其他就是应用代码
  return true;
}
```

Module diff

`module diff`的主要思路为必须前后两次构建的资源包，进行 module diff;这样下发 jsbundle 的时候只下发增量部分。这里一般主要和离线包进行配合使用,要考虑的有:

- 如何进行代码 diff?
- 离线包的代码如何和增量部分进行组装?
- 是否采用懒加载的方式加载 diff?
- 等

https://juejin.cn/post/6964607946336501797#heading-4

## Fabric架构

Fabric 是 React Native 新的架构中渲染器，是新架构的主要部分，也是新旧架构中变化最大的地方。

在使用 `react` 开发的时候，对于 `react-dom` ，我们都再熟悉不过。Web 端我们几乎都能看到 `ReactDOM.render` 作为 `react` 框架渲染的入口

`react-dom` 作为一个独立的库，担起了 `react` 和 `Web` 之间的桥梁。在 `react` 的 render 和 commit 阶段， `react-dom` 会创建或更新 DOM 树，并且最终渲染到 `Web` 页面上

`react-dom = react-native-renderer + fabric + native api`

在 `React-Native` 应用中，我们似乎并没有见到类似 `ReactDOM.render` 的入口，经常看到的是

```javascript
AppRegistry.registerComponent(appName, () => App);
```

如果我们在 `react-native` 源码中顺着 `AppRegistry.registerComponent` 看下去，可以找到这样一段代码

```javascript
// Libaries/ReactNative/renderApplication.js 

if (fabric) {
  require('../Renderer/shims/ReactFabric').render(
    renderable,
    rootTag,
    null,
    useConcurrentRoot,
  );
} else {
  require('../Renderer/shims/ReactNative').render(renderable, rootTag);
}
```

看到这里，就看到了熟悉的 `render` 方法。暴露这个 `render` 方法的是 `ReactFabric` 和 `ReactNative` 这两个文件。这两个也正分别是 React Native 新旧两种架构下的渲染器

`react-native-renderer` 和 `react` 配合，在 `react` 的 render 和 commit 阶段共同工作，但是这次不同的是，针对 Native(Android/iOS) 环境，不需要 `react-native-renderer` 去生成 DOM 树，而是转换为 Native 能理解的树形结构，我们称之为 `Shadow Tree` 。因为 `Shadow Tree` 要依赖 `Yoga(C++ 库)` 去计算 `layout`，因此 `Shadow Tree` 要维护在 C++ 侧或原生侧。这就迎来了我们的主角 `Fabric`

Fabric 渲染器承担起了生成 `Shadow Tree` 和调用 `Yoga` 计算 layout 的主要工作。 `react-native-renderer` 在与 `react` 配合时，会有创建和更新 `Shadow Tree` 节点的需要，这时就只需要调用 `Fabric` 暴露给 JS 侧的方法，就可以轻松的同步完成

Fabric 的两大转变：

1. 告别 Bridge 异步通信。得益于 `JSI` 的存在， `react-native-renderer` 作为 JS 代码，能够畅通无阻的调用 `Fabric` 的 C++ 代码。关于 Bridge 通信和 JSI，可以参考之前的文章。
2. 将渲染逻辑从 Native(Android/iOS) 侧统一到 C++ 侧。这带来的好处是类似的逻辑无需在 Android 和 iOS 两侧各维护一份，同时也为将来接入更多的 Native 平台做好了准备。



## UI库

### quark-design 

https://quark-design.hellobike.com/#/zh-CN/component/cell-react



### NativeBase

https://docs.nativebase.io/installation



### tamagui

https://github.com/tamagui/tamagui



## React Native Windows

https://github.com/microsoft/react-native-windows

https://github.com/Microsoft/react-native-macos
