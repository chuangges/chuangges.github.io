---
title: Web 图片和视频
tags:
  - 项目开发
categories: 项目开发
top: false
keywords:
  - 视频功能
date: 2019-03-02 21:02:57
description: Web 图片、Video 视频、Agora 声网
---

# 一、Web 图片

## 图片获取
> 本地图片信息获取的两种方式：H5 input 选择图片、native jsBridge 扫描或导入扫描身份证、银行卡等。


  ```js
  /**
   * @title H5 选择方式
   * @method changeImg        图片选中：获取 file 对象
   * @method fileReader       图片读取：file 转 base64
   * @method compress         图片压缩：缩放比例、图片质量等
   * @method dataURLtoFile    格式转换：base64 转 file
   * <input type="file" accept="image/*" capture="camera" @change="changeImg" />
  **/
  async changeImg(e) {
    let file = e.target.files[0];
    if (!file) return;
    // 图片预览
    let imgBase64 = await this.fileReader(file);
    // 图片上传：截取 data:image/png;base64 后面的纯字符串、file 对象
    let base64Img = imgBase64.substring(imgBase64.indexOf(",") + 1);
  },
  fileReader(file, maxSize) {
    let reader = new FileReader();
    reader.readAsDataURL(file);
    return new Promise(resolve =>
        (reader.onloadend = () => {
          resolve(reader.result);
        })
    )
  },
  compress(baseUrl, file, maxSize = 1, scale = 1, quality = 0.9) {
    // file.size 获取字节数：1MB = 1024KB，1KB = 1024字节
    if (file.size < maxSize * 1024 * 1024) {
        return baseUrl;
    }
    let img = new Image();
    img.src = baseUrl;
    img.setAttribute("crossOrigin", "Anonymous"); // url 为外域时需要
    return new Promise(resolve => 
        (img.onload = () => {
          let canvas = document.createElement("canvas"),
            ctx = canvas.getContext("2d"),
            w = img.width * scale,
            h = img.height * scale;
          canvas.width = w;
          canvas.height = h;
          ctx.drawImage(img, 0, 0, w, h);
          let baseImg = canvas.toDataURL("image/jpeg", quality)
          let file = this.dataURLtoFile(baseImg)
          resolve(file);
        })
    )
  },
  dataURLtoFile(dataurl, filename){
    let arr = dataurl.split(','), mime = arr[0].match(/:(.*?);/)[1],
        bstr = atob(arr[1]), n = bstr.length, u8arr = new Uint8Array(n);
    while(n--){
        u8arr[n] = bstr.charCodeAt(n);
    }
    return new File([u8arr], filename, {type: mime});
  }

  /**
   * @title Native 扫描方式
   * @method idCardScan     呼叫身份证扫描
   * @method getBank        导入银行卡
  **/
  window.idCardScan = function (options) {
    clearTimer()
    const idcard = function () {
      return new Promise((resolve, reject) => {
        window.jsBridge.status.idCard.status = false
        window.jsBridge.status.idCard.value = ''
        options = options ? JSON.stringify(options) : undefined
        window.HQAppJSInterface.requestScanCertificateCard(options)
        window.__tid = window.setInterval(success => {
          if (window.jsBridge.status.idCard.status) {
            clearTimer()
            resolve(SetCustomerRules(window.jsBridge.status.idCard.value))
            window.jsBridge.status.idCard.status = false
            window.jsBridge.status.idCard.value = ''
          } else {
            // reject('fail')
          }
        }, 30)
      })
    }
    return window.checkload().then(function (data) {
      return idcard()
    })
  }

  window.getBank = function () {
    clearTimer()
    let bank = function () {
      return new Promise((resolve, reject) => {
        window.jsBridge.status.bank.status = false
        window.jsBridge.status.bank.value = ''
        window.HQAppJSInterface.requestScanBankCard()
        window.__tid = window.setInterval(success => {
          if (window.jsBridge.status.bank.status) {
            clearTimer()
            resolve(window.jsBridge.status.bank.value)
            window.jsBridge.status.bank.status = false
            window.jsBridge.status.bank.value = ''
          } else {
            // reject('fail')
          }
        }, 30)
      })
    }
    return window.checkload().then(function (data) {
      return bank()
    })
  }

  // 组件中触发方法
  window.idCardScan().then(res => {
    this.$emit('scan', JSON.parse(res))
  })
  window.idCardScan({ skipOCR: 'Y' })
  ```



## OCR 识别
> 指将图片上的文字位置和内容智能识别为文字信息并返回，目前支持的场景有：`身份证识别、银行卡识别、名片识别、营业执照识别、行驶证驾驶证识别、车牌号识别、通用印刷体识别、手写体识别`。


  ```js
  // infoScan.vue：扫描识别组件，前端将图片信息传给后台对接 ocr 识别平台返回结果即可。
  <template>
    <Icon class="wrap" scan @click="nativeScan">
      <input v-if="h5" type="file" accept="image/*" @change="h5Scan">
    </Icon>
  </template>
  <script>
  export default {
    name: 'HqScan',
    props: {
      h5: {
        type: Boolean,
        default: () => false
      }
    },
    methods: {
      nativeScan () {
        if (!this.h5) {
          window.getBank().then(res => {
            this.report(JSON.parse(res))
          }).catch(console.error)
        }
      },
      // 读取并压缩，然后上传并识别图片
      h5Scan ($event) {
        const file = $event.target.files[0]
        let file = e.target.files[0];
        this.fileReader(file)
          .then(image => Promise.resolve(this.compress(file, img)))
          .then(img => Promise.all([this.recognition(img), this.upload(img)]))
          .then(this.merge)
          .then(this.report, toast)
      },
      upload (file) {
        return fetch(API.UPLOAD, { file }).then(({ url: [url] }) => {
          return Promise.resolve(url)
        }, this.reject)
      },
      recognition (image) {
        // OCR 识别：将图片信息发给后端即可
        return this.axios.post(API.OCR.BANK, { image }).then(({ data }) => {
          const code = data['rsp_code']
          if (code === '0000') {
            return Promise.resolve(data)
          } else if (code === '1111') {
            return Promise.reject(data['error_msg'])
          } else {
            return Promise.reject('银行卡识别失败')
          }
        })
      },
      merge ([info, cardImgUrl]) {
        toast('扫描成功')
        return Promise.resolve({ ...info, cardImgUrl })
      },
      reject (data) {
        return Promise.reject(data)
      },
      report (data) {
        this.$emit('scan', data)
      }
    }
  }
  </script>
  <style lang="less">
  .wrap {
    position: relative;
    input {
      position: absolute;
      width: 100%;
      height: 100%;
      left: 0;
      top: 0;
      -webkit-appearance: button;
      opacity: 0;
    }
  }
  </style>
  ```


## 人脸识别
> 主要有两大步骤：`1、人脸检测：找出人脸位置，用于美颜、换肤、抠图、换脸。2、人脸识别：识别用户，用于会员、支付`。

  ```js
  methods: {
    // 身份证等资料上传接口：获取 uniqueUserId
    fileUpload(){
      request({
        url: "/insure/ocrIdentityVerify",
        method: 'POST',
        data: params,
        headers: {
          "Content-Type": "multipart/form-data"
        }
      })
      .then(res => {
        if (res.code == 200) {
          this.faceRecognition(res.data.uniqueUserId);
        }
      })
    },
    // 人脸识别接口：获取人脸识别的跳转地址，并拼接上当前地址用于识别成功后返回原页面
    faceRecognition(uniqueUserId){
      request({
        url: "/insure/biometricsSigForInsure",
        method: 'GET',
        data: { uniqueUserId },
      })
      .then(res => {
        if (res.code == 200 && res.success) {
          // 存储 uniqueTbUserId
          this.saveUniqueUserId(uniqueUserId);
          window.location.href = res.data + encodeURIComponent(window.location.href);
        }
      })
    },
    /**
      * 查询人脸识别的结果，未通过人脸识别或者出错跳转订单详情
      * @param ticket 人脸识别返回
      * @param uniqueUserId   OCR识别返回
     **/
    async faceRecognitionResult(ticket, uniqueUserId){
      const resData = await request({
        url: "/insure/biometricsResultForInsure",
        method: 'POST',
        data: {
          ticket: ticket,
          uniqueUserId: uniqueUserId
        }
      })
    }
  },
  mounted(){
    const { ticket } = this.$route.query;
    let UniqueUserParms = JSON.parse(localStorage.getItem("UniqueUserParms")) || {};
    if(ticket && UniqueUserParms.uniqueTbUserId){
      this.faceRecognitionResult(ticket, UniqueUserParms.uniqueTbUserId)
    }
  }
  ```


# 一、Video 视频
> 自定义封装视频控件的样式和功能。

## 组件封装

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
      this.$refs.playControl.addEventListener("click", this.handleClickPlayControl);
    },
    destroyed() {
      this.draggable.destroy();
    }
  }

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

  ```js
  // draggable.js
  export default function Draggable(el, callback) {
    let startX = 0;
    let isTouchStart = false;

    el.addEventListener("touchstart", handleDown);
    window.addEventListener("touchmove", handleMove);
    window.addEventListener('touchend', handleUp);

    function handleDown(e) {
      isTouchStart = true;
      startX = e.touches[0].pageX;
    }
    function handleMove(e) {
      if (isTouchStart) {
        let deltX = e.touches[0].pageX - startX;
        startX = e.touches[0].pageX;
        callback(deltX);
      }
    }
    function handleUp() {
      isTouchStart = false;
    }
    function destroy() {
      el.removeEventListener("touchstart", handleDown);
      window.removeEventListener("touchmove", handleMove);
      window.removeEventListener('touchup', handleUp);
    }
    return { destroy }
  }
  ```


## 组件使用
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
      <img 
        width="60" 
        src="images/icon-play.png" 
        @click="playVideo" 
        class="{'shown': isPaused}" />
      <video-control v-if="video" :video="video" />
    </div>
  </template>
  <script>
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


# 三、Agora 声网
> 一个专注移动端的高清实时通话云服务解决方案，运用场景有：`语音通话（一对一、多对多）、视频通话（一对一、多对多）、互动直播（语音、视频）、互动游戏（游戏内置实时语音、视频通话）、录制（服务端录制）、信令（呼叫、消息传递、状态同步等）`。


## web 集成

  ```js
  /**
   * @description rtc.js：创建本地的音视频流并监听远端的音视频流
   * @param AgoraRTC：软件包安装命令为 npm i agora-rtc-sdk -S
   * @param UID：同一个频道不能重复，PC 使用当年*2+随机数，小程序使用当年+随机数
  **/
  import AgoraRTC from 'agora-rtc-sdk'

  let appId = 'cf95ca211f164383b833f2e9bcdae701';
  let videoContainerId = 'control_panel_remote_video';
  let localVideoContainerId = 'control_panel_local_video';

  export default function createRtcClient({ channel, UID, token }, onAfterJoin) {
    let client = AgoraRTC.createClient({ 
      mode: 'live',  // live：直播模式，用户分为主播和观众。rtc：通信模式，任何用户自由说话
      codec: "h264"  // 编解码可选 h264、vp8。与小程序链接时则必须使用 h264
    })

    let localStream;
    let audioMuted = false;

    AgoraRTC.Logger.setLogLevel(-1);
    AgoraRTC.Logger.enableLogUpload();

    // 初始化 Client 对象 
    client.init(appId, function () {
      client.join(token, channel, UID, function () {

        // 创建并初始化本地的音视频流
        localStream = AgoraRTC.createStream({ 
          streamID: UID, 
          audio: true, 
          video: true, 
          screen: false 
        });
        localStream.setVideoProfile('360p_3');
        localStream.setAudioProfile('speech_standard');

        localStream.init(function () {
          client.publish(localStream, function (err) {
            console.error("Publish local stream error: ", err)
          });
          localStream.play(localVideoContainerId)
        })
      })
    })

    // 监听远端的音视频流
    client.on('stream-added', function (evt) {
      var stream = evt.stream;
      var uid = stream.getId();

      client.subscribe(stream);
      
      if (onAfterJoin && !client.clientUID) {
        // 视频录制：须放到客户进入频道拿到 uid 之后触发
        client.clientUID = uid;
        onAfterJoin(uid)
      }
    })

    client.on('stream-subscribed', function(evt) {
      var remoteStream = evt.stream;
      remoteStream.play(videoContainerId, { fit: 'contain' }, function (err){
        if (err.status !== "aborted") {
          Notification.warning({
            title: '提示',
            message: '远端视频播放失败，点此重试',
            onClick() {
              remoteStream.resume()
                .then(function (result) {
                  console.log('resume-remote-success, 恢复成功：' + result)
                  notify.close();
                })
                .catch(function (reason) {
                  console.log('resume-remote-fail, 恢复失败：' + reason)
                })
            }
          })
        }
      })
      
    })

    client.on('stream-removed', function (evt) {
      var stream = evt.stream;
      stream.stop();
      console.log("Remote stream is removed " + stream.getId());
    })

    // 用户主动挂断，client.destroy 触发而离开频道，不触发 peer-leave
    client.on("peer-leave", function (evt) {
      var uid = evt.uid;
      writeLog("remote left user", uid);
      document.getElementById(videoContainerId).innerHTML = '';
    })

    client.destroy = function () {
      client.leave();
      if (localStream) {
        client.unpublish(localStream);
        localStream.stop();
        localStream.close();
      }
      const lcontainer = document.getElementById(localVideoContainerId);
      if (lcontainer) {
        lcontainer.innerHTML = '';
      }
      client = null;
      localStream = null;
      console.log("rtc client destroyed!");
    }

    client.toggleAudio = function () {
      if (audioMuted) {
        if (localStream.unmuteAudio()) {
          audioMuted = false
        }
      } else {
        if (localStream.muteAudio()) {
          audioMuted = true
        }
      }
      return audioMuted;
    }

    client.muteVideo = function () {
      localStream.muteVideo();
    }

    return client;
  }


  /**
   * @description store.js
   * @param roomNo：上线时获取的房间号，下线则清除
   * @param UID：同一个频道不能重复，PC 使用当年*2+随机数，小程序使用当年+随机数
   * @param rtcClientTickTimer：设定通话时间超过30分钟则提示，无操作则自动结束
  **/
  const UID = parseInt(new Date().getFullYear() * 2 + '' + parseInt(Math.random() * 100000));
  let currentRtcClient = null;
  let rtcClientTickTimer = 0;

  function doCreateRtcClient(roomNo, reconnect) {
    if(curRtcClient) { return }
    
    videoService.getAgoraToken({ uid: UID, roomNo })
      .then(res => {
        if (res.success) {
          return res.value
        }else {
          return Promise.reject(res)
        }
      })
      .then(token => {
        curRtcClient = createRtcClient({ channel: roomNo, UID, token }, uid => {
          if (reconnect !== true) {
            // 开始视频录制
            videoService.beginRecord(roomNo, uid).then(res => {
              if (!res.success) { notify('启用视频录制失败（不影响报案）', 'warning') }
            })
          }
        })
        longComunicationConfirm();
      }).catch(err => {
        notify('建立通话失败', 'warning');
        console.error(err)
      })
  }
  function longComunicationConfirm() {
    if (window.location.hostname !== 'video-claim.zhongan.io') {
      rtcClientTickTimer = setTimeout(() => {
        showConfirm('通话时间过长，为避免通话时间浪费，无操作30s后自动退出通话！', {
          confirmButtonText: '继续通话',
          cancelButtonText: '结束通话',
          showClose: false,
          showCancelButton: false
        }).then(() => {
          clearTimeout(rtcClientTickTimer);
          // 如果已经主动挂断，则无需再次提示
          if (rtcClientTickTimer > 0) {
            longComunicationConfirm();
          }
        })
        rtcClientTickTimer = setTimeout(destroyRtcClient, 30 * 1000)
      }, 30 * 60 * 1000);
    }
  }

  function destroyRtcClient() {
    if (curRtcClient) {
      curRtcClient.destroy();
      curRtcClient = null;
    }
    ping.stop();
    logger.send({ type: 'destroyRtcClient' });
    clearTimeout(rtcClientTickTimer);
    rtcClientTickTimer = 0;
  }
  ```


## 小程序集成
> 开启小程序支持的服务、创建微信小程序组件 `<live-pusher>、<live-player>`、直接引用 SDK (`require('../lib/Agora.2.4.1.js')`) 并创建音视频流对象 (API 同上)。

  ```xml
  <live-pusher 
    url="{{pushUrl}}" 
    autopush 
    mode="RTC" 
    enable-camera="{{enableCamera}}" 
    device-position="{{devicePosition}}" 
    waiting-image="/images/connecting.jpg" 
    background-mute="{{false}}" 
    auto-pause-if-open-native="{{false}}" 
    bindstatechange="handlePusherStateChange"
    bindnetstatus="handlePusherNetStatusChange" 
    binderror="handlePusherError"
  />

  <live-player 
    hidden="{{!enableCamera}}" 
    src="{{playUrl}}" 
    mode="RTC" 
    autoplay 
    sound-mode="speaker" 
    object-fit="fillCrop" 
    auto-pause-if-open-native="{{false}}" 
    bindstatechange="handlePlayerStateChange" 
    bindnetstatus="handlePlayerNetStatusChange"
  />
  ```








