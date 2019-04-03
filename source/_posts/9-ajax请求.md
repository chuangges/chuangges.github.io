---
title: 通信方式和页面功能
tags:
  - Javascript
  - Ajax
categories: Javascript
top: false
keywords:
  - js
  - ajax
  - javascript
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
    ![HTTP 请求流程](/images/post/http_req.png)
    <!-- <img src="/images/post/http_req.png" alt=""> -->
  </div> 
2. 域名解析过程
  <div align="center">
    ![域名解析过程](/images/post/domain.png)
    <!-- <img src="/images/post/domain.png" alt=""> -->
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
	    var link = document.createElement('a');
	    link.download = filename;
      link.style.display = 'none';
      
	    // 字符内容转变成blob地址
	    var blob = new Blob([content]);
      link.href = URL.createObjectURL(blob);
      
	    // 模拟点击和移除
	    document.body.appendChild(link);
      link.click();
	    document.body.removeChild(link);
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
      
	    // 图片转base64地址
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
	};
  ```


## 图片上传
  <div align="center">
    ![图片上传流程图](/images/post/img_upload.png)
    <!-- <img src="/images/post/img_upload.png" alt=""> -->
  </div> 

  ```js
  // oss图片上传
  var ossUpload = {
      // 获取上传文件后缀
      getSuffix: function(filename) {
          var pos = filename.lastIndexOf('.');
          var suffix = '';
          if (pos != -1) {
              suffix = filename.substring(pos + 1)
          }
          return suffix;
      },
      // 配置上传参数
      setUpParam: function($target ,data) {
          var formData = new FormData();
          $.each(data, function(i, n) {
              formData.append(i, n)
          })
          formData.append('file', $target[0].files[0]);
          return formData;
      },
      // 上传图片
      uploadImg: function(url, formData, callback) {
          $.ajax({
              url: url.host,
              type: 'POST',
              data: formData,
              processData: false,
              contentType: false
          })
          .done(function(data) {
              callback(data);
          })
          .fail(function() {
              setTimeout(function() {
                  uploadImg(url, formData, callback);
              }, 1000)
          })
      }
  }
  ```



# 八、数据储存

## cookie
> 同一网站上所有页面共享，有大小、数量限制(4kb) 和过期时间，适用于保存一些用户名等简单信息
          
  * 设置：`$.cookie("name", data, option)`    
  * 获取：`$.cookie("name")`             
  * 删除：`$.cookie('name', null)`   


## localStorage
> 本地存储的数据没有过期时间，而且可储存电话本等持久的大量数据

  * 设置：`localStorage.setItem(key, value) / .key = value`
  * 读取：`localStorage.getItem(key) / .key`
  * 删除：`localStorage.removeItem(key)/ clear()`


## sessionStorage
> 会话存储，用户关闭浏览器窗口后就会删除数据，基本操作同上


## 以上区别
  * 传递
    * cookie 数据通常经过加密，而且会在浏览器和服务器间来回传递
    * 后两个不会自动把数据发给服务器，仅在本地保存。
  * 存储大小
    * cookie 数据大小不能超过4k，始终在同源的http请求中携带
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
> Application Cache。浏览速度较快，而且减少服务器负载 



# 九、用户登录

## 实现逻辑
  <div align="center">
    ![登录注册逻辑](/images/post/login.png)
    <!-- <img src="/images/post/login.png" alt=""> -->
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




