---
title: Node.js 基础入门
tags:
  - Node.js
categories: Node.js
top: false
keywords:
  - node
date: 2019-04-16 00:49:49
description: NodeJS 特点、运行机制、安装配置、核心模块
---

# 一、NodeJS
> 用于创建高性能的Web服务器

  <div style="text-indent: 2em">脚本语言都需要一个解析器才能运行，浏览器是html中Js代码的解析器。NodeJS 则是 一个独立运行的 js 代码解析器，它封装了 V8 引擎并引入了 commonjs 规范，分别解决了js比较慢和乱的问题，填补了 js 在服务器端的空白。</div>
  <div style="text-indent: 2em">每一种解析器都是一个运行环境，不但允许 JS 定义各种数据结构并进行各种计算，还允许JS使用运行环境提供的内置对象和方法做一些事情。例如运行在浏览器中的 JS 用于操作 DOM，浏览器就提供了 document 等内置对象。而运行在 NodeJS 中的 JS 用于操作磁盘文件或搭建 HTTP 服务器，NodeJS 就相应提供了 fs、http 等内置对象。</div>


## 特点
  * 轻量高效 
  * 事件驱动模型
  * 异步非阻塞 I/O 模型
  * 单进程单线程应用程序


## 不足
  * 不适合处理复杂逻辑
  * 不适合计算密集型应用
  * 不适合单用户多任务的程序
  * 代码某个环节报错则整个系统崩溃
  * 只支持单核 CPU 而不能充分利用多核 CPU 服务器


## 适用场景
  <div style="text-indent: 2em">NodeJS 处理并发的能力较强，但处理计算和逻辑的能力很弱，因此适用于 高并发、I/O 密集、少量业务逻辑 的场景，比如 RESTFUL API、实时聊天、客户端逻辑强大的单页 APP。一般由前端处理复杂逻辑，NodeJS 只提供异步 I/O，这样可以实现对高并发的高性能处理。</div>

              
# 二、NodeJS 程序

## 功能架构    
  <div align="center">
    ![Texture Mapping](/images/frame/node-framework.png) 
  </div> 
            
  * __顶部__：Js 编写的应用程序和模块
  * __中间__：通过代码包装和暴露接口而实现 js 可以调用 c++ 代码
    * __Bindings__：绑定核心库的依赖
    * __Addons__：绑定第三方或者自定义 C/C++
  * __底部__：C/C++ 编写的内部组件，提供了系统底层操作方法
    * __V8__：Google 开源 js 引擎，用于将 Js 代码编译为机器码执行以便快速编译
    * __libuv__：跨平台的底层封装，实现事件循环、文件操作等异步功能。
            

## 运行机制
> IO 操作的核心是通过事件机制实现异步执行

  * 同步异步：操作任务是否按顺序依次执行
  * 阻塞非阻塞：IO 操作是否影响主进城执行其它代码

  <div style="text-indent: 2em;">采用了单线程模型，它使用一个主线程处理所有的请求，当接收到操作请求后就作为一个事件放入任务队列中，然后继续接收其他请求，如果有 I/O 操作（比较耗时）就交给 libuv 的工作线程去执行，所以主线程速度快而且不会阻塞，这就是 `单线程、异步 I/O`。将所有操作都注册为一个事件并等待触发的方式就是 `事件驱动`。</div>

  <div style="text-indent: 2em; margin-top: 15px;">当主线程没有请求接入时，就开始通过事件循环机制查看任务队列来检查队列中是否有要处理的事件，如果是 I/O 任务就交给 libuv 的工作线程去执行并指定回调函数，否则就直接处理并返回结果给上层。当工作线程中的 I/O 任务完成后执行指定的回调函数，把这个完成的事件放到事件队列中等待事件循环时处理。这就是 `事件循环`。</div>

  <div style="text-indent: 2em; margin-top: 15px;">如果 I/O 操作由于复杂的逻辑处理而发生了阻塞，但返回的结果是暂时在事件队列中等待，由事件循环自动依次取回到主程序中恢复执行，而事件队列在主程序之外存储事件，所以不会干扰主程序执行。这就是 `非阻塞 I/O`。</div>


## 线程机制
  * NodeJs 内部通过线程池来完成异步操作，libuv 针对不同平台的差异性实现了统一调用，所以 NodeJS 程序的单线程仅仅指 Js 运行在单线程，程序本身并非单线程
  * libuv 默认创建一个由四个线程组成的线程池来加载异步操作，但如今的操作系统已经对很多 I/O 任务提供了异步接口，所以只有不存在其他方式时，异步 I/O 才会使用线程池



# 三、Web 服务器

## 服务器
> 从硬件上来看，服务器是一台配置比较高的PC机器。但是从软件角度根据用途可分为 web 服务器、数据库服务器等，用来处理客户端请求的服务器有以下两种：
          
  * __web服务器__
    * 又称 HTTP 服务器，包括 Apache、Nginx 等
    * 专注于处理 HTTP 协议、传递本地磁盘上的静态资源给客户端，常用于提供代理服务
  * __应用服务器__
    * 提供可以处理业务逻辑的方法，包括 WebLogic、Tomcat 等
    * 常用来加载动态内容和处理响应数据并返回给客户端，但则只能访问其安装目录中的文件


## 代理
  * __代理服务器__
    * 一种具有转发功能的应用程序，扮演了位于服务器和客户端之间"中间人"的角色
    * 接收由客户端发送的请求并转发给服务器，同时也接收服务器返回的响应并转发给客户端
  * __正向代理__
    * 隐藏了真实的请求客户端，客户端请求的服务都被代理服务器代替来请求
    * 比如翻墙的原理就是在国外搭建一台代理服务器，代理去访问国外网址并返回结果给国内客户端
  * __反向代理__
    * 隐藏了真实的服务端，反向代理服务器会帮客户端把请求转发到真实服务器
    * 常用来做负载均衡，比如访问百度时会有很多服务器接收服务
  * __应用__
    * web 项目开发时联合使用 apache + tomcat + nginx 
    * 访问静态、动态资源时使用 apache/tomcat 解析
    * nginx 作为反向代理服务器，同时支持高并发

    
## 运行机制
  <div style="text-indent: 2em; margin-top: 15px;">Apache 等传统 Web 服务器采用了多线程模型，它会为每个客户端请求分配一个线程来同步执行 I/O操作，系统通过线程池进行线程切换来减少时间开销。这种方式容易造成阻塞，而且浪费 CPU 和内存，当连接达到一定数量时可能会导致服务器耗光内存而崩溃，只适用于 异步操作较少但业务逻辑较复杂的应用。这就是 `线程驱动`。</div>
                


# 四、安装配置
> window 系统

## 安装
> 本质是把 NodeJS 执行程序复制到一个目录，然后保证这个目录在系统 PATH 环境变量下，以便终端下可以使用 node 命令

  * 版本：Windows Installer(.msi) 64位
  * 地址：建议不要安装在 C 盘而修改默认地址为 "D:\nodejs\"
  * 命令
    * 查看版本：`node -v`
    * 交互模式：`node`
    * 执行文件：`node hello.js`


## 配置

### 全局安装和缓存路径

  1. 新建文件夹：`node_global、node_cache`
  2. 执行命令
    * `npm config set cache "D:\nodejs\node_cache"`
    * `npm config set prefix "D:\nodejs\node_global"`
  3. 修改文件：node_modules\npm\npmrc
    * `prefix = D:\nodejs\node_global`
    * `cache = D:\nodejs\node_global`


### 环境变量
> 全局使用 npm、cnpm 等命令

  <div style="text-indent: 2em; margin-top: 15px;">path 指系统默认指定到某一路径，运行命令时会先从这些路径中开始找。另一种方法是在系统变量中设置 NODE_PATH = 安装的根目录，NodeJS 允许通过变量 NODE_PATH 指定额外的模块搜索路径。所以配置方法如下：</div>

  * 用户变量 PATH 末尾添加 `";D:\nodejs\node_global"`
  * 系统变量新增 `NODE_PATH: "D:\nodejs\node_global\node_modules"`


## 安装 cnpm
> npm 服务器在国外而安装速度慢，所以大多都使用淘宝对 npm 的镜像服务器 cnpm，它和 npm 命令相同

  * 安装：`npm install cnpm -g --registry=https://registry.npm.taobao.org`  
  * 查看：`cnpm -v`
  * 安装依赖：`cnpm install`
  * 安装失败的解决方案：`npm config set registry https://registry.npm.taobao.org`

                  
## 安装 nodemon         
> 编写调试 Node 项目时，每次修改代码后都需要重新启动，比较麻烦。nodemon 工具可以监听代码文件的变化，并自动重启 Node 服务器和数据库服务器

  ```js
  // 安装
  cnpm install -g  nodemon

  // app.js
  var http = require('http')
  http.createServer((req, res) => {
      res.end('hello node');
  }).listen(3000);

  // 启动
  nodemon app
  ```


    



