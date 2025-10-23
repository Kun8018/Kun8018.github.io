---
title: Javascript开发（十六）
date: 2021-01-18 21:40:33
categories: IT
tags:
    - IT，Web,Node
toc: true
thumbnail: http://cdn.kunkunzhang.top/javascript.png
---

​     第十六篇主要讲常用的js库.主要偏向于JavaScript的库和前端库

<!--more-->

## hedron.js



## format.js

FormatJS 是一个模块化的 JavaScript 国际化库。可用于格式化数值、日期等显示，使用 i18n 工业标准，提供一些常用的模板和组件库。

已经有很多包内置formatjs，比如react-intl

react-intl npm 包文件可以通过设置package.json 文件的main的方式可以为commnjs, ES6, UMD dev, UMD prod这几个版本，但无论是使用那个版本的React Intl, 它们都提供相同的命名导出如下:

- [`injectIntl`](https://link.juejin.cn?target=API.md%23injectintl)
- [`defineMessages`](https://link.juejin.cn?target=API.md%23definemessages)
- [`IntlProvider`](https://link.juejin.cn?target=Components.md%23intlprovider)
- [`FormattedDate`](https://link.juejin.cn?target=Components.md%23formatteddate)
- [`FormattedTime`](https://link.juejin.cn?target=Components.md%23formattedtime)
- [`FormattedRelativeTime`](https://link.juejin.cn?target=Components.md%23formattedrelativetime)
- [`FormattedNumber`](https://link.juejin.cn?target=Components.md%23formattednumber)
- [`FormattedPlural`](https://link.juejin.cn?target=Components.md%23formattedplural)
- [`FormattedMessage`](https://link.juejin.cn?target=Components.md%23formattedmessage)
- [`FormattedHTMLMessage`](https://link.juejin.cn?target=Components.md%23formattedhtmlmessage)

## shepherd.js

引导用户进行网站教程

安装

```html
<link rel="stylesheet" href="shepherd.js/dist/css/shepherd.css"/>
<script src="shepherd.js/dist/js/shepherd.min.js"></script>
```

使用

```javascript
const tour = new Shepherd.Tour({
  defaultStepOptions: {
    cancelIcon: {
      enabled: true
    },
    classes: 'class-1 class-2',
    scrollTo: { behavior: 'smooth', block: 'center' }
  }
});

tour.addStep({
  title: 'Creating a Shepherd Tour',
  text: `Creating a Shepherd tour is easy. too!\
  Just create a \`Tour\` instance, and add as many steps as you want.`,
  attachTo: {
    element: '.hero-example',
    on: 'bottom'
  },
  buttons: [
    {
      action() {
        return this.back();
      },
      classes: 'shepherd-button-secondary',
      text: 'Back'
    },
    {
      action() {
        return this.next();
      },
      text: 'Next'
    }
  ],
  id: 'creating'
});

tour.start();
```

### react-shepherd

安装

```shell
npm install --save react-shepherd
```

使用

```react
import React, { Component, useContext } from 'react'
import { ShepherdTour, ShepherdTourContext } from 'react-shepherd'
import newSteps from './steps'

const tourOptions = {
  defaultStepOptions: {
    cancelIcon: {
      enabled: true
    }
  },
  useModalOverlay: true
};

function Button() {
  const tour = useContext(ShepherdTourContext);

  return (
    <button className="button dark" onClick={tour.start}>
      Start Tour
    </button>
  );
}

class App extends Component {
  render() {
    return (
      <div>
        <ShepherdTour steps={newSteps} tourOptions={tourOptions}>
          <Button />
        </ShepherdTour>
      </div>
    );
  }
}
```

## Driver.js

引导用户

https://driverjs.com/



## peer.js

简化使用WebRTC

https://www.cnblogs.com/yjmyzz/p/peerjs-tutorial.html

这是mozilla开发者官网上的一张图, 大致描述了webrtc的处理过程：

- A通过STUN服务器，收集自己的网络信息
- A创建Offer SDP，通过Signal Channel(信令服务器)给到B
- B做出回应生成Answer SDP，通过Signal Channel给到A
- B通过STUN收集自己的网络信息，通过Signal Channel给到A

注：如果A,B之间无法直接穿透（即：无法建立点对点的P2P直连），将通过TURN服务器中转。

要创建一个真正的webrtc应用还是有些小复杂的，特别是SDP交换（createOffer及createAnswer）、网络候选信息收集(ICE candidate)，这些都需要开发人员对webrtc的机制有足够的了解，对webrtc初学者来讲有一定的开发门槛。

而peerjs开源项目简化了webrtc的开发过程，把SDP交换、ICE candidate这些偏底层的细节都做了封装，开发人员只需要关注应用本身就行了。
peerjs的核心对象Peer，它有几个常用方法：

- peer.connect 创建点对点的连接
- peer.call 向另1个peer端发起音视频实时通信
- peer.on 对各种事件的监控回调
- peer.disconnect 断开连接
- peer.reconnect 重新连接
- peer.destroy 销毁对象

另外还有二个重要对象DataConnection、MediaConnection，其中：

- DataConnection用于收发数据(对应于webrtc中的DataChannel)，它的所有方法中有一个重要的send方法，用于向另一个peer端发送数据；
- MediaConnection用于处理媒体流，它有一个重要的stream属性，表示关联的媒体流。

更多细节可查阅peerjs的api在线文档 (注：peerjs的所有api只有一页，估计15分钟左右就全部看一圈)

peerjs的服务端(即信令服务器)很简单，只需要下面这段nodejs代码即可

```javascript
var fs = require('fs');
var PeerServer = require('peer').PeerServer;
 
var options = {
  //webrtc要求SSL安全传输,所以要设置证书
  key: fs.readFileSync('key/server.key'),
  cert: fs.readFileSync('key/server.crt')
}
 
var server = PeerServer({
  port: 9000,
  ssl: options,
  path:"/"
});
```

文本聊天

主要流程：

- Jack和Rose先连接到PeerJs服务器
- Rose指定要建立p2p连接的对方名称（即：Jack)，然后发送消息
- Jack在自己的页面上，可以实时收到Rose发送过来的文字，并回复

```javascript
var txtSelfId = document.querySelector("input#txtSelfId");
var txtTargetId = document.querySelector("input#txtTargetId");
var txtMsg = document.querySelector("input#txtMsg");
var tdBox = document.querySelector("td#tdBox");
var btnRegister = document.querySelector("button#btnRegister");
var btnSend = document.querySelector("button#btnSend");
 
let peer = null;
let conn = null;
 
//peer连接时，id不允许有中文，所以转换成hashcode数字
hashCode = function (str) {
    var hash = 0;
    if (str.length == 0) return hash;
    for (i = 0; i < str.length; i++) {
        char = str.charCodeAt(i);
        hash = ((hash << 5) - hash) + char;
        hash = hash & hash;
    }
    return hash;
}
 
sendMessage = function (message) {
    conn.send(JSON.stringify(message));
    console.log(message);
    tdBox.innerHTML = tdBox.innerHTML += "<div class='align_left'>" + message.from + " : " + message.body + "</div>";
}
 
window.onload = function () {
 
    //peerserver的连接选项(debug:3表示打开调试，将在浏览器的console输出详细日志)
    let connOption = { host: 'localhost', port: 9000, path: '/', debug: 3 };
 
    //register处理
    btnRegister.onclick = function () {
        if (!peer) {
            if (txtSelfId.value.length == 0) {
                alert("please input your name");
                txtSelfId.focus();
                return;
            }
            //创建peer实例
            peer = new Peer(hashCode(txtSelfId.value), connOption);
 
            //register成功的回调
            peer.on('open', function (id) {
                tdBox.innerHTML = tdBox.innerHTML += "<div class='align_right'>system : register success " + id + "</div>";
            });
 
            peer.on('connection', (conn) => {
                //收到对方消息的回调
                conn.on('data', (data) => {
                    var msg = JSON.parse(data);
                    tdBox.innerHTML = tdBox.innerHTML += "<div class='align_right'>" + msg.from + " : " + msg.body + "</div>";
                    if (txtTargetId.value.length == 0) {
                        txtTargetId.value = msg.from;
                    }
                });
            });
        }
    }
 
    //发送消息处理
    btnSend.onclick = function () {
        //消息体
        var message = { "from": txtSelfId.value, "to": txtTargetId.value, "body": txtMsg.value };
        if (!conn) {
            if (txtTargetId.value.length == 0) {
                alert("please input target name");
                txtTargetId.focus();
                return;
            }
            if (txtMsg.value.length == 0) {
                alert("please input message");
                txtMsg.focus();
                return;
            }
 
            //创建到对方的连接
            conn = peer.connect(hashCode(txtTargetId.value));
            conn.on('open', () => {
                //首次发送消息
                sendMessage(message);
            });
        }
 
        //发送消息
        if (conn.open) {
            sendMessage(message);
        }
    }
}
```

如果两端的浏览器如果不在一个网段，需要配置stun或turn服务器，参考下面的配置

```javascript
var peer = new Peer({
  config: {'iceServers': [
    { url: 'stun:stun.l.google.com:19302' },
    { url: 'turn:homeo@turn.bistri.com:80', credential: 'homeo' }
  ]} /* Sample servers, please use appropriate ones */
});
```

视频通话

在1个页面上输入”张三“并点击register，同时允许使用摄像头，然后在另1个页面输入”李四“，也点击register，并允许使用摄像头，然后把摄像头切换到另1个，这样2个页面看到的本地视频就不一样了（相当于2个端各自的视频流)。然后在"李四"的页面上，target name这里输入"张三"，并点击call按钮发起视频通话，此时"张三"的页面上会马上收到邀请确认

”张三“选择Accept同意后，二端就相互建立连接，开始实时视频通话

注：首次运行时，浏览器会弹出类似下图的提示框询问是否同意启用摄像头/麦克风（出于安全隐私考虑)，如果手一抖选择了不允许，就算刷新页面，也不会再弹出提示框。

```javascript
btnCall.onclick = function () {
    if (txtTargetId.value.length == 0) {
        alert("please input target name");
        txtTargetId.focus();
        return;
    }
    sendMessage(txtSelfId.value, txtTargetId.value, "call");
}

function sendMessage(from, to, action) {
    var message = { "from": from, "to": to, "action": action };
    if (!localConn) {
        localConn = peer.connect(hashCode(to));
        localConn.on('open', () => {
            localConn.send(JSON.stringify(message));
            console.log(message);
        });
    }
    if (localConn.open){
        localConn.send(JSON.stringify(message));
        console.log(message);
    }
}

//register处理
btnRegister.onclick = function () {
    if (!peer) {
        if (txtSelfId.value.length == 0) {
            alert("please input your name");
            txtSelfId.focus();
            return;
        }
        peer = new Peer(hashCode(txtSelfId.value), connOption);
        peer.on('open', function (id) {
            console.log("register success. " + id);
        });
        peer.on('call', function (call) {
            call.answer(localStream);
        });
        peer.on('connection', (conn) => {
            conn.on('data', (data) => {
                var msg = JSON.parse(data);
                console.log(msg);
                //“接收方“收到邀请时，弹出询问对话框
                if (msg.action === "call") {
                    lblFrom.innerText = msg.from;
                    txtTargetId.value = msg.from;
                    $("#dialog-confirm").dialog({
                        resizable: false,
                        height: "auto",
                        width: 400,
                        modal: true,
                        buttons: {
                            "Accept": function () {
                                $(this).dialog("close");
                                sendMessage(msg.to, msg.from, "accept");
                            },
                            Cancel: function () {
                                $(this).dialog("close");
                            }
                        }
                    });
                }
                  
                //“发起方“发起视频call，并绑定媒体流
                if (msg.action === "accept") {
                    console.log("accept call => " + JSON.stringify(msg));
                    var call = peer.call(hashCode(msg.from), localStream);
                    call.on('stream', function (stream) {
                        console.log('received remote stream');
                        remoteVideo.srcObject = stream;
                        sendMessage(msg.to, msg.from, "accept-ok");
                    });
                }
  
                //"接收方"发起视频call，并绑定媒体流  
                if (msg.action === "accept-ok") {
                    console.log("accept-ok call => " + JSON.stringify(msg));
                    var call = peer.call(hashCode(msg.from), localStream);
                    call.on('stream', function (stream) {
                        console.log('received remote stream');
                        remoteVideo.srcObject = stream;                          
                    });
                }
            });
        });
    }
}
```

白板共享

send方法不仅仅可以用来发送文字消息，同样也可以发送其它内容，每次在canvas上的的涂鸦，本质上就是调用canvas的api在一系列的坐标点上连续画线。只要把1个页面上画线经过的坐标点发送到另1个页面上，再还原出来就可以了。

```javascript
window.onload = function () {
    if (!navigator.mediaDevices ||
        !navigator.mediaDevices.getUserMedia) {
        console.log('webrtc is not supported!');
        alert("webrtc is not supported!");
        return;
    }
  
    let connOption = { host: 'localhost', port: 9000, path: '/', debug: 3 };
  
    context = demoCanvas.getContext('2d');
  
    //canvas鼠标按下的处理
    demoCanvas.onmousedown = function (e) {
        e.preventDefault();
        context.strokeStyle='#00f';
        context.beginPath();
        started = true;
        buffer.push({ "x": e.offsetX, "y": e.offsetY });
    }
  
     //canvas鼠标移动的处理
    demoCanvas.onmousemove = function (e) {
        if (started) {
            context.lineTo(e.offsetX, e.offsetY);
            context.stroke();
            buffer.push({ "x": e.offsetX, "y": e.offsetY });
        }
    }
  
     //canvas鼠标抬起的处理
    demoCanvas.onmouseup = function (e) {
        if (started) {
            started = false;
            //鼠标抬起时，发送坐标数据
            sendData(txtSelfId.value, txtTargetId.value, buffer);
            buffer = [];
        }
    }
  
    //register按钮处理
    btnRegister.onclick = function () {
        if (!peer) {
            if (txtSelfId.value.length == 0) {
                alert("please input your name");
                txtSelfId.focus();
                return;
            }
            peer = new Peer(hashCode(txtSelfId.value), connOption);
            peer.on('open', function (id) {
                console.log("register success. " + id);
            });
            peer.on('connection', (conn) => {
                conn.on('data', (data) => {
                    let msg = JSON.parse(data);
                    console.log(msg);
                    txtTargetId.value = msg.from;
                    //还原canvas
                    context.strokeStyle='#f00';
                    context.beginPath();
                    context.moveTo(msg.data[0].x,msg.data[0].y);
                    for (const pos in msg.data) {
                        context.lineTo(msg.data[pos].x,msg.data[pos].y);
                    }
                    context.stroke();
                });
            });
        }
    }
  
    //share按钮处理
    btnShare.onclick = function () {
        if (txtTargetId.value.length == 0) {
            alert("please input target name");
            txtTargetId.focus();
            return;
        }
    }
    start();
}

function sendData(from, to, data) {
    if (from.length == 0 || to.length == 0 || data.length == 0) {
        return;
    }
    let message = { "from": from, "to": to, "data": data };
    if (!localConn) {
        localConn = peer.connect(hashCode(to));
        localConn.on('open', () => {
            localConn.send(JSON.stringify(message));
            console.log(message);
        });
    }
    if (localConn.open) {
        localConn.send(JSON.stringify(message));
        console.log(message);
    }
}
```

发送文件

在2个浏览器页面上，分别register2个用户，然后在其中1个页面上，输入对方的名字，然后选择一张图片，另1个页面将会收到传过来的图片。

核心仍然利用的是DataConnection的send方法，只不过发送的内容里包含了图片对应的blob对象

```javascript
btnRegister.onclick = function () {
    if (!peer) {
        if (txtSelfId.value.length == 0) {
            alert("please input your name");
            txtSelfId.focus();
            return;
        }
        peer = new Peer(hashCode(txtSelfId.value), connOption);
        peer.on('open', function (id) {
            console.log("register success. " + id);
            lblStatus.innerHTML = "scoket open"
        });
  
        peer.on('connection', (conn) => {
            conn.on('data', (data) => {
                console.log("receive remote data");
                lblStatus.innerHTML = "receive data from " + data.from;
                txtTargetId.value = data.from
                if (data.filetype.includes('image')) {
                    lblStatus.innerHTML = data.filename + "(" + data.filetype + ") from:" + data.from
                    const bytes = new Uint8Array(data.file)
                    //用base64编码，还原图片
                    img.src = 'data:image/png;base64,' + encode(bytes)
                }
            });
        });
    }
}
  
//文件变化时，触发sendFile
inputFile.onchange = function (event) {
    if (txtTargetId.value.length == 0) {
        alert("please input target name");
        txtTargetId.focus();
        return;
    }
    const file = event.target.files[0] 
    //构造图片对应的blob对象   
    const blob = new Blob(event.target.files, { type: file.type });
    img.src = window.URL.createObjectURL(file);
    sendFile(txtSelfId.value, txtTargetId.value, blob, file.name, file.type);
}

function sendFile(from, to, blob, fileName, fileType) {
    var message = { "from": from, "to": to, "file": blob, "filename": fileName, "filetype": fileType };
    if (!localConn) {
        localConn = peer.connect(hashCode(to));
        localConn.on('open', () => {
            localConn.send(message);
            console.log('onopen sendfile');
        });
    }
    localConn.send(message);
    console.log('send file');
}
```

## howler.js

web处理音频

```javascript
import {Howl, Howler} from 'howler';

// Setup the new Howl.
const sound = new Howl({
  src: ['sound.webm', 'sound.mp3']
});

// Play the sound.
sound.play();

// Change global volume.
Howler.volume(0.5);
```



## video.js

**HTTP stream**是各家自己定义的http流，应用于国内点播视频网站。

**HLS**是苹果公司实现的基于 HTTP 的流媒体传输协议，全称 HTTP Live Streaming，可支持流媒体的直播和点播，主要应用在 iOS 系统，为 iOS 设备（如 iPhone、iPad）提供音视频直播和点播方案。

**RTMP**是实时消息传输协议，Real Time Messaging Protocol，是 Adobe Systems 公司为 Flash 播放器和服务器之间音频、视频和数据传输开发的开放协议。协议基于 TCP，是一个协议族，包括 RTMP 基本协议及 RTMPT/RTMPS/RTMPE 等多种变种。RTMP 是一种设计用来进行实时数据通信的网络协议，主要用来在 Flash/AIR 平台和支持RTMP协议的流媒体/交互服务器之间进行音视频和数据通信。

HTTP用于点播，本质上还是文件分发，实时性差。

HLS支持**点播和直播** ，HLS的延迟在10秒以上。

RTMP本质上是流协议，主要的优势是：实时性高(实时性一般在3秒之内)、稳定性高。主要用于**直播**应用，对实时性有一定要求。

RTMP协议一般传输的是flv，f4v格式流，RTSP协议一般传输的是ts,mp4格式的流。HTTP没有特定的流。

video.js有两种初始化方式，一种是在video的html标签之中，一种是使用js来进行初始化。两种都需先引入video.js和video-js.css

在html标签中初始化

```html
<link href="//vjs.zencdn.net/7.3.0/video-js.min.css" rel="stylesheet">
<script src="//vjs.zencdn.net/7.3.0/video.min.js"></script>

<video
    id="my-player"
    class="video-js"
    controls
    preload="auto"
    poster="//vjs.zencdn.net/v/oceans.png"
    width="600"
    height="400"
    data-setup='{}'>
  <source src="//vjs.zencdn.net/v/oceans.mp4" type="video/mp4"></source>
  <source src="//vjs.zencdn.net/v/oceans.webm" type="video/webm"></source>
  <source src="//vjs.zencdn.net/v/oceans.ogv" type="video/ogg"></source>
  <p class="vjs-no-js">
    To view this video please enable JavaScript, and consider upgrading to a
    web browser that
    <a href="https://videojs.com/html5-video-support/" target="_blank">
      supports HTML5 video
    </a>
  </p>
</video>
```

在js中初始化

```html
<!-- vjs-big-play-centered可使大的播放按钮居住，vjs-fluid可使视频占满容器 -->
<video id="myVideo" class="video-js vjs-big-play-centered vjs-fluid">
  <p class="vjs-no-js">
    To view this video please enable JavaScript, and consider upgrading to a
    web browser that
    <a href="https://videojs.com/html5-video-support/" target="_blank">
      supports HTML5 video
    </a>
  </p>
</video>

<script>
var player = videojs(document.getElementById('myVideo'), {
  controls: true, // 是否显示控制条
  poster: 'xxx', // 视频封面图地址
  preload: 'auto',
  autoplay: false,
  fluid: true, // 自适应宽高
  language: 'zh-CN', // 设置语言
  muted: false, // 是否静音
  inactivityTimeout: false,
  controlBar: { // 设置控制条组件
    /* 设置控制条里面组件的相关属性及显示与否
    'currentTimeDisplay':true,
    'timeDivider':true,
    'durationDisplay':true,
    'remainingTimeDisplay':false,
    volumePanel: {
      inline: false,
    }
    */
    /* 使用children的形式可以控制每一个控件的位置，以及显示与否 */
    children: [
      {name: 'playToggle'}, // 播放按钮
      {name: 'currentTimeDisplay'}, // 当前已播放时间
      {name: 'progressControl'}, // 播放进度条
      {name: 'durationDisplay'}, // 总时间
      { // 倍数播放
        name: 'playbackRateMenuButton',
        'playbackRates': [0.5, 1, 1.5, 2, 2.5]
      },
      {
        name: 'volumePanel', // 音量控制
        inline: false, // 不使用水平方式
      },
      {name: 'FullscreenToggle'} // 全屏
    ]
  },
  sources:[ // 视频源
      {
          src: '//vjs.zencdn.net/v/oceans.mp4',
          type: 'video/mp4',
          poster: '//vjs.zencdn.net/v/oceans.png'
      }
  ]
}, function (){
  console.log('视频可以播放了',this);
});
</script>
```

controlBar组件说明

- `playToggle`, //播放暂停按钮
- `volumeMenuButton`,//音量控制
- `currentTimeDisplay`,//当前播放时间
- `timeDivider`, // '/' 分隔符
- `durationDisplay`, //总时间
- `progressControl`, //点播流时，播放进度条，seek控制
- `liveDisplay`, //直播流时，显示LIVE
- `remainingTimeDisplay`, //当前播放时间
- `playbackRateMenuButton`, //播放速率，当前只有html5模式下才支持设置播放速率
- `fullscreenToggle` //全屏控制

> `currentTimeDisplay`,`timeDivider`,`durationDisplay`是相对于 `remainingTimeDisplay`的另一套组件，后者只显示当前播放时间，前者还显示总时间。若要显示成前者这种模式，即 '当前时间/总时间'，可以在初始化播放器选项中配置

动态切换视频

```html
<script>
  var data = {
    src: 'xxx.mp4',
    type: 'video/mp4'
  };
  var player = videojs('myVideo', {...});
  player.pause();
  player.src(data);
  player.load(data);
  // 动态切换poster
  player.posterImage.setSrc('xxx.jpg');
  player.play();

  // 销毁videojs
  //player.dispose();
</script>
```

设置语言

```html
<script src="//example.com/path/to/lang/es.js"></script>
<script src="//example.com/path/to/lang/zh-CN.js"></script>
<script src="//example.com/path/to/lang/zh-TW.js"></script>

<script>
var player = videojs('myVideo', {
    language: 'zh-CN' // 初始化时设置语言，立即生效
});

/* 动态切换语言
  使用这种方式进行动态切换不会立即生效，必须有所操作后才会生效。如播放按钮，必须点击一次播放按钮后播放按钮的提示文字才会改变  
 */
//player.language('zh-TW');
</script>
```

vue开发

```vue
import Video from 'video.js'
/* 不能直接引入js，否则会报错：videojs is not defined 
import 'video.js/dist/lang/zh-CN.js' */
import video_zhCN from 'video.js/dist/lang/zh-CN.json'
import video_en from  'video.js/dist/lang/en.json'
import 'video.js/dist/video-js.css'

Video.addLanguage('zh-CN', video_zhCN);
Video.addLanguage('en', video_en);
```



## plyr.js

视频播放组件，基于h5增强

```html
<script src="path/to/plyr.js"></script>
<script>
  const player = new Plyr('#player');
</script>
```



### jszip



```javascript
const zip = new JSZip();

zip.file("Hello.txt", "Hello World\n");

const img = zip.folder("images");
img.file("smile.gif", imgData, {base64: true});

zip.generateAsync({type:"blob"}).then(function(content) {
    // see FileSaver.js
    saveAs(content, "example.zip");
});
```



## gitjs

获取文件夹git信息的包

```shell
npm install simple-git
```

使用

```javascript
// require the library, main export is a function
const simpleGit = require('simple-git');
simpleGit().clean(simpleGit.CleanOptions.FORCE);

// or use named properties
const { simpleGit, CleanOptions } = require('simple-git');
simpleGit().clean(CleanOptions.FORCE);

const git = simpleGit();
git.init(onInit).addRemote('origin', 'git@github.com:steveukx/git-js.git', onRemoteAdd);

function onInit(err, initResult) {}
function onRemoteAdd(err, addRemoteResult) {}
```



## pace.js



## PDF.js



## wiki.js



## axios

Axios 是一个基于 promise 的 HTTP 库，可以用在浏览器和 node.js 中。

其基本的特性是

- 从浏览器中创建 XMLHttpRequests
- 从 node.js 创建 http 请求
- 支持 Promise API
- 拦截请求和响应
- 转换请求数据和响应数据
- 取消请求
- 自动转换 JSON 数据
- 客户端支持防御 XSRF



responseType： 设置表示服务器响应的数据类型，可以是 'arraybuffer', 'blob', 'document', 'json', 'text', 'stream'， 默认是json



请求/响应拦截器

```javascript
// 添加请求拦截器
axios.interceptors.request.use(function (config) {
    // 在发送请求之前做些什么
    return config;
  }, function (error) {
    // 对请求错误做些什么
    return Promise.reject(error);
  });

// 添加响应拦截器
axios.interceptors.response.use(function (response) {
    // 对响应数据做点什么
    return response;
  }, function (error) {
    // 对响应错误做点什么
    return Promise.reject(error);
  });

const myInterceptor = axios.interceptors.request.use(function () {/*...*/});
axios.interceptors.request.eject(myInterceptor);
```



全局配置

```javascript
axios.defaults.baseURL = 'https://api.example.com';
axios.defaults.headers.common['Authorization'] = AUTH_TOKEN;
axios.defaults.headers.post['Content-Type'] = 'application/x-www-form-urlencoded';
```

错误处理

```javascript
axios.get('/user/12345', {
  validateStatus: function (status) {
    return status < 500; // Reject only if the status code is greater than or equal to 500
  }
})
```



取消请求

```javascript
const CancelToken = axios.CancelToken;
const source = CancelToken.source();

axios.get('/user/12345', {
  cancelToken: source.token
}).catch(function(thrown) {
  if (axios.isCancel(thrown)) {
    console.log('Request canceled', thrown.message);
  } else {
     // 处理错误
  }
});

axios.post('/user/12345', {
  name: 'new name'
}, {
  cancelToken: source.token
})

// 取消请求（message 参数是可选的）
source.cancel('Operation canceled by the user.');
```

### 拦截重复请求

当请求的方法、路径、参数完全一样时，就是重复请求，重复请求需要进行拦截，利用axios的请求拦截器和响应拦截器

定义辅助函数

`generateReqKey`：用于根据当前请求的信息，生成请求 Key。**只有 key 相同才会判定为是重复请求**。这一点很重要，而且可能跟具体的业务场景有关，比如有一种请求，输入框模糊搜索，用户高频输入关键字，一次性发出多个请求，可能先发出的请求，最后才响应，导致实际搜索结果与预期不符。这种其实就只需要根据 URL 和请求方法判定其为重复请求，然后取消之前的请求就可以了

这里我认为，如果有需要的话，可以暴露一个 API 给开发者进行自定义重复的规则。这里我们先根据请求方法、url、以及参数生成唯一的 key 去做

```javascript
function generateReqKey(config) {
  const { method, url, params, data } = config;
  return [method, url, Qs.stringify(params), Qs.stringify(data)].join("&");
}
```

addPendingRequest。用于把当前请求信息添加到 pendingRequest 对象中。

```javascript
const pendingRequest = new Map();
function addPendingRequest(config) {
  const requestKey = generateReqKey(config);
  config.cancelToken = config.cancelToken || new axios.CancelToken((cancel) => {
    if (!pendingRequest.has(requestKey)) {
       pendingRequest.set(requestKey, cancel);
    }
  });
}
```

removePendingRequest。检查是否存在重复请求，**若存在则取消已发的请求**。

```javascript
function removePendingRequest(config) {
  const requestKey = generateReqKey(config);
  if (pendingRequest.has(requestKey)) {
     const cancelToken = pendingRequest.get(requestKey);
     cancelToken(requestKey);
     pendingRequest.delete(requestKey);
  }
}
```

添加请求拦截器和响应拦截器

```javascript
axios.interceptors.request.use(
  function (config) {
    removePendingRequest(config); // 检查是否存在重复请求，若存在则取消已发的请求
    addPendingRequest(config); // 把当前请求信息添加到pendingRequest对象中
    return config;
  },
  (error) => {
     return Promise.reject(error);
  }
);

axios.interceptors.response.use(
  (response) => {
     removePendingRequest(response.config); // 从pendingRequest对象中移除请求
     return response;
   },
   (error) => {
      removePendingRequest(error.config || {}); // 从pendingRequest对象中移除请求
      if (axios.isCancel(error)) {
        console.log("已取消的重复请求：" + error.message);
      } else {
        // 添加异常处理
      }
      return Promise.reject(error);
   }
);
```

如果有一些接口就是需要重复发送请求，可以考虑加一下白名单功能，让请求不进行取消

### moxios

基于axios的mock包

安装

```shell
npm install moxios --save-dev
```

使用

```javascript
import axios from 'axios'
import moxios from 'moxios'
import sinon from 'sinon'
import { equal } from 'assert'

describe('mocking axios requests', function () {

  describe('across entire suite', function () {

    beforeEach(function () {
      // import and pass your custom axios instance to this method
      moxios.install()
    })

    afterEach(function () {
      // import and pass your custom axios instance to this method
      moxios.uninstall()
    })

    it('specify response for a specific request', function (done) {
      let input = document.querySelector('.UserList__Filter__Input')
      let button = document.querySelector('.UserList__Filter__Button')

      input.value = 'flintstone'
      button.click()

      // Elsewhere in your code axios.get('/users/search', { params: { q: 'flintstone' } }) is called

      moxios.wait(function () {
        let request = moxios.requests.mostRecent()
        request.respondWith({
          status: 200,
          response: [
            { id: 1, firstName: 'Fred', lastName: 'Flintstone' },
            { id: 2, firstName: 'Wilma', lastName: 'Flintstone' }
          ]
        })
          .then(function () {
            let list = document.querySelector('.UserList__Data')
            equal(list.rows.length, 2)
            equal(list.rows[0].cells[0].innerHTML, 'Fred')
            equal(list.rows[1].cells[0].innerHTML, 'Wilma')
            done()
          })
      })
    })

    it('stub response for any matching request URL', function (done) {
      // Match against an exact URL value
      moxios.stubRequest('/say/hello', {
        status: 200,
        responseText: 'hello'
      })

      // Alternatively URL can be a RegExp
      moxios.stubRequest(/say.*/, {/* … */})

      let onFulfilled = sinon.spy()
      axios.get('/say/hello').then(onFulfilled)

      moxios.wait(function () {
        equal(onFulfilled.getCall(0).args[0].data, 'hello')
        done()
      })
    })

  })

  it('just for a single spec', function (done) {
    moxios.withMock(function () {
      let onFulfilled = sinon.spy()
      axios.get('/users/12345').then(onFulfilled)

      moxios.wait(function () {
        let request = moxios.requests.mostRecent()
        request.respondWith({
          status: 200,
          response: {
            id: 12345, firstName: 'Fred', lastName: 'Flintstone'
          }
        })
          .then(function () {
            equal(onFulfilled.called, true)
            done()
          })
      })
    })
  })

  it('Should reject the request', function (done) {
    const errorResp = {
      status: 400,
      response: { message: 'invalid data' }
    }

    moxios.wait(function () {
      let request = moxios.requests.mostRecent()
      request.reject(errorResp)
    })
      .catch(function (err) {
        equal(err.status, errorResp.status)
        equal(err.response.message, errorResp.response.message)
        done()
      })
  })
})
```



https://blog.csdn.net/yellow757/article/details/107484717

## alova

https://alova.js.org/zh-CN/tutorial/getting-started/overview



## collect.js

处理数组和对象的函数库，类似于lodash，但是比lodash处理数组的函数更多

安装

```shell
npm install collect.js --save
```

使用

```js
import collect from 'collect.js';

collect([1, 2, 3])
  .where('price', '>', 299)
  .sortBy('brand');
```



## 自定义JavaScript插件

插件需要满足的条件

一个可复用的插件需要满足以下条件：

1. 插件自身的作用域与用户当前的作用域相互独立，也就是插件内部的私有变量不能影响使用者的环境变量；
2. 插件需具备默认设置参数；
3. 插件除了具备已实现的基本功能外，需提供部分API，使用者可以通过该API修改插件功能的默认参数，从而实现用户自定义插件效果；
4. 插件需提供监听入口，及针对指定元素进行监听，使得该元素与插件响应达到插件效果；
5. 插件支持链式调用。

插件全局函数

实现私有作用域，最好的办法就是使用闭包。可以把插件当做一个函数，插件内部的变量及函数的私有变量，为了在调用插件后依旧能使用其功能，闭包的作用就是延长函数(插件)内部变量的生命周期，使得插件函数可以重复调用，而不影响用户自身作用域。

故需将插件的所有功能写在一个立即执行函数中

```javascript
(function () {
	//插件所有功能都写在这个函数下
})();
```

插件的默认参数

插件的主要功能可以总结至几个关键参数，通过这几个关键参数即可修改插件的主要功能，也是第三步API设置的关键参数。

将默认参数放置在全局函数的最前面，参数变量名为options,通过对象字面量进行赋值：

```javascript
var options = {
	key1: para1,
	key2: para2,
	key3: para3,
	...
	keyn: paran
}
```

key即为可以插件变量名字，para为该变量对应的值。如我需要编写一个设置颜色的插件，默认颜色为黑色，option应为：

```javascript
var options = {
	color: '#333333'
}
```

编写功能部分时调用方式：`options.color`。

插件API、参数设置和监听

因为API指向的是使用者，故需要在用户调用插件时将API暴露给用户，因用户API时是通过插件提供的名字进行使用，故将API设置为Object类型，用户就可以通过调用API的key进行使用，具体的代码如下

```javascript
var api = {
	config: function (ops) {
		//....
		return this;
	},
	listen: function listen(elem) {
		//...
		return this;
	},
	feature1: function() {
		//...
	},
	feature2: function() {
		//...
	}
}
this.pluginName = api;
```

上面提供了api的写法示范，该api提供了config以设置自定义参数，listen为插件监听的dom操作，feature为插件的主要功能，使用options参数的功能都要写在api下，注意`api.config`和`api.listen`两个函数都应该在最后返回`this`，以便实现插件的链式调用

有了上面的框架，针对`config`设置函数的写法就有了明确的要求：在用户没有传入自定义函数时，默认使用上一节options中的参数，如果用户有设置config参数，使用用户自定义参数

```javascript
config: function (opts) {
	//没有参数传入，直接返回默认参数
	if(!opts) return options;
	//有参数传入，通过key将options的值更新为用户的值
	for(var key in opts) {
		options[key] = opts[key];
	}
	return this;
}
```

针对元素的监听listen，需要对所有符合条件的dom元素进行监听

```javascript
listen: function listen(elem) {
	//这里通过typeof设置监听的元素需为字符串调用，实际可根据需要进行更改
	if (typeof elem === 'string') {
		//这里使用ES5的querySelectorAll方法获取dom元素
		var elems = document.querySelectorAll(elem),
			i = elems.length;
			//通过递归将listen方法应用在所有的dom元素上
			while (i--) {
				listen(elems[i]);
			}
			return
	}
	//在这里，你可以将插件的部分功能函数写在这里

	return this;
}
```

在config和listen这两个最基本的API完成后，需要将API与插件的名字结合起来

```javascript
this.pluginName = api;
```

则最基本的API如下

```javascript
var api = {
	//插件参数设定
	config: function (opts) {
		if(!opts) return options;
		for(var key in opts) {
			options[key] = opts[key];
		}
		return this;
	},
	//插件监听
	listen: function listen(elem) {
		if (typeof elem === 'string') {
			var elems = document.querySelectorAll(elem),
				i = elems.length;
				while (i--) {
					listen(elems[i]);
				}
				return
		}
		//插件功能函数可以写在这
		return this;
	}
}
//将API赋值给插件名字
this.pluginName = api;
```

http://geocld.github.io/2016/03/10/javascript_plugin/

https://www.jianshu.com/p/e65c246beac1
