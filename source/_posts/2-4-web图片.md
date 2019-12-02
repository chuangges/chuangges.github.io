---
title: Web 图片处理
tags:
  - 项目开发
categories: 项目开发
top: false
keywords:
  - 图片处理
date: 2019-02-28 20:12:36
description: 图片预览、图片上传、图片识别
---


# 一、预览上传

  <div align="center"> 
    ![图片上传流程图](/images/web/img_upload.png)
  </div> 

  ```js
  // input ref="upload_input" type="file" accept="image/*" capture="camera" @change="changeImg"

  // 模拟点击调用：输入框隐藏
  this.$refs.upload_input.click()
  
  // vue
  export default {
    methods: {
      async changeImg(){
        let file = this.$refs.upload_input.files[0]
        if(!file) return

        const index = this.fileList.findIndex(item => item.name == file.name)
        if(index > -1) return

        let imgBase64 = await this.fileReader(file)

        // 截取 data:image/png;base64 后面的纯字符串
        var base64Img = imgBase64.substring(imgBase64.indexOf(",") + 1);

        // 根据原始图片大小判断是否需要压缩：size 是字节数，1MB = 1024KB，1KB = 1024字节
        if(file.size > this.max_size * 1024){
          this.compressImg(imgBase64, file.name)
        }else{
          this.$emit("updateFileList", file, imgBase64)
        }
      },
      // 获取用户拍照的图片信息
      fileReader (file) {
        let _this = this
        let reader = new FileReader()
        reader.readAsDataURL(file)
        // 监听读取操作结束
        return new Promise(resolve => reader.onloadend = () => { return resolve(reader.result) })
      },
      // 压缩图片
      compressImg(bdata, name){
        var _this = this
        var quality = 0.8;   // 压缩质量：0.1 时最小压缩
        var canvas = document.createElement("canvas");
        var ctx = canvas.getContext("2d");

        var img = new Image();
        img.src = bdata;
        img.onload = function(){
            canvas.width = _this.targetWidth;
            canvas.height = _this.targetWidth * (img.height / img.width);
            ctx.drawImage(img, 0, 0, canvas.width, canvas.height);
            // 压缩后的 base64 图片
            var cdata = canvas.toDataURL("image/jpeg", quality);

            // 压缩率
            var oldImgLen = bdata.length;
            var newImgLen = cdata.length;
            var compresRadio = (((oldImgLen-newImgLen)/oldImgLen*100).toFixed(2))+'%';
            
            // 压缩后的文件格式
            let newFile = _this.dataURLtoFile(cdata, name);
            _this.$emit("updateList", newFile, cdata);
        }
      },
      // base64 转 file
      dataURLtoFile(dataurl, filename){
        let arr = dataurl.split(','), mime = arr[0].match(/:(.*?);/)[1],
            bstr = atob(arr[1]), n = bstr.length, u8arr = new Uint8Array(n);
        while(n--){
            u8arr[n] = bstr.charCodeAt(n);
        }
        return new File([u8arr], filename, {type: mime});
      }
    }
  }
  ```

  ```js
  // 图片压缩
  export default function compressImg (base64, w, callback, min, max) {
    var newImage = new Image()
    var quality = 0.6 // 压缩系数0-1之间
    newImage.src = base64
    newImage.setAttribute('crossOrigin', 'Anonymous')	// url为外域时需要
    var imgWidth, imgHeight
    newImage.onload = function () {
      imgWidth = this.width
      imgHeight = this.height
      var canvas = document.createElement('canvas')
      var ctx = canvas.getContext('2d')
      if (Math.max(imgWidth, imgHeight) > w) {
        if (imgWidth > imgHeight) {
          canvas.width = w
          canvas.height = w * imgHeight / imgWidth
        } else {
          canvas.height = w
          canvas.width = w * imgWidth / imgHeight
        }
      } else {
        canvas.width = imgWidth
        canvas.height = imgHeight
        quality = 0.6
      }
      ctx.clearRect(0, 0, canvas.width, canvas.height)
      ctx.drawImage(this, 0, 0, canvas.width, canvas.height)
      var base64 = canvas.toDataURL('image/jpeg', quality) // 压缩语句
      // 如想确保图片压缩到自己想要的尺寸,如要求在 min-max kb之间，请加以下语句，quality初始值根据情况自定
      while (base64.length / 1024 > max) {
        quality -= 0.01
        base64 = canvas.toDataURL('image/jpeg', quality)
      }
      // 防止最后一次压缩低于最低尺寸，只要quality递减合理，无需考虑
      while (base64.length / 1024 < min) {
        quality += 0.001
        base64 = canvas.toDataURL('image/jpeg', quality)
      }
      callback(base64)// 必须通过回调函数返回，否则无法及时拿到该值
    }
  }
  ```



# 二、OCR 识别

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













