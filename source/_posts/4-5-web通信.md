---
title: Web 通信和页面功能
tags:
  - Javascript
categories: Javascript
top: false
keywords:
  - js
date: 2019-03-24 23:09:57
description: HTTP 协议、Ajax 异步请求、Socket 实时通信、上传下载和数据储存等页面功能
---

# 一、HTTP 协议
> 超文本传输协议，它是基于 TCP/IP 协议的一个应用层协议，用于定义客户端与服务器通信的格式，具有单向请求、无状态等特点

## HTTP 报文
> HTTP 通信的传输单位，可分为 请求报文和响应报文，内部结构如下

  * 请求行：用于说明请求方法、请求地址、http 版本号 
  * 响应行：用于说明服务器 http 版本号、响应状态码、状态码的原因短句
  * 头部字段：分为 通用首部字段、请求首部字段、响应首部字段、实体首部字段
  * 实体部分：可以用实体首部字段加以说明，常用 content-type 说明实体内容的类型


## 操作方式
  * 浏览器的 url 地址栏 
  * XMLHttpRequest 对象
  * 页面有 src 属性的标签（img、script、link 等） 
  * 带有 action 属性的 form 表单 


## 连接方式
> 实际上是 TCP 协议的长/短连接，区别在于数据传输后是否立即关闭连接

  1. 短连接
    * HTTP1.0 默认方式。每次 HTTP 操作则建立一次连接，任务结束就立即关闭
    * 常用于 并发量大但每个用户无需频繁操作的场景，比如浏览器网址的 http 服务
  2. 长连接
    * HTTP1.1 开始默认方式。任务结束后并不会立即关闭 TCP 连接，再次请求时继续使用
    * HTTP 响应头会添加 `Connection: keep-alive`，而且可以在服务器软件中设定连接时间



## 工作流程

1. 全部流程
  <div align="center"> 
    ![HTTP 请求流程](/images/web/http_req.png)
  </div> 
2. 域名解析过程
  <div align="center"> 
    ![域名解析过程](/images/web/domain.png)
  </div> 
3. 三次握手
  <div style="text-indent: 2em">TCP 协议中，建立 TCP 需要与百度服务器握手三次，你先告诉服务器你要给服务器发东西（SYN），服务器应答你并告诉你它也要给你发东西（SYN、ACK），然后你应答服务器（ACK），共来回3次，称为3次握手。</div>
  


# 二、Ajax 请求
> 异步网络请求，能够在页面不跳转不刷新的情况下实现局部加载的异步通信，减少了传输数据量

## 原理
<div style="text-indent: 2em">在用户和服务器之间加了―个中间层 (Ajax 引擎)，通过 XmlHttpRequest 对象来向服务器发异步请求并获取数据，然后通过 js 操作 DOM 而更新页面，这样就让用户操作与服务器响应实现了异步化，从而 js 可以及时向服务器提出请求和处理响应而不阻塞用户，达到无刷新的效果。注意如果出现乱码问题就是因为编码格式冲突，发送中文数据时通过 encodeURI() 编码，接收中文数据时通过 decodeURI() 解码即可。</div>

## 优点
  * 使用异步方式与服务器通信，响应速度更快
  * 基于标准化的并被广泛支持的技术，不需要下载插件或者小程序
  * 页面无刷新，在页面内与服务器通信，减少用户等待时间，增强了用户体验
  * 可以把一些原本服务器的工作转接到客户端，利用客户端闲置的能力来处理，减轻了服务器和带宽的负担，节约空间和宽带租用成本


## 缺点
  * 对搜索引擎的支持比较弱
  * 可能会影响程序中的异常处理机制
  * 无法进行操作的后退，即不支持浏览器的页面后退
  * 安全问题，对一些网站攻击，比如 csrf、xxs、sql 注入等不能很好地防御


## 请求方式
  1. 区别
    * method：`GET、POST`
    * 请求头：POST 发送数据时必须设置
    * 安全性：GET 请求时暴露参数数据，安全性较低
    * 长度限制：GET 有长度限制，不适合发送大量数据
    * 请求缓存：GET 请求可以被缓存和保存，而 POST 请求不能
    * 参数位置：`url?参数=值&参数=值、xhr.send("参数=值&参数=值")`
  2. 场景
    * GET：安全性要求不高时，从服务器获取数据
    * POST：对于大量数据、密码数据等，提交到服务器处理

   

## ContentType
> 请求体类型，告诉服务器发送数据的格式

  * `application/x-www-form-urlencoded`：jquery ajax 默认类型，使用查询字符串格式的数据
  * `multipart/form-data`：常用于表单上传文件，设置 form enctype 为该类型
  * `application/json`：axios 默认类型，使用 json 格式的数据


## 原生写法
  ```js
  // 1、创建 XMLHttpRequest 对象 (用来和服务器交换数据的 js 内置对象)
  var xhr;
  if(XMLHttpRequest){
      xhr = new XMLHttpRequest();   // w3c 标准
  }else{
      xhr = new ActiveXObject('Microsoft.XMLHTTP')  // IE6、IE5
  }


  // 2、连接服务器和发送请求

  // GET 
  xhr.open('GET', url?参数=值, true);   // true 表示请求异步处理
  xhr.send();    

  // POST 必须设置请求头
  xhr.open("POST", url, true);   
  xhr.setRequestHeader("Content-type", "application/x-www-form-urlencoded");
  xhr.send("参数=值")


  // 3、监听回调函数状态和处理响应数据
  xhr.onreadystatechange = function(){
      // 判断响应是否接收完成、服务器是否成功处理请求
      if(xhr.readyState == 4 && xhr.status ==200){
          show.innerHTML = xhr.responseText;
      }
  }
  ```


## JQuery 写法
  ```js
  $.get(url?参数=值, calback)

  $.post(url, {参数：值}, callback)

  $.ajax({
      type:       // 数据的提交方式：get、post
      url:        // 请求地址
      async:      // 是否支持异步刷新，默认 true
      data:       // 需要提交的数据
      dataType:   // 服务器返回数据的类型，html、json, jsonp 等
      success: function(data){
      }           // 请求成功后的回调函数 
      error:function(data){
      }           // 请求失败后的回调函数 
  })
  ```

## Ajax 封装
  ```js
  function requestUrl(url, type, data, callback, error, async) {
      var async = (async == null || async.toString() == "" || typeof(async) == "undefined") ? true : !!async;
      $.ajax({
          url: url,
          method: type,
          async: async,
          data: data
      })
      .done(function( _data ) {
          if (_data.status == 200) {
              callback(_data.data);
          } else {
              if (typeof(error) == 'function') {
                  error(_data)
              } else {
                  alert(_data.data.errMsg)
              }
          }
      })
      .fail(function( jqXHR, textStatus ) {
          setTimeout(function() {
              requestUrl(url, type, data, callback, error, async);
          }, 1000);
      })
  }
  ```


# 三、Ajax 跨域
> A 网站的 js 代码试图访问 B 网站的数据

## 同源策略
<div style="text-indent: 2em">浏览器出于安全方面的考虑，只允许与本域下的接口交互。不同源的客户端脚本在没有明确授权的情况下，不能读写对方的资源。ajax的请求与访问同样会受到浏览器同源策略的限制，不能访问不同主域中的地址。</div>

## JSONP 跨域
> jsonp 只是一种非强制性协议而不是新技术，和 json、ajax 都没有任何关系

1. __原理__：利用 script 可以引用外部文件实现跨域请求的特点，动态添加 script 调用服务器的 js 脚本
2. __json 和 jsonp__
  * json：一种轻量级的数据交换格式
  * jsonp：一种借助 script 元素解决主流浏览器的跨域读取数据问题的方式
3. __实现方式__
  1. 定义数据处理函数：`var handler = function(data){ }`
  2. 创建 script 标签，src 的地址设置为 `url?callback=handler`
  3. 服务端在收到请求后，解析参数并返回数据，输出 fun(data) 字符串
  4. fun(data) 会放到 script 标签中作为 js 执行，即此时会调用函数 
  
  
## CORS 跨域
> 一个W3C标准，全称是"跨域资源共享，它允许浏览器向跨源服务器发出 xhr 请求

  1. __原理__：使用自定义的 HTTP 头部让浏览器与服务器沟通，决定请求是应该成功或失败
  2. __使用__：发送请求时附加一个额外的 origin 头部，比如 `Origin:http://www.nczonline.net`


## jQuery Ajax 跨域
  ```js
  $.ajax({
      type: "get",
      async: false,
      url: url,
      data: data,
      dataType: "jsonp",
      jsonp: "callback",         // 传递给请求处理程序或页面来获取回调参数 
      jsonpCallback: "handler"   // 自定义的 jsonp 回调函数名称  
      success: function(data){
          console.log(data)
      }，
      error: function(){ }
  })
  ```



# 四、Ajax 数据

## JSON
> 处理大量数据的规范格式

  * 对象：`var json_obj = { name: 'Alice', age: 20 }`
  * 字符串：`var json_str = “{ name: 'Alice', age: 20 }”`
  * 转换
    * 对象转字符串：`JSON.stringify(json_obj)`
    * 字符串转对象：`JSON.parse(json_str) / eval('('+json_str+')')`  
    * eval：不推荐使用，因为它会把 js 数据而不只是 json_str 都转为 json 对象


## FormData
> 处理包括图片和文件的表单数据并通过 post 方式发送到服务端

  * html 
    * form：`form id="form" method="post" enctype="multipart/form-data"`
    * input：`input type="file" name="file"`
  * 操作
    ```js
    // 1、实例一个空 FormData 对象之后 append 键值对
    var formdata = new FormData() 
    var file = document.getElementById("file").files[0]
    formData.append('file', file)
                              
    // 2、form元素对象 作为参数传入  
    var form = document.getElementById("form")
    var formdata = new FormData(form);                          

    // 3、getFormData 方法
    var formobj =  document.getElementById("form");
    var formdata = formobj.getFormData()

    // 其它 API：has、getAll、forEach
    formdata.delete("file")   
    var file = formdata.get("file")        
    ```



# 五、websocket
> 一种全双工通信协议(双向通信)，和 HTTP 都是基于 TCP 协议的应用层协议，两者有良好的兼容性但没有联系。它能更好的节省服务器资源和带宽，而且可以实现实时通讯

## 实时通信
  * Ajax 轮询
    * 原理：客户端设置 计时器，每隔一段时间就向服务器发送一次请求
    * 缺点：只能由客户端发起请求，而且需要服务器有很快的 资源处理速度
  * http 长轮询
    * 原理：客户端发起请求建立连接后，服务器会等到有内容更新时才去响应，否则不响应
    * 缺点：采用阻塞模型，需要服务器有很高的并发处理能力，会占用服务器更多的资源空间。    
  * WebSocket：只需要经过一个握手的动作，客户端与服务端就可以互相传送数据，不需要询问和等待


## 区分 Socket
  * WebSocket 在建立握手时通过 HTTP 传输数据，但是建立之后传输真正数据时不需要 HTTP
  * Socket 是应用层与TCP/IP协议族通信的中间软件抽象层，它其实并非一个协议而是一组接口

## 连接过程
  1. 浏览器、服务器通过 TCP 三次握手建立连接 (通信基础)
  2. 浏览器通过向服务器发起一个包含 WebSocket 支持版本号等附加头信息的 HTTP请求
  3. 服务器同样采用HTTP协议返回应答信息
  4. 当收到连接成功的信息后通过 TCP 通道进行传输通信
  5. 客户端/服务器端 关闭连接前可以自由传输数据

## API

  ```js
  // 建立连接
  var ws = null;
  function initWebSocket{
    // 浏览器支持则建立连接  
    if ('WebSocket' in window) {
        ws = new WebSocket('ws://10.148.221.210:5000')  

        // 连接成功则向服务器发送数据
        ws.onopen = function() {
            // 0、1、2、3 ：正在连接、连接成功、连接正在关闭、连接失败或已关闭
            if (socket.readyState===1) {
                ws.send("connect success")
            }
        })

        // 收到服务器数据则执行相关处理
        socket.onmessage = function(msg){ }

        // 连接失败则重连
        ws.addEventListener('error', fn)
    }
  }

  // 离开当前页面时断开连接
  ws.close()
  ```


# 六、表单数据

## 表单提交
  
  1. 问题：点击一次按钮而执行两次 Ajax 
  2. 原因：执行完 Ajax 请之求后，并没有阻止 submit 提交
  3. 解决方案
    * 不使用 type="submit" 的按钮，而使用 type="button"
    * 点击事件的回调函数中的最后一行添加：return false
    * 先解绑再绑定：`$("#btn").off("click").on("click", fn)` 


## 表单序列化
> 普通表单元素必须设置 name，checkbox 必须设置 name、value

  ```js
  var data = $("form").serialize();           // 字符串 
  var jsonArr = $("form").serializeArray();   // 数组
  var jsonObj = $("form").serializeObject();  // JSON

  $.fn.serializeObject = function () {
      var o = {};
      var a = this.serializeArray();
      $.each(a, function () {
          var val = this.value || ''   // 如果是 null 则赋值为 ''  
          if(o[this.name]) {           
              if(this.value){
                  o[this.name] = o[this.name] + "," + val;
              }
              
          } else {
              o[this.name] = val; 
          }
      });
      var $radio = $('input[type=radio],input[type=checkbox],select[multiple="multiple"]',this);
      $.each($radio,function(){
          if(!o.hasOwnProperty(this.name)){
              o[this.name] = null;
          }else if(o[this.name] == 'on'){
              o[this.name] = true;
          }else{
              o[this.name] = o[this.name];
          }
      });
      return o;
  };
  ```




# 七、页面功能

## 页面跳转
  1. 实现方式
    * 关闭当前页面，打开新页面
      * `a href="url"`
      * `window.location.href = url`
    * 保留当前页面，打开新页面
      * `a href="url" target="_blank"`
      * `window.open(url, "_blank")`
    * 模拟点击 a 标签
      * `$("#box")[0].click()`
      * `document.getElementById("box").click()` 
  2. 传递数据
    * url 查询字符串
    * cookie 等数据储存
    * window.open、window.opener  


## 页面刷新
  * `window.location.reload()`
  * `window.location.href = url`
  * `window.location.replace(url)`


## 文件下载
> 浏览器中的文件地址打不开时就会变为下载

  * `location.href = url`：
  * 借助 Blob 和 download 属性实现文本信息文件下载
  * 借助 Base64 实现任意文件下载

  ```js
  // location 下载文件
	function dl_file(){
	   var url = "http://www.baidu.com/"
	   location.href = url + "?name=mike&age=20"
	}

  //借助 Blob 和 download 属性实现文本信息文件下载
	function bolb_download(content, filename) {

    // 创建隐藏的可下载链接
    var link = document.createElement('a')
    link.setAttribute("download",name)
    link.setAttribute("target", "_blank")

    link.style.display = 'none'
    
    // 字符内容转变成 blob 地址
    var blob = new Blob([content])
    link.href = URL.createObjectURL(blob)
    
    // 模拟点击和移除
    document.body.appendChild(link)
    link.click()
    document.body.removeChild(link)
  };
  
  var textarea = document.querySelector('textarea');
	var btn = document.querySelector('input[type="button"]');
	if ('download' in document.createElement('a')) {
	    // 作为test.html文件下载
	    btn.addEventListener('click', function () {
	        bolb_download(textarea.value, 'test.html');    
	    });
	} else {
	    btn.onclick = function () {
	        alert('浏览器不支持');    
	    };
	}


  // 借助 Base64 实现任意文件下载
	function di_file(domImg, filename) {

    var link = document.createElement('a');
    link.download = filename;
    link.style.display = 'none';
    
    // 图片转 base64 地址
    var canvas = document.createElement('canvas');
    var context = canvas.getContext('2d');
    var width = domImg.width;
    var height = domImg.height;
    context.drawImage(domImg, 0, 0);
    link.href = context.toDataURL('image/png');
    
    // 模拟点击和移除
    document.body.appendChild(link);
    link.click();
    document.body.removeChild(link);
	}
  ```


## 文件在线预览
> 常用需求为移动端预览 pdf

* a 标签：`a href="fileurl" target="_blank"`
* 页面内嵌 `iframe`
* 通过标签嵌入内容
  * `embed :src="previewUrl" type="application/pdf" width="100%" height="100%"`
  * `object :src="previewUrl" width="100%" height="100%"`
* 通过预览插件
  * `pdfObject.js`：移动端不支持
  * `pdf.js`：移动端支持，但是插件本身太大，而且盖章无法正常显示


## 图片预览与上传
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
        if(index &gt; -1) return

        let imgBase64 = await this.fileReader(file)

        // 截取 data:image/png;base64 后面的纯字符串
        var base64Img = imgBase64.substring(imgBase64.indexOf(",") + 1);

        // 根据原始图片大小判断是否需要压缩：size 是字节数，1MB = 1024KB，1KB = 1024字节
        if(file.size &gt; this.max_size * 1024){
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



# 八、数据储存

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



# 九、用户登录

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




