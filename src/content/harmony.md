---
title: 鸿蒙
date: 2020-03-06 21:40:33
categories: 技术博客
tags:
    - IT,Web，Html
toc: true
thumbnail: 
---

鸿蒙入门

<!--more-->

鸿蒙是华为自研的智能终端操作系统，从2019年的鸿蒙1.0正式发布到现在的4.0+，华为自研操作系统也有些年头了。而当下最受开发者关注的，是Harmony OS NEXT。在去年华为宣布Harmony OS NEXT将完全剥离AOSP。也就是说，Android APK 将不能直接在 HarmonyOS NEXT 上运行。

## 开发

ArkTS 和 ArkUI 是华为自己的主要开发方式，兼容当前的os系统，并且更新os next也不会有非常大的改动，可以通过这个方式进行体验。（很像compose和flutter，特别是在语言设计阶段就参考了flutter的很多底层实现）通过原生方式学习和开发能够较好得接入鸿蒙生态（包括设备之间的快速连接、资源共享、快应用等独特功能）。
但是：想要使用harmony os next preview开发需要申请，并获取sdk

**学习方式：**通过华为开发者官网进行学习，每一个小节后面会有题目，完成题目会给你的华为开发者学堂账号颁发一个小证书。
[应用程序框架-华为开发者学堂 (huawei.com)](https://developer.huawei.com/consumer/cn/training/course/slightMooc/C101667310940295021)
华为官网提供了丰富的demo程序供学习和使用
[HarmonyOS (huawei.com)](https://developer.huawei.com/consumer/cn/codelabsPortal/serviceTypes/harmonyos-cn)

**2.基于ArkUI-X的华为自己开发的跨平台方案**
其实此开发方式和鸿蒙原生方法相同，甚至代码可以直接替换，当前已经可以通过ArkUI-X进行Android和Ios的开发和打包，语法和ArkUI一致。提供了Bridge供原生侧和arkui-x的通信。

https://juejin.cn/post/7264996015806545983

使用flutter开发鸿蒙

首先通过flutter来开发Harmony，是基于当前越来越多的平台下，大家能够想到的比较好的解决方案，所以相关资料和讨论也是很多的。但是在这里有一个重要的前提条件:Flutter 鸿蒙化，需要华为提供的真机和SDK。并且google官方并没有主动适配harmony，目前是由三方机构和华为共同维护的生态，已对非常多的flutter_plugin做了适配工作如（shared_prefence），但还是有很多坑需要踩。
开发方式：
[OpenHarmony-SIG/flutter_flutter (gitee.com)](https://gitee.com/openharmony-sig/flutter_flutter)
申请鸿蒙开发套件，申请通过后，在套件列表下载相应系统的工具，并进行环境变量等配置。（目前已经支持windows、macOs）ps：刚推出时只支持linux
三大系统编译方式参考：
[不是鸿蒙 ArkUI 不会写，而是 Flutter 更有性价比 - 掘金 (juejin.cn)](https://juejin.cn/post/7329110277172985908#heading-24)

