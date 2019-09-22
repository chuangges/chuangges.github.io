---
title: HTML5 新特性
tags:
  - HTML + CSS
categories: HTML + CSS
top: false
keywords:
  - html
date: 2019-03-07 22:50:40
description: audio、Canvas、WebSocket、Web Worker、拖放、定位、全屏、数据存储
---


# 一、audio、video  
> 支持的格式：Ogg、MP3、WAV

## 基础使用

  ```html
  <!-- 
      src：地址、preload：预加载、autoplay：自动播放
      loop：循环播放、controls：浏览器自带的控制条 
  -->
  <audio src="music/bg.mp3" autoplay>不支持提示语句</audio>

  <audio id="audio" loop controls>
    <source src="music/bg.ogg" type="audio/ogg"></source>
    <source src="music/bg.mp3" type="audio/mpeg"></source>
    优先播放音乐 bg.ogg，不支持播放 bg.mp3
  </audio>

  <!-- width/height：video 独有属性、poster：视频封面 -->
  <video src="video.mp4" width="400" height="300" loop></video>

  <video preload="auto" poster="video.jpg" controls autobuffer>
    <source src="movie.ogg" type="video/ogg"></source>
    <source src="movie.mp4" type="video/mp4"></source>
    您的浏览器不支持 video 标签
  </video>
  ```
       
  ```js
  // var audio = new Audio("test.mp3");
  var audio = document.getElementById("audio");

  // 资源属性
  audio.load()         // 重新加载
  audio.currentSrc     // 当前资源地址
  audio.src = value      // 设置当前资源地址
  audio.canPlayType(type)  // 是否能播放某种格式的资源

  // 播放状态属性
  audio.currentTime = value  // 当前播放的位置
  audio.startTime/duration/loop/autoPlay
  audio.played/paused/ended/
  
  // 状态控制
  audio.play();          // 播放
  audio.pause();         // 暂停
  audio.controls;        // 默认控制条
  audio.volume = value;  // 音量
  audio.muted = value;   // 静音

  // 事件：loadstart、progress
  
  
  // 监听开始播放
  audio.addEventListener('pause', function (e) {
      console.log('开始播放')
  })
  // 监听暂停播放
  audio.addEventListener('pause', function (e) {
      console.log('暂停播放')
  })
  // 监听媒体数据已经加载完成，
  audio.addEventListener('loadedmetadata', function (e) {
      console.log('音频时长：'+ e.target.duration)
  });
  // 监听时间改变，durationchange 资源长度，volumechange 音量
  audio.addEventListener('timeupdate', function (e) {
      let t = e.target
      console.log('剩余时长：'(t.duration - t.currentTime))
  }, false);  
  // 监听播放完成
  audio.addEventListener('ended', function (e) {
      console.log('播放完成')
  }, false);   


  var video = document.getElementById('video');
  // 事件：play、pause、progress、error
  video.addEventListener('play',function(){ });
  ```



## 自动播放
> ios、Android、微信为了节省流量而规定忽视 autoplay 属性

  ```js
  // ios、Android：循环播放失效时方案相似，
  document.addEventListener('touchstart', function () {
    document.getElementById('audio').play();
  })


  // 微信
  document.addEventListener('WeixinJSBridgeReady', function() {
    document.getElementById('audios').play()
  })
  // 针对苹果的手机微信端
  function autoPlayVideo(){
    wx.config({
        debug:false,
        appId:"",
        timestamp:1,
        nonceStr:"",
        signature:"",
        jsApiList:[]
    });
    wx.ready(function(){
        var autoplayVideo = document.getElementById("audio");
        autoplayVideo.play()
    })
  }
  ```


## 用户权限
> navigator.getUserMedia 可以提示用户需要权限去使用像摄像头、麦克风等媒体设备

  ```js
  // 兼容写法
  navigator.getMedia = (navigator.getUserMedia ||
    navigator.webkitGetUserMedia ||
    navigator.mozGetUserMedia ||
    navigator.msGetUserMedia);

  // 获取用户摄像头并提供拍照功能：video、canvas、button
  window.addEventListener("DOMContentLoaded", function() {
    // 获取元素，创建设置等等
    var canvas = document.getElementById("canvas"),
    context = canvas.getContext("2d"),
    video = document.getElementById("video"),
    videoObj = { "video": true },
    errBack = function(error) {
        console.log("Video capture error: ", error.code);
    };

    // 添加 video 监听器
    if(navigator.getUserMedia) { // 标准
        navigator.getUserMedia(videoObj, function(stream) {
            video.src = stream;
            video.play();
        }, errBack);
    } else if(navigator.webkitGetUserMedia) { // WebKit 前缀
        navigator.webkitGetUserMedia(videoObj, function(stream){
            video.src = window.webkitURL.createObjectURL(stream);
            video.play();
        }, errBack);
    }
    else if(navigator.mozGetUserMedia) {    // Firefox 前缀
        navigator.mozGetUserMedia(videoObj, function(stream){
            video.src = window.URL.createObjectURL(stream);
            video.play();
        }, errBack);
    }

    document.getElementById("snap").addEventListener("click", function() {
        context.drawImage(video, 0, 0, 640, 480);
    });

  }, false)
  ```


# 二、Canvas
> 提供了一系列绘图方法的对象

## 绘制图形
  ```js
  let canvas = document.getElementById("canvas"); 
  if(canvas.getContext){
    let ctx = canvas.getContext("2d"); 
  }
  
  // 线段 
  ctx.moveTo(100, 20); 
  ctx.lineTo(200, 40);
  ctx.lineWidth = 5; 
  ctx.strokeStyle = '#CC0000'; 
  ctx.stroke();    // 绘制

  ctx.beginPath();
  ctx.strokeStyle = "#ff0000";
  ctx.lineTo(20, 20);
  ctx.lineTo(20, 70);
  ctx.lineTo(70, 70);
  ctx.closePath();
  ctx.stroke();


  // 路径：填充
  ctx.beginPath();
  ctx.arc(240, 20, 50, 0, Math.PI/2 , false);
  ctx.closePath();
  ctx.fillStyle = '#0000ff';
  ctx.fill();

  // 路径：填充 + 边框
  ctx.beginPath();
  ctx.arc(300, 20, 50, 0, Math.PI/2 , false);
  ctx.strokeStyle = '#0000ff';
  ctx.closePath();
  ctx.stroke();
  ctx.fillStyle = 'rgba(0,0,255,0.2)';
  ctx.fill();


  // 贝塞尔曲线
  ctx.moveTo(300, 300);
  ctx.lineWidth = 10;
  ctx.strokeStyle = "skyblue";
  ctx.bezierCurveTo(300, 50, 500, 50, 150, 150);
  ctx.stroke();
        

  // 蓝色填充矩形
  ctx.fillStyle = "#0000ff";
  ctx.fillRect(20, 100, 60, 60);    

  // 红色边框矩形
  ctx.lineWidth = 10; 
  ctx.strokeRect(100, 100, 60, 60);  
  

  // 圆
  ctx.fillStyle = "#0000ff";
  ctx.strokeStyle = "#ff0000";
  ctx.beginPath();
  ctx.arc(50, 240, 40, 0, 2*Math.PI, true);
  ctx.closePath();
  ctx.stroke();
  ctx.fill();

  ctx.fillStyle = "pink";
  ctx.beginPath();
  ctx.arc(50, 240, 20, 0, 2*Math.PI, true);
  ctx.closePath();
  ctx.fill();


  // 文本
  ctx.font = "Bold 20px Arial"; 
  ctx.textAlign = "left";
  ctx.fillStyle = "#008600"; 
  ctx.fillText("Hello!", 10, 50); 
  ctx.strokeText("Hello!", 10, 100); 


  // 渐变色
  var grd = ctx.createLinearGradient(0, 0, 300, 200);
  grd.addColorStop(0,"#ff0000");
  grd.addColorStop(1,"#0000ff");
  ctx.fillStyle = grd;
  ctx.fillRect(0, 0, 300,200);


  // 阴影
  ctx.shadowOffsetX = 10; 
  ctx.shadowOffsetY = 10; 
  ctx.shadowBlur = 5; 
  ctx.shadowColor = "rgba(0,0,0,0.5)"; 
  ctx.fillStyle = "#CC0000"; 
  ctx.fillRect(10, 10, 200, 100);
  ```


## 绘制图像
  ```js
  // canvas id="canvas" width="400" height="400"
  let canvas = document.getElementById("canvas");
  let ctx = canvas.getContext("2d");
  let image = new Image();
  image.onload = function() {
      
      // 图片绘制
      // ctx.drawImage(image, 0, 0)

      // 图片裁剪
      ctx.beginPath(); 
      ctx.arc(100, 100, 100, 0, Math.PI*2, true); 
      ctx.clip();          
      ctx.drawImage(image, 0, 0);
  }
  image.src = './mobile-type.png';

  // 获取图片保存地址
  let src = canvas.toDataURL('image/png')
  ```

   
## 动画效果
  ```js
  var x = 0;
  var y = 15;
  var speed = 5;
  function animation() {
      window.requestAnimationFrame(animate);
      x += speed;
      if(x <= 0 || x >= 475){
          speed = -speed;
      }
      draw()
  }
  function draw() {
      var canvas = document.getElementById("canvas");
      var context = canvas.getContext("2d");
      context.clearRect(0, 0, 500, 170);
      context.fillStyle = "#ff00ff";
      context.fillRect(x, y, 25, 25);
  }
  animation()
  ```


## 像素处理

  ```js
  // 获取图像数据
  let imgData = ctx.getImageData(0, 0, canvas.width, canvas.height)
      
  // 处理图像数据
  filter(imageData);

  // 重新绘制图像
  ctx.putImageData(imageData, 0, 0)
  ```


# 三、WebSocket
  ```js
  // 创建实例
  var socket = new WebSocket('ws://localhost:8080');

  // 打开 socket
  socket.onopen = function(event) {

      // 发送消息
      socket.send('hello HTML5')

      // 监听消息
      socket.onmessage = function(event) {
          console.log('receive a message', event)
      };

      // 监听关闭
      socket.onclose = function(ev) {
          console.log('socket has closed', event)
      };

      // 关闭 socket
      socket.close()
  }
  ```


# 四、Web Worker

## 机制
  * 在当前 js 的主线程中，通过 Worker 类加载一个 js 文件来开辟一个新的线程
  * 新线程可以加载 JS 进行大量的复杂运算，同时主线程不需要挂起等待而是执行其它代码
  * 新线程创建时，它的脚本文件生成的两个对象和一套 API 可以实现主线程和子线程的数据通信
    * __worker 对象__：主线程脚本中通过构造函数显式创建，通过实例方法获取
    * __WorkerGlobalScope 对象__：子线程脚本中隐式创建的全局对象，通过 this 获取
    * __API 接口__：主线程和子线程脚本间进行数据传输的接口，主要为 postMessage、onmessage
      

## Web 主线程
  1. 创建新线程：`var worker = new Worker(url)`
  2. 发送数据：`worker.postMessage(data)`
  3. 接收数据：`worker.onmessage = fn`
  4. 结束新线程：`worker.terminate()`

  ```js
  // 判断当前浏览器是否支持 web worker
  if (typeof (Worker) != "undefined") {
      if (typeof (w) == "undefined") {       
          w = new Worker("webworker.js"); 
      }  
      // 事件处理函数，用来处理后端的 web worker 传递的消息  
      w.onmessage = function (event) { 
          // do something
      };
  } else { 
      
  }

  // 加载一个 JS 文件来创建一个 worker，同时返回一个 worker 实例
  var worker = new Worker("worker.js")

  // 向 worker 发送数据
  worker.postMessage()

  // 接收 worker 发送的数据
  worker.onmessage = function(event){ }

  // 异常处理
  worker.onerror = function(err){
    // 错误所在的代码文件和行数、错误信息
    alert("Line #" + err.lineno + " - " + err.message + " in " + err.filename);
  }

  // 结束 worker
  worker.terminate()  
  ```
      

## worker 新线程
> 全局变量 this 省略

  1. 发送数据：`postMessage(data)`
  2. 接收数据：`onmessage = fn`
  3. 加载脚本：`importScripts('a.js', 'b.js')`
  4. 结束自身：`self.close()`    


## 应用场景

  * 用来进行处理大量的复杂计算而不挂起主线程
  * 可以在 worker 中通过 `importScripts(url) `加载其它脚本文件
  * 可以使用 `setTimeout()、clearTimeout()、setInterval()、clearInterval()`
  * 可以使用 `XMLHttpRequest` 来发送请求
  * 可以访问 `navigator、location` 对象


## 操作限制
> Worker 线程脚本

  * __脚本限制__：无法调用 alert、confirm 等函数
  * __同源限制__：不能跨域加载脚本文件，它必须与主线程的脚本文件同源
  * __通信限制__：Worker 线程和主线程不在同一个上下文环境，它们不能直接通信
  * __文件限制__：无法读取本地文件，所加载的脚本必须 来自网络或通过服务器打开
  * __DOM 限制__
    * 无法访问 DOM 节点
    * 无法访问 window、document 等全局变量


## 分类
  * __专用线程__
    * 特点：只能被创建它的页面访问，随当前页面的关闭而结束
    * 通信：通过 onmessage()、postmessage()
  * __共享线程__
    * 特点：可以被多个页面访问
    * 通信：connect 后通过 port 属性

  ```js
  // ---------- 专用线程 -----------
  // 主线程 index.html/script
  var worker = new Worker("worker.js"); 
  worker.postMessage("hello world");      // 发送数据
  worker.onmessage = function (event) {   // 接收数据
      console.log(event.data);             
      worker.terminate();
  }
  
  // 子线程：worker.js 
  onmessage = function (event){          // 接收数据
      var d = event.data;    
      postMessage("已收到：" + d);        // 发送数据
  }

  // 通过服务器打开文件：直接打开会报错
  live-server


  // ---------- 共享线程 -----------
  // 主线程 
  worker.port.onmessage = function(e){}
  worker.port.postMessage('data');
  
  // 子线程 
  addEventListener('connect', function(event){
      var port = event.ports[0]
      // 接收
      port.onmessage = function(event){
          console.log(event.data);
      };
      // 发送
      port.postMessage("data");
      port.start();
  })
  ```


# 五、拖放操作

## 事件
> 事件对象为 被拖拽的元素、放置的目标元素 (拖放范围)

  * 拖拽元素
    * __dragstart__：拖拽开始时触发 
    * __drag__：拖拽过程中连续触发
    * __dragend__：拖拽结束时触发
  * 目标元素
    * __dragenter__：拖入元素时触发
    * __dragover__：拖拽元素时连续触发
    * __dragleave__：元素拖出时触发
    * __drop__：拖入后释放鼠标时触发
  * 执行顺序
    * drop 触发：`dragstart、drag、dragenter、dragover、drop、dragend`
    * drop 不触发：`dragstart、drag、dragenter、dragover、dragleave、dragend`


## dataTransfer 对象
> 拖动时回调函数接受的事件参数。注意火狐浏览器下必须设置它的 setData 方法才可以拖拽除图片外的其他标签

  * 属性
    * __dropEffect__：元素行为和相应光标
    * __effectAllowed__：允许拖动元素的光标样式
    * __files__：被拖放文件的 FileList 对象
  * 方法
    * __getData__：读取对象中的数据
    * __setData__：在对象上储存数据
    * __clearData__：清除对象中的数据
    * __setDragImage__：指定拖动时显示的图像
    * __addElement__：添加元素和被拖拽元素一同被拖拽

  ```js
  img,ondragstart = event => {
      event.dataTransfer.setData("text/plain", event.target.id)
  }
  div.drop = event => {
      event.preventDefault();
      var imgId = event.dataTransfer.getData("text/plain")
  }
  ```


## 拖拽上传预览图片
  ```js
  window.onload = function(){
      var list = document.getElementById('list');
      var box = document.getElementById('box');
      box.ondragenter = function(){
          this.innerHTML = '可以释放';
      };
      box.ondragover = function(e){
          e.preventDefault();
      };
      box.ondragleave = function(){
          this.innerHTML = '请拖拽到此区域';
      };
      box.ondrop = function(event){
          // 连续触发事件阻止冒泡
          event.preventDefault(); 

          // 获取数据对象
          var fs = event.dataTransfer.files;
          for(var i=0; i > fs.length; i++){

              // 读取文件信息的接口对象
              var fr = new FileReader();  
              if( fs[i].type.indexOf('image')!=-1 ){
                  fr.readAsDataURL( fs[i] );
                  fr.onload = function(){
                      var oLi = document.createElement('li');
                      var oImg = document.createElement('img');
                      oImg.src = this.result;
                      oLi.appendChild( oImg );
                      list.appendChild( oLi );
                  }
              }else{
                  alert('请拖放图片指定格式');
              }
          }
      }
  }
  ```


# 六、地理定位 
> 常用的地理位置定位方式：HTML5、百度地图、高德地图
        
  ```js
  // HTML5 定位
  function getLocation() {
      var options = {
            enableHighAccuracy: true,  // 是否要求高精度
            maximumAge: 1000           // 应用缓存时间
      }
      if (navigator.geolocation) {
          navigator.geolocation.getCurrentPosition(position =>  {
              var latitude = position.coords.latitude;    
              var longitude = position.coords.longitude;  
              console.log(`纬度：${latitude}, 经度：${longitude}`)

          }, error => {
              console.log(error)
              // 常见错误：需要用户授权、仅限 HTTPS、需要翻墙 (谷歌浏览器)
          }, options);

      } else {
          // 浏览器不支持
      }
  }

  // 百度地图：申请密钥之后以参数形式引入，然后即可调用
  // script src="http://api.map.baidu.com/api?v=2.0&ak=wmwHFMPxi66GlPBVUrdgEhDzbLUqlSrM"

  var map = new BMap.Map("allmap"); // 创建Map实例
  var point = new BMap.Point(116.404, 39.915)
  map.centerAndZoom(point, 15); // 初始化地图，设置中心点坐标和地图级别
  // map.centerAndZoom("上海",15);  

  map.addControl(new BMap.MapTypeControl()); // 添加地图类型控件
  map.setCurrentCity("北京");      // 设置地图显示的城市
  map.enableScrollWheelZoom(true); // 开启鼠标滚轮缩放
  ```


# 七、全屏模式
  ```js
  // 启动全屏模式  
  launchFullScreen(document.documentElement);   // 整个页面  
  launchFullScreen(document.getElementById("video")); // 单独元素 

  // 取消全屏  
  cancelFullscreen(); 

  function launchFullScreen(element) {  
      if(element.requestFullScreen) {  
          element.requestFullScreen();  
      } else if(element.mozRequestFullScreen) {  
          element.mozRequestFullScreen();  
      } else if(element.webkitRequestFullScreen) {  
          element.webkitRequestFullScreen();  
      }  
  }  
  function cancelFullscreen() {  
      if(document.cancelFullScreen) {  
          document.cancelFullScreen();  
      } else if(document.mozCancelFullScreen) {  
          document.mozCancelFullScreen();  
      } else if(document.webkitCancelFullScreen) {  
          document.webkitCancelFullScreen();  
      }  
  }  

 
  // 全屏元素
  var fullscreenEle = document.fullscreenElement ||
                      document.mozFullScreenElement ||
                      document.webkitFullscreenElement;

  // 是否全屏
  var isFullscreen = document.fullscreenEnabled ||
                      window.fullScreen ||
                      document.webkitIsFullScreen ||
                      document.msFullscreenEnabled;

  // 全屏事件
  document.addEventListener('fullscreenchange', function(){ })
  document.addEventListener('fullscreenerror', function(){ })
  ```
  

# 八、数据储存
> 客户端存储方式：localStorage、sessionStorage、cookie、UserData、webSQL、IndexedDB、HTML5 离线存储等。


## cookie
> 随着每次 http 请求头信息一起发送，无形中增加了网络流量，而且能存储的数据容量较小，适用于购物车、客户端登录等场景

  * 优点
    * 可控制过期时间 
    * 可扩展、可用性比较好
    * 可加密减少 cookie 被破解的可能性
  * 缺点
    * 在请求头上携带数据安全性差
    * 数量和长度有限制，最多 20 条，最长不能超过 40k
  * API
    * 存储：`document.cookie = "键=值"`
    * 读取：`var val = document.cookie`         
    * 删除：
      * `var date = new Date()`
      * `document.cookie = "key=value;expires=" + date.toGMTString()`  


  ```js
  export const setCookie = (name, value, expiredays) => {
      var exdate = new Date();　　　　
      exdate.setDate(exdate.getDate() + expiredays);　　　　
      document.cookie = name + "=" + escape(value) + ((expiredays == null) ? "" : ";expires=" + exdate.toGMTString());
  }

  export const getCookie = name => {
      var arr, reg = new RegExp("(^| )" + name + "=([^;]*)(;|$)");
      if (arr = document.cookie.match(reg))
          return (arr[2]);
      else
          return null;
  }

  export const delCookie = name => {
      var exp = new Date();
      exp.setTime(exp.getTime() - 1);
      var cval = this.getCookie(name);
      if (cval != null)
      document.cookie = name + "=" + cval + ";expires=" + exp.toGMTString();
  }
  ```


## localStorage
> 本地存储方式可以长期存储数据，没有时间限制，而且可以储存电话本等大量数据

  * 特点：同源策略限制、只在本地存储、永久保存、同浏览器共享
  * 优点
    * 扩展了 cookie 的 4k 限制
    * 可以将请求数据直接存储到本地，节约带宽
    * 遵循同源策略，不同网站之间不能直接共用
  * 缺点
    * 需要手动删除，否则长期存在
    * 浏览器大小不一，版本的支持也不一样
    * 只支持存储 string 类型的数据，JSON 对象需要转换
    * 本质是对字符串的读取，如果存储内容多则会消耗内存空间而导致页面变卡
  * 应用场景
    * 多页面访问共同数据：可以在多个标签页中共享数据
    * 数据比较大的临时保存方案：比如在线编辑文章时的自动保存
  * API
    * 存储：`localStorage.setItem(key, value)`
    * 读取
      * 单个：`localStorage.getItem(key)`
      * 全部：`localStorage.valueOf()`
    * 删除
      * 单个：`localStorage.removeItem(key)`
      * 全部：`localStorage.clear()`


  ```js
  export const setStore = (name, content) => {
    if (!name) return;
    if (typeof content !== 'string') {
      content = JSON.stringify(content);
    }
    window.localStorage.setItem(name, content);
  }
  export const getStore = name => {
    if (!name) return;
    return window.localStorage.getItem(name);
  }
  export const removeStore = name => {
    if (!name) return;
    window.localStorage.removeItem(name);
  }
  ```


## sessionStorage
> 会话存储，关闭浏览器之后数据就会消失。非常适合单页应用程序，便于各业务模块之间的传值

  * 特点
    * __同源策略限制__ 
    * __单标签页限制__：同一个标签页中的同源页面共享数据
    * __只在本地存储__：数据只会在存储在本地，并在标签页关闭后清除
    * __存储方式__：采用键值对的方式，注意 value 值必须为字符串类型
    * __存储上限限制__：不同的浏览器存储的上限不同，但大多数限制在 5MB 以下
  * API
    * 存储：`sessionStorage.setItem(key, value)`
    * 读取
      * 单个：`sessionStorage.getItem(key)`
      * 全部：`sessionStorage.valueOf()`
    * 删除
      * 单个：`sessionStorage.removeItem(key)`
      * 全部：`sessionStorage.clear()`

 
## 以上区别
  * 传递
    * cookie 数据通常经过加密，而且会在浏览器和服务器间来回传递
    * 后两个不会自动把数据发给服务器，仅在本地保存。
  * 存储大小
    * cookie 数据大小不能超过4k，始终在同源的 http 请求中携带
    * 后两个虽然也有存储大小的限制但比 cookie 大得多，可以达到 5M 及以上
  * 生命周期
    * cookie 数据只在过期时间之前一直有效
    * localStorage存储持久数据，不要不主动删除数据就有效有效
    * sessionStorage数据在当前浏览器窗口关闭后自动删除
  * 作用域    
    * sessionStorage 不在不同浏览器窗口中共享
    * 另外两个在所有同源窗口中都共享


## 临时数据
> html 标签上添加自定义属性来存储和操作数据，注意 js 可以动态添加和删除，但不能删除行内添加的

  * html：`div data-name="值"`，name 为自定义属性名
  * 注意：`data-e-name：eName, data-myName：myname`
  * 原生 js
    * 获取：`div.dataset.name`
    * 设置：`div.dataset.name = new`
    * 删除：`div.dataset.name = null`
  * jQuery
    * 获取：`$("div").data("name")`
    * 设置：`$("div").data("name", "new")`
    * 删除：`$("div").removeDate("name")`


## 离线存储
> Application Cache 应用程序缓存，常用于存储网页

  * 优势
    * 加载快：已缓存资源加载速度块
    * 离线浏览：用户可以在应用离线时使用
    * 减少服务器负载：浏览器只从服务器下载更新过的资源
  * 使用
    * html 标签添加 manifest 属性：html manifest="./js/demo.manifest"
    * 编写 manifest 文件：用于告知浏览器需要缓存和不需要缓存的内容



# 九、其它

  * 普通元素
    * 新增标签：`nav、header、aside、section、footer、canvas、video、output、...`
    * 新增属性：`contextmenu、contentEditable、hidden、draggable、data-*`
  * 表单元素
    * 输入验证：`邮箱、地址、日期、数字、电话、范围、搜索、颜色`
    * 新增属性：`required、pattern、autofocus、autocomplete、novalidate、multiple`
  * 超链接
    * 短信：`<a href='sms:15919218899'></a>`
    * 电话：`<a href="tel:15919218899"></a>`
    * 邮件：`<a href="mailto:99519876@qq.com"></a>`
    * 地图：`<a href="http://map.baidu.com/mobile/search..."></a>`
    * QQ客服：`<a target="_blank" href="http://wpa.qq.com/msgrd?v=3&..."></a>`
  * History
    * 功能：实现无刷新更新地址
    * API：`history.pushState、history.replaceState`
   


