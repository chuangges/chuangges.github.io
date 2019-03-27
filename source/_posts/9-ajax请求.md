---
title: Ajax 的全面总结
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
description: HTTP、Ajax、跨域方案、JSON、FormData、Base64、Blob
---

# 一、HTTP 
> 超文本传输协议

  1. 简介：用于 web 应用传输数据，只能由客户端发起并由服务端响应，具有无状态等特点。
  2. 传输单位：http 报文 (请求报文、响应报文)，报文结构可分为 请求/响应行、首部字段、实体部分。
    * 请求行：用于说明请求方法、请求地址、http 版本号 
    * 响应行：用于说明服务器 http 版本号、响应状态码、状态码的原因短句
    * 首部字段：分为 通用首部字段、请求首部字段、响应首部字段、实体首部字段
    * 实体部分：可以用实体首部字段加以说明，常用 content-type 说明实体内容的类型
  3. 操作方式
    * 浏览器的 url 地址栏 
    * 页面有 src 属性的标签（img、script、link 等） 
    * 带有 action 属性的 form 表单 
    * XMLHttpRequest 对象


# 二、Ajax
> JavaScript 异步网络请求，在网页不跳转不刷新的情况下能够刷新局部页面内容

## 原理
<div style="text-indent: 2em">在用户和服务器之间加了―个中间层 (Ajax 引擎)，通过 XmlHttpRequest 对象来向服务器发异步请求并获取数据，然后通过 js 操作 DOM 而更新页面，这样就让用户操作与服务器响应实现了异步化，从而 js 可以及时向服务器提出请求和处理响应而不阻塞用户，达到无刷新的效果;</div>

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
    * 参数位置：`url?参数=值&参数=值、xhr.send("参数=值&参数=值")`
  2. 场景
    * GET：直接在地址栏输入参数
    * POST：有密码、数据量过大 (无字符限制)、带有文件的表单


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



# 三、跨域
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



# 四、Ajax 常用的数据格式

## JSON
> 处理大量数据的规范格式

  * 对象：`var json_obj = { name: 'Alice', age: 20 }`
  * 字符串：`var json_str = “{ name: 'Alice', age: 20 }”`
  * 转换
    * 对象转字符串：`JSON.stringify(json_obj)`
    * 字符串转对象：`JSON.parse(json_str) / eval('('+json_str+')')`  
    * eval：不推荐使用，因为它会把 js 数据而不只是 json_str 都转为 json 对象


## FormData
> 处理包括二进制文件的表单数据并通过 post 方式发送到服务端

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




# 五、Base64

## 编码
> 用来将数据内容全部转换成 ASCII(可见字符) 的一个编码算法，并非加密算法

1. 理解 
  * 本质：一种将二进制数据转为文本数据的方案
  * 用途：早期用于邮件传输，现在常用于图片转码
  * 适用场景
    * 当访问外部资源很麻烦或受限时
    * 当图片的体积太小，占用一个HTTP会话不是很值得时。
    * 当图片是在服务器端用程序动态生成，各个访问用户显示不同时
  * 缺点：编码后的数据体积一般会变大, 而且 DataUrl 形式的图片不会被浏览器缓存
2. 





