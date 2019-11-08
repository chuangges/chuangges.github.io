---
title: Web 图片处理
tags:
  - 项目开发
categories: 项目开发
top: false
keywords:
  - 项目开发
date: 2019-02-26 20:12:36
description: 用户登录、图片上传
---


# 一、用户登录

## 实现逻辑
  <div align="center"> 
    ![登录注册逻辑](/images/web/login.png)
  </div> 


## 实现过程
  1. 利用 Ajax 将用户名和密码发送到对应接口
  2. 后台接收到对应数据后，通过用户名去查询对应密码
  3. 对比查询到的密码和前台传过来的密码是否相等，如果相等返回成功，否则返回失败
  4. 前端接收到登陆成功状态，将对应登陆成功的样式显示在对应位置，并将 username、userid 保存为 cookie (登陆状态保持)


## Token
> 遵从 oauth2.0 规范的授权令牌，它是一种身份/权限的认证方式 

  1. 生成方式：服务端接收客户端发送的 userId，然后根据算法和编码方式生成
  2. 优势
    * 可以避免 CSRF 攻击
    * 完全由应用管理，可以避开同源策略
    * 可扩展性强(不用存储)，还可用于 APP
    * 可以是无状态的，可以在多个服务间共享


# 二、图片的预览与上传

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















