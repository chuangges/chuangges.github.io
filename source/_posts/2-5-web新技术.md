---
title: Web 新技术的应用
tags:
  - 项目开发
categories: 项目开发
top: false
keywords:
  - 技术
date: 2019-02-28 21:02:57
description: OCR 识别、Video 视频
---

# 一、OCR 识别

## 基础理解

  <div style="text-indent: 2em; margin-bottom: 20px;">OCR 是实时高效的定位与识别图片中的所有文字信息并返回文字框位置与文字内容，就是将图片上的文字内容智能识别为可编辑的文本。目前支持的场景有：`身份证识别、银行卡识别、名片识别、营业执照识别、行驶证驾驶证识别、车牌号识别、通用印刷体识别、手写体识别`。</div>

  <div style="text-indent: 2em">OCR 的本质是图像识别，其原理和其他的图像识别问题基本相同。主要包含两大关键技术：`文本检测和文字识别`，首先将图像中的特征的提取并检测目标区域，然后对目标区域的字符进行分割和分类。相关阅读：[OCR 知识资料全集](https://cloud.tencent.com/developer/article/1092091?fromSource=waitui)、[OCR 文字识别快速体验版](https://cloud.tencent.com/developer/article/1190739?fromSource=waitui)</div>


## 开发流程

  * 技术文档：[百度 OCR](https://cloud.baidu.com/doc/OCR/index.html)
  * 引用流程
    * 创建应用
      * 选项：控制台左侧、文字识别
      * 获取：Secret Key、API Key
    * 接入 API
      * API：应用程序编程接口，是一些基于服务端层并可以直接的预定义函数，不需要关注实现的底层语言
      * access_token：可以通过 Postman POST 请求获取，需要传入应用的 SK、AK 作为参数
      * 调用：根据技术文档的地址和参数，通过 Postman 调试接口
    * 导入 SDK
      * SDK：软件开发工具包，是一个完全封装好并提供指定语言调用接口的二进制包，包括辅助开发某类软件的相关文档、范例和工具等


## 人脸识别

### 实现原理

  1. 通过 HOG 找出图片中所有人脸的位置
  2. 计算出人脸的 68 个特征点并适当的调整人脸位置，对齐人脸
  3. 将得到的面部图像放入神经网络，得到 128 个特征测量值，并保存它们
  4. 和以前保存过的测量值一并计算欧氏距离并得到欧氏距离值，通过对比数值大小确定是否同一个人


### 应用场景
> 人脸识别分为两大步骤：人脸检测和人脸识别，它们的应用场景也各不相同

  * 人脸检测：美颜、换肤、抠图、换脸。目的是找出人脸的位置
  * 人脸识别：会员、支付。目的是识别用户


## 前端代码
> 前端发送人脸照片、后台对比人脸库

  ```js
  /* 
    div class="videos" ref="videos"
      video id="video" width="400" height="300" muted autoplay
      // 隐藏掉，为了发送照片
      canvas id="canvas" width="400" height="300" v-show="false"
  */

  export default {
    name: "Videos",
    data() {
      return {};
    },
    methods: {
      camera_options() {
        // 打开网络摄像头
        let that = this;
        var constraints = {
          video: true, // 使用摄像头对象
          audio: false // 不适用音频
        };
        var video = document.getElementById("video");
        var promise = navigator.mediaDevices.getUserMedia(constraints);
        promise
          .then(MediaStream => {
            /**
             * @desc MediaStream 返回参数
             * active：true
             * id："ykCZTVor0KNVypRFZW8dFSwrFd9QuihhWmqA"
             * onactive：null
             * onaddtrack：null
             * oninactive：null
             * onremovetrack：null
             */
            if ("srcObject" in video) {
              video.srcObject = MediaStream;
            } else {
              // 旧版浏览器可能没有 srcObject
              video.src = URL.createObjectURL(MediaStream);
            }
            video.play();
          })
          .catch(error => {
            console.info(error);
          });
        /**
         *  @desc 倒计时以后进行拍照的操作
         */
        setTimeout(function() {
          let canvas = document.getElementById("canvas");
          canvas.getContext("2d").drawImage(video, 0, 0, 200, 100);
          canvas = canvas.toDataURL("image/png");
          if (!canvas) return;

          //上传人脸图片：base64 文件
          that.$axios({});

          //上传人脸图片：file 文件
          let blob = that.dataURLtoBlob(canvas);
          let file = that.blobToFile(blob, "imgName");
          if (blob) that.$axios({});
        }, 1000);
      },
      /**
       * 将图片转为file格式
       * @param {Object} dataurl 将拿到的base64的数据当做参数传递
       */
      dataURLtoBlob: function(dataurl) {
        let arr = dataurl.split(","),
          mime = arr[0].match(/:(.*?);/)[1],
          bstr = atob(arr[1]),
          n = bstr.length,
          u8arr = new Uint8Array(n);
        while (n--) {
          u8arr[n] = bstr.charCodeAt(n);
        }
        return new Blob([u8arr], {
          type: mime
        });
      },
      /**
       * @param {Object} theBlob  文件
       * @param {Object} fileName 文件名字
       */
      blobToFile: function(theBlob, fileName) {
        theBlob.lastModifiedDate = new Date();
        theBlob.name = fileName;
        return theBlob;
      }
    },
    created() {
      this.camera_options();
    }
  };
  ```


# 二、Video 视频

## 标签属性
  ```
  src：视频的URL
  poster：视频封面，没有播放时显示的图片
  preload：预加载
  autoplay：自动播放
  loop：循环播放
  controls：浏览器自带的控制条
  width：视频宽度
  height：视频高度
  webkit-playsinline="true" IOS下防止全屏播放
  playsinline="true" 同上
  x-webkit-airplay="true" 支持ios的AirPlay功能
  x5-video-player-type="h5" 启用同层H5播放器
  x5-video-player-fullscreen="true" 全屏设置
  x5-video-orientation="portraint" 竖屏
  style="object-fit:fill" 封面铺满
  muted="true" 静音播放
  ```


## 标签样式
> chrome 调试方式：F12、右上方三个点、setting、Perferences、勾选 Show user agent shadow Dom，然后就可以查看 video 标签的控制栏 dom 结构。

  ```scss
  // 全屏按钮
  video::-webkit-media-controls-fullscreen-button { }

  // 播放按钮
  video::-webkit-media-controls-play-button { }

  // 进度条
  video::-webkit-media-controls-timeline { }

  // 观看的当前时间
  video::-webkit-media-controls-current-time-display { }

  // 剩余时间
  video::-webkit-media-controls-time-remaining-display { }

  // 音量按钮
  video::-webkit-media-controls-mute-button { }
  video::-webkit-media-controls-toggle-closed-captions-button { }

  // 音量的控制条
  video::-webkit-media-controls-volume-slider { }

  //所有控件
  video::-webkit-media-controls-enclosure { }
  ```


## 对象属性

### 错误状态
  ```js
  $video.error;      // null: 正常  
  $video.error.code; // 1.用户终止 2.网络错误 3.解码错误 4.URL无效 
  ```

### 网络状态
  ```js
  $video.currentSrc;         // 当前资源的 URL  
  $video.src = value;        // 设置当前资源的 URL  
  $video.canPlayType(type);  // 是否能播放某种格式的资源  
  $video.networkState;       // 视频的当前网络状态
  $video.buffered;           // 获取已缓冲区域
  $video.buffered.end(0)     // 获取最后一刻的数据
  $video.load();             // 重新加载 src 指定的资源 
  $video.preload;            // 是否预加载视频
  ```

### 准备状态
  ```js
  $video.readyState;    // 视频是否已准备好播放
  $video.seeking;       // 是否正在寻址 
  ```

### 播放状态
  ```js
  $video.currentTime = value; // 当前播放位置  
  $video.duration;            // 当前资源长度  
  $video.paused;              // 是否暂停  
  $video.defaultPlaybackRate = value;  // 默认的回放速度
  $video.playbackRate = value;         // 当前播放速度 
  $video.seekable;    // 返回可以寻址的区域 
  $video.ended;       // 是否结束  
  $video.autoPlay;    // 是否自动播放  
  $video.loop;        // 是否循环播放  
  $video.play();      // 播放  
  $video.pause();     //暂停  
  ```

### 相关控制
  ```js
  $video.controls;         // 是否有默认控制条  
  $video.volume = value;   // 音量  
  $video.muted = value;    // 静音
  ```


## 对象方法
  ```js
  loadstart         // 客户端开始请求数据  
  *progress         // 客户端正在请求数据  
  suspend           // 延迟下载  
  abort             // 客户端主动终止下载（不是因为错误引起） 
  *error            // 请求数据时遇到错误  
  stalled           // 网速失速  
  *play             // 开始播放时触发  
  *pause            // 暂停时触发  
  loadedmetadata    // 成功获取资源长度  
  *waiting          // 等待数据，并非错误  
  *playing          // 开始回放  
  canplay           // 可以播放，但中途可能因为加载而暂停  
  *canplaythrough   // 可以播放  
  seeking           // 资源寻找中  
  seeked            // 资源寻找完毕  
  *timeupdate       // 播放时间改变  
  *ended            // 播放结束  
  ratechange        // 播放速率改变  
  durationchange    // 资源长度改变  
  *volumechange     // 音量改变         
  ```


## 自定义视频组件

### 组件封装
> video 控件的样式和功能封装

  ```html
  <template>
    <div class="video-controls">
      <div ref="playControl" :class="{'play-control':true, playing:paused, paused: !paused}"></div>
      <div ref="current" class="current-time">{{currentTime}}</div>
      <div ref="progress" class="progress" @click="handleJumpProgress">
        <i ref="slider" :style="{width: progress*100 + '%'}"></i>
      </div>
      <div class="duration-time">{{totalTime}}</div>
    </div>
  </template>

  <script>
  import Draggable from "./draggable";
  export default {
    name: "video-controls",
    props: ["video"],
    data() {
      return {
        paused: true,
        currentTime: "00:00",
        totalTime: "00:00",
        progress: 0
      };
    },
    methods: {
      handleJumpProgress,
      handleDragProgress,
      handleDurationChange,
      handleTimeUpdate,
      handleClickPlayControl
    },
    mounted() {
      this.draggable = Draggable(this.$refs.progress, this.handleDragProgress);
      this.video.addEventListener("durationchange", this.handleDurationChange);
      this.video.addEventListener("timeupdate", this.handleTimeUpdate);
      this.video.addEventListener("play", e => (this.paused = false));
      this.video.addEventListener("pause", e => (this.paused = true));
      this.$refs.playControl.addEventListener(
        "click",
        this.handleClickPlayControl
      );
    },
    destroyed() {
      this.draggable.destroy();
    }
  };

  function handleClickPlayControl(e) {
    if (this.paused) {
      this.video.play();
    } else {
      this.video.pause();
    }
  }

  function handleTimeUpdate(e) {
    this.currentTime = formatTime(e.target.currentTime);
    this.progress = e.target.currentTime / e.target.duration;
    this.paused = false;
  }

  function handleDurationChange(e) {
    this.totalTime = formatTime(e.target.duration);
  }

  function handleDragProgress(delt) {
    const { progress, slider } = this.$refs;
    let w = slider.offsetWidth + delt;
    if (w > progress.offsetWidth) {
      w = progress.offsetWidth;
    } else if (w < 0) {
      w = 0;
    }
    this.progress = w / progress.offsetWidth;
    this.video.currentTime = this.progress * this.video.duration;
  }

  function handleJumpProgress(e) {
    const target = e.currentTarget;
    const rect = target.getBoundingClientRect();
    const clickX = e.clientX - rect.left;
    this.progress = clickX / rect.width;
    this.video.currentTime = this.progress * this.video.duration;
  }

  function formatTime(seconds) {
    const m = Math.floor(seconds / 60);
    const s = Math.ceil(seconds % 60);
    return formatNumber(m) + ":" + formatNumber(s);
  }

  function formatNumber(num) {
    return num < 10 ? "0" + num : num;
  }
  </script>

  <style lang="scss">
  .video-controls {
    position: absolute;
    display: flex;
    height: 40px;
    left: 20px;
    right: 20px;
    bottom: 20px;
    background: rgba(0, 0, 0, 0.5);
    border-radius: 2px;
    color: #fff;
    align-items: center;
    text-align: center;
    font-size: 14px;
    line-height: 18px;

    .play-control {
      width: 40px;
      padding-top: 1px;
    }

    .playing::after {
      content: "";
      display: inline-block;
      border-top: 8px solid transparent;
      border-left: 12px solid #fff;
      border-bottom: 8px solid transparent;
      vertical-align: top;
    }

    .paused::after {
      content: "";
      display: inline-block;
      width: 8px;
      height: 15px;
      border-left: 2px solid #fff;
      border-right: 2px solid #fff;
    }

    .progress {
      position: relative;
      flex: 1;
      height: 100%;

      &::before,
      i {
        content: "";
        position: absolute;
        width: 100%;
        height: 2px;
        left: 0;
        top: 50%;
        margin-top: -1px;
        background: #818a95;
        border-radius: 2px;
      }

      i {
        background-color: #fff;
        width: 0;
        z-index: 2;
      }

      i::after {
        content: "";
        position: absolute;
        top: -4px;
        right: -10px;
        width: 10px;
        height: 10px;
        border-radius: 100%;
        background-color: #fff;
      }
    }

    .current-time {
      width: 50px;
      text-align: left;
    }

    .duration-time {
      width: 60px;
    }
  }
  </style>
  ```


### 组件使用
  ```html
  <template>
    <div class="video-player">
      <video
        ref="video"
        autoplay
        preload
        disablePictureInPicture
        controlslist="nodownload nofullscreen"
        playsinline
        webkit-playsinline
        x5-video-player-type="h5"
        @play="isPaused = false"
        @pause="isPaused = true"
        @contextmenu="handleContext"
        @fullscreenchange="handleFullScreenChange"
      ></video>
      <img :class="{'shown': isPaused}" width="60" src="images/icon-play.png" @click="playVideo" />
      <video-control v-if="video" :video="video" />
    </div>
  </template>
  <script>
  import onTap from "@/assets/js/ontap";
  import VideoControl from "@/components/Controls.vue";
  export default {
    name: "video-player",
    components: { VideoControl },
    data() {
      return {
        video: null,
        isPaused: true,
        curIndex: 0,
        videoList: []
      };
    },
    methods: {
      playVideo(e) {
        let video = this.$refs.video;
        if (video.paused) {
          video.play();
        } else {
          video.pause();
        }
      },
      handleContext(e) {
        e.preventDefault();
      },
      onTap,
      handleFullScreenChange,
    },
    created(){
      this.getVideoUrl(0)
    },
    mounted() {
      this.onTap(this.$refs.video, this.playVideo);
      this.video = this.$refs.video;
      this.video.addEventListener("ended", () => {
        if (this.curIndex < this.videoList.length - 1) {
          this.curIndex++;
          this.$refs.video.src = res.value;
          this.$refs.video.play();
        }
      });
    }
  }
  function onTap(el, callback) {
    let touchStarted = false;
    el.addEventListener('touchstart', function (e) {
      touchStarted = true;
      setTimeout(() => {
        touchStarted = false
      }, 500)
    })
    el.addEventListener('touchmove', function (e) {
      touchStarted = false
    })
    el.addEventListener('touchend', function (e) {
      if (touchStarted) {
        callback(e)
      }
    })
  }
  </script>
  <style lang="scss">
  .video-player {
    position: relative;
    video {
      display: block;
      width: 100vw;
      height: 100vh;
      background-color: #243c70;
      object-fit: fill;
    }
    img {
      position: absolute;
      left: 50%;
      top: 50%;
      transform: translate(-50%, -50%);
      opacity: 0;
      &.shown {
        opacity: 1;
      }
    }
  }
  </style>
  ```















