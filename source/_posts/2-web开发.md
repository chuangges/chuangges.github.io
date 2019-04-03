---
title: web 项目开发
tags:
  - Skill
categories: Web Skills
top: false
keywords:
  - tool
date: 2019-02-17 15:41:48
description: 前端技能和网站、浏览器结构和渲染、渲染和框架模式、Restful 规范、模块化开发
---

# 一、技术栈
> web 开发就是呈现用户可理解的界面并响应用户操作

<div align="center">
![前端技能图](/images/post/technology.png)
<!-- <img src="/images/post/technology.png" alt=""> -->
</div> 


# 二、程序员网站       
  1. __常用__
    * [印记中文](https://www.docschina.org/)：教程中文版
    * [站长工具](http://tool.chinaz.com/tools/openweb.aspx/)：代码整理和测试
    * [在线工具](http://tool.lu/)：代码处理工具合集
    * [Github](https://github.com/)：代码托管
    * [Stack Overflow](https://stackoverflow.com/)：技术问答社区
    * [Learn Anything](https://stackoverflow.com/)：思维导图形式学习技术
  2. __提升__
    * [百度脑图](http://naotu.baidu.com/)：思维导图          
    * [ProcessOn](https://slides.com/)：脑图工具         
    * [Slides](https://www.processon.com/)：WebPPT 编辑器        
    * [CanIuse](https://caniuse.com/)：浏览器兼容         
    * [Overapi](http://overapi.com/)：API速查手册  
    * [RunJS](https://runjs.cn/)：JS在线编辑和分享         
    * [Standardjs](https://standardjs.com/)：JS编码规范   
    * [Faviconer](https://www.favicon-generator.org/)：图标生成器                    
  3. __了解__
    * [零花钱](https://github.com/easychen/howto-make-more-money)：接单网站 
    * [Electron](https://electronjs.org/)：跨平台程序  
    * [.gitignore](https://github.com/github/gitignore)：不同语言项目 
    * [墨刀](https://modao.cc/)：在线制作移动应用原型 



# 三、浏览器

## 页面判断
> PC 端还是移动端

  * 功能检测 “touchstart” in document
  * 特征检测  navigator.userAgent


## 分层结构
> 浏览器的抽象分层结构图中将浏览器分成了以下子系统

  * 用户界面：用于和用户进行交互的功能组件，如地址栏、返回、前进按钮等
  * 浏览器引擎：用于查询和操作渲染引擎的界面
  * 渲染引擎：负责显示请求的内容，如果请求 HTML 则它解析 HTML 和 CSS 并显示
  * 网络：负责处理网络相关的事务，如 HTTP 请求等
  * UI 后端：负责绘制提示框等浏览器组件，底层使用操作系统的用户接口
  * Js 解释器：负责解析和执行 JavaScript 代码
  * 数据存储：负责持久存储 cookie 和缓存等应用数据

  <div align="center">
    ![浏览器结构](/images/frame/browserCom.png)
    <!-- <img src="/images/frame/browserCom.png" alt=""> -->
  </div> 


## 渲染流程
  * 解析HTML结构（构建DOM树）
  * 加载外部脚本和css文件（构建CSSOM树）
  * 解析并执行脚本代码（js 代码）
  * DOM 树构建完成（此时会触发DOMContentLoaded事件）
  * 加载外部静态文件（图标图片等）
  * 页面加载完成（此时会触发load事件）

  <div align="center">
    ![浏览器结构](/images/frame/browerRender.png)
    <!-- <img src="/images/frame/browerRender.png" alt=""> -->
  </div> 


## 渲染问题
  * 资源下载
    * 图片下载不会产生阻塞
    * css 下载时会阻塞渲染，但带有 media 属性除外
    * 遇到 script 标签时，DOM 构建停止直到 js 脚本下载并执行完毕，此时浏览器一般会下载其他资源但不会解析。如果 js 中有对 CSSOM 的操作，还会先确保 CSSOM 已经被下载并构建。
  * 重绘重排导致重新进行渲染树的生成
    * 重绘：简单外观的改变就会引起重绘，比如颜色变化等。重排一定重绘。
    * 重排(回流)：重新计算布局，通常由元素的结构、增删、位置、尺寸变化引起。比如 img 下载成功后替换填充页面 img 元素而引起尺寸变化。也会由 js 的属性值读取引起，比如读取 offset、scroll、cilent、getComputedStyle 等信息。
    * 优化


    * 直接改变 className，如果动态改变样式则使用 cssText
    * 让要操作的元素进行离线处理，处理完后一起更新
      * 使用 `display: none`，只引发两次回流和重绘
      * 使用 `DocumentFragment` 进行缓存操作，引发一次回流和重绘
      * 使用 `cloneNode(true/false)、replaceChild`，引发一次回流和重绘



## 渲染优化
  * 简化 DOM 结构，最好不深于六层
  * 尽量缓存 DOM 查找，查找器尽量简洁
  * 涉及多域名的网站，可以开启域名预解析
  * 动画尽量使用在 绝对/固定 定位 的元素上
  * 隐藏在屏幕外 或 页面滚动时，尽量停止动画
  * DOM 操作尽量通过缓存减少次数，避免过度触发回流
  * js 脚本放在页面 body 底部，减少对其他过程的阻塞
  * Js 修改元素样式操作尽量减少，使用类名修改或动画操作样式
  * 减少首次下载的文件数量大小，使用图片懒加载、js 按需加载等方式
  * css 尽量层次简单并放入 head，提前加载并防止 html 渲染后重新结合 css 引起页面闪烁


# 四、渲染方式
> 客户端是上网时使用的手机、电脑等机器，而服务器是性能较强大的机器，可保持长时间运行并保证可随时访问服务器上文件

## 客户端渲染
  * 实现原理：浏览器异步请求服务器获取数据，然后浏览器拼接 数据和模板文件 得到最终的 HTML字符串，最后直接解析并渲染到页面。
  * 优势
    * 页面切换速度快
      * 传统网站中的不同页面切换是通过服务器加载一个新的页面
      * SPA 模型中是通过动态地重写部分页面实现局部刷新和懒加载
    * 一定程度上减少了服务器的压力，浏览器负责页面逻辑和渲染
    * 前后端分离设计，服务器只需要提供接口
  * 问题
    * SEO：传统的搜索引擎只会从 HTML 中抓取数据，导致前端渲染的页面无法被抓取。
    * 首屏性能：用户首次加载页面时，需要先下载框架和应用程序的代码，然后再渲染页面。比如常用的 SPA 会把所有 JS 整体打包导致文件太大，页面加载时间较长而可能导致白屏问题，用户体验比较差。

## 预渲染
> 用来改善少数营销页面的 SEO 而无需使用 web 服务器实时动态编译 HTML

  * 实现原理：即在构建阶段简单地生成匹配特定路由的静态 html 文件，可以通过 webpack 的 prerender-spa-plugin 轻松添加。
  * 优点：设置简单，并且可以将前端页面作为一个完全静态的站点
    缺点：搜索引擎不能抓取到 网页内容和结构，而且不能插入动态数据


## 服务端渲染
> 指传统的 ASP、Java 或 PHP 的渲染机制，是预渲染的升级机制

  * 实现原理：浏览器发起请求后，服务端将 数据和模板拼接生成的 html 字符串返回给浏览器，浏览器只是进行解析和渲染。
  * 优点
    * 更好的 SEO：搜索引擎爬虫可以抓取渲染好的页面
    * 更少的客户端电量消耗：对于电量有限的手机或平板比较重要
    * 更快的内容到达时间：首屏加载更快而避免白屏问题，因为服务端只需要返回渲染好的 HTML
  * 缺点
    * 更多的服务器端负载（CPU 和内存资源）
    * 更复杂的开发和部署（兼容 Node 执行环境）
    * 不利于前后端分离，开发效率低


## 同构渲染
> 在服务器和客户端都可以运行的代码程序叫 同构，注意需要兼容 Node.js 的执行环境

  * 实现原理：浏览器发起请求后，服务器将 数据和模板拼接生成的 html 字符串返回给浏览器，浏览器将其插入到指定节点后渲染页面。
  * 优点
    * 提高首屏性能
    * 对搜索引擎友好，有助于 SEO
    * 前后端共用一套 Node.js 代码，节省开发时间
  * 缺点
    * 内存溢出
    * 异步操作
    * 大量计算、个性化缓存等性能
    * 服务器端和浏览器的环境差异
	* 复杂类型无法转义为字符串形式发送给前端


## 架构选择
  * 同构前一定要考虑到浏览器和服务器的环境差异，站在更高层面考虑
  * 对于需要做 SEO 的项目，如果是一个后台应用，则只要首页做一些静态内容宣导即可。如果是内容型网站，那么可以考虑专门做一些页面给搜索引擎。
  * 对于前端渲染首屏问题，我们可以采用分拆打包（路由库）、交互优化（加载动画）、部分同构（使用同构把菜单和页面骨架渲染出来） 等一些方式合理利用同构。



# 五、框架模式

## 框架和库
  * 类库：一个有组织的功能集合，用于提供特定功能的接口并被调用
  * 框架：构建应用程序的整体架构，使开发者可以专注于逻辑处理而提高开发效率
  * 联系：框架一般会集成大量库并在合适地方调用，但有时也可称之为库，两者区别只在于思考角度


## 框架模式

### MVC
  * 实现思路：所有的终端用户请求被发送到 Controller，Controller 依赖请求去选择加载哪个模型并附加到对应 View，最终 View 作为响应发送给终端用户。
  * 缺陷：包含大量的业务逻辑后，代码就会难以阅读和维护。

  <div align="center">
    ![MVC架构图](/images/frame/mvc.png)
    <!-- <img src="/images/post/technology.png" alt=""> -->
  </div> 


### MVP
  * 实现思路：切断的 View 和 Model 的联系, 让 View 只和 Presenter（原Controller）交互，减少在需求变化中需要维护的对象的数量，相比 MVC 成本更低而且更容易理解。
  * 缺陷：最接近用户的界面是需要根据需求变化而频繁更改的。

  <div align="center">
    ![MVP架构图](/images/frame/mvp.png)
    <!-- <img src="/images/frame/mvp.png" alt=""> -->
  </div> 


### MTV
  * 实现思路：Model 负责业务对象和数据库的关系映射(ORM)，Template 负责如何把页面展示给用户(html)，View 负责业务逻辑并在适当时候调用 Model和Template。此外，通过一个URL分发器将一个个URL的页面请求分发给不同的View处理，View再调用相应的Model和Template。

  <div align="center">
  ![MTV架构图](/images/frame/mtv.png)
  <!-- <img src="/images/frame/mtv.png" alt=""> -->
  </div> 


### MVVM
  * 实现思路：View 接收用户交互请求并发给ViewModel --> ViewModel 操作 Model 数据更新 --> Model 更新后通知 ViewModel 数据化 --> ViewModel 更新 View 数据。这样开发者只需关注业务逻辑，不需要手动操作DOM和关注数据状态的同步问题，复杂的数据状态维护完全由 MVVM 来统一管理，完美解决了复杂交互导致代码难以维护的问题。
  * 优点
    * 独立开发（可专注于业务逻辑和数据）
    * 低耦合性（View 和 Model 相互独立）
    * 可重用性（可让多个 View 重用一段视图逻辑）
    * 方便测试（可测试 ViewModel，而 Controller 不可）
  * 缺点
    * 调用复杂度增加
    * viewModel 会越来越庞大（逻辑多）
    * 类会增多（每个VC都附带一个 viewModel）

  <div align="center">
  ![MVVM架构图](/images/frame/mvvm.png)
  <!-- <img src="/images/frame/mvvm.png" alt=""> -->
  </div> 


## 框架分类
  * 可视化组件：Echarts
  * UI框架：Bootstrap、EasyUI、ElementUI
  * JS框架：jQuery、Zepto.js、NodeJs、requirejs、VueJs、angularJS、reactJS



# 六、Restful 架构
<div style="text-indent: 2em">RESTful 是一种架构的规范与约束、原则而并非架构或标准，符合这种规范的架构就是 RESTful架构。基于这个风格设计的软件可以更简洁，更有层次，更容易实现缓存等机制，常用于客户端和服务器交互类的软件。</div>

## 架构
  * RESTful架构：面向资源的架构，即针对资源设计接口。客户端操作通过资源对应的 URL 进行操作，从而访问服务器端资源。
  * SOA架构：面向服务的体系结构。SOA将不同的功能组件视为一种服务并分别封装，降低了组件之间的耦合程度，增加了代码的复用程度。


## 特点
  * 资源；作为资源的标识，比如 html / jpg / json
  * URI：统一资源定位符，即每个 URI 对应一个特定的资源
  * 无状态：所有资源都可以通过 URI 去定位，而不与其他资源产生耦合
  * 统一接口：数据元操作CURD 分别对应http的 get、post、put、delete


## URI 
  1. __区别 URL__
    * URI：统一资源定位符，对应具体的资源。
    * URL：统一资源标识符，对应具体的资源的地址
    * 联系：URL 是属于 URI 的一部分 
  2. __规范__
    * 不用大写
    * 用中杠 - 不用下杠 _ 
    * 参数列表要 encode
    * 名词表示资源集合，资源集合时使用复数形式如 zoos/1/animals 
    * 每个网址代表一种资源，所以网址中不能有动词，一般只能有名词，而且所用的名词往往与数据库的表格名对应
  3. __版本__
   <div style="text-indent: 2em">应该将API的版本号放入到URI中：/api.example.com/v1/zoos</div>
  4. __通信__
    * 前后端统一使用 json 数据
    * 客户端通过url中的 `？& ,` 实现过滤、搜索等复杂操作
    * 客户端通过标准的 HTTP 方法（`GET获取、POST新建、PUT更新、DELETE删除`）对应服务器端资源 CRUD（数据元操作，包括 `SELECT、CREATE、UPDATE、DELETE`）



# 七、模块化编程

<div style="text-indent: 2em">早期引用 JS 文件时直接通过 script 即可，但是随着项目的复杂度越来越大，这种方式带来了 逻辑混乱、可维护性差等问题。为了编写可维护的代码，我们采用将代码合理拆分到不同的文件里，每一个文件就是一个模块，文件路径就是模块名，这种组织代码的方式就是模块化。</div> 

## 优势
  * 开发效率高，有利于多人协同开发
  * 解决项目中的全局变量污染的问题
  * 功能单一，方便代码的复用和维护
  * 解决文件依赖问题，无需关注文件引用顺序


## JS 环境
  * 服务器端
    * 相同的代码需要多次执行
    * CPU 和内存资源是瓶颈
    * 加载时从磁盘中加载
  * 浏览器端
    * 代码需要从一个服务器端分发到多个客户端执行
    * 带宽网络是瓶颈
    * 加载时需要通过网络加载


## 发展进程
  1. 命名空间形式的代码封装 (函数封装)
  2. 通过 立即执行函数(IIFE) 创建的命名空间
  3. 服务器端运行时 Nodejs 的 CommonJS 规范
  4. 将模块化运行在浏览器端的 AMD/CMD 规范
  5. 兼容 AMD 和 CommonJS 的 UMD 规范
  6. 通过语言标准支持的 ES Module

  ```js
  // 命名空间
  function square(x) {
      if(typeof x !== "number") return;
      return x * x;
  }
  var utils = {
      add: function(x, y) {
          return x + y
      }
  }

  // IIFE
  var module = (function(){
      var count = 0;
      return {
          inc: function(){
              count += 1;
          },
          dec: function(){
              count += -1;
          }
      }
  })()
  module.inc();

  // CommonJS

  ```



## CommonJS
> 服务器端的模块化开发规范，主要特点是 同步加载模块，Nodejs 的模块规范就是参照它实现的

<div style="text-indent: 2em">CommonJS 会在启动时把内置模块和加载过的模块放在本地硬盘，文件读取时不受限于网络而等待时间很短，所以在 Node 环境中使用同步加载的方式不会有很大问题。但是 CommonJS 如果应用在浏览器端，同步加载的机制会使得 JS 阻塞 UI 线程，造成页面卡顿，网速不好时还会导致浏览器假死。</div> 


### 模块定义
<div style="text-indent: 2em">为了方便，NodeJs 为每个模块提供一个 exports 变量指向 module.exports，相当于在每个模块头部执行 `var exports = module.exports`。exports 默认是个空对象，可以添加属性和方法但不能被赋值，因为这样会切断了两者联系，而且不能导出需要new实例化的类。使用时建议 module.exports 导出对象和类、exports 导出普通函数和变量</div> 

  * 一个单独的文件就是一个模块
  * 每个模块都是一个单独的作用域
  * 每个模块内部都有一个 module 对象来代表当前模块
  * 暴露模块中的内容可以通过 module.exports、exports 
    

### 模块加载
  * 本质：读取模块内部的 module.exports 变量
  * 特点
    * 缓存结果：模块第一次加载并执行之后就会缓存结果，再次加载时就会直接读取缓存结果，想要再次运行则必须清除缓存
    * 同步加载：模块加载会阻塞接下来代码的执行，需要等到模块加载完成才能继续执行
  * 方法
    * require()：当前模块中读取其它模块并执行
    * require.resolve()：解析模块标识符的绝对路径

### 模块标识
> require() 的参数，require 会根据其格式使用不同的路径加载原则去寻找模块文件

  * 模块名：符合小驼峰命名的字符串
    * 核心模块：系统安装目录
    * 安装模块：node_modules 目录
  * 绝对路径：以 "/" 开头
  * 相对路径：以 "./" 开头


  ```js
  // 导出一个要实例化的类  module.exports = exports = function (){ }

  // rocker.js 
  module.exports = function(name, age) {
      this.name = name;
      this.age = age;
      thisl.about = function() {
          console.log(this.name + 'is' + this.age + 'years old');
      };
  };

  // 引用
  var Rocker = require('./rocker.js')
  var r = new Rocker('Ozzy', 62)
  r.about()   // Ozzy is 62 years old


  // 导出一个静态类 exports.funcName = function (){}

  // hello.js
  function hello() {
      console.log('Hello, world');
  }
  function greet(name) {
      console.log('Hello, ' + name + '!');
  }
  // 写法一
  module.exports = {
      hello: hello,
      greet: greet
  }
  // 写法二
  exports.hello = hello;
  exports.greet = greet;
  // 错误写法
  exports = {
      hello: hello,
      greet: greet
  }

  // 引用
  var hello = require('./hello.js')
  hello.hello()  // Hello, world
  ```


## AMD
> 异步模块定义，浏览器端模块化开发规范。它允许异步和按需加载模块，requireJS 是参照它实现的

### 特点
  * 异步加载方式，模块加载不影响后面语句的运行
    * 实现方式：依赖模块的所有语句都放置在回调函数中并在模块加载完成后执行
    * 解决问题：js 加载时浏览器会停止页面渲染，加载文件越多则页面失去的响应时间越长
  * 管理模块之间的依赖性，便于代码的编写和维护
    * 解决问题：js 文件需要它的被依赖文件早于它加载到浏览器 


### 语法 
  * 模块定义：`define(可选模块标识, 依赖模块数组, 模块初始化要执行的函数或对象)`
  * 模块加载：`require(依赖模块数组, 模块加载成功后的回调函数)`

  ```js
  // 定义模块 myModule.js
  define(['dependency'], function(){
      var name = 'Byron';
      function printName(){
          console.log(name);
      }
      return {
          printName: printName
      };
  });

  // 加载模块
  require(['myModule'], function (my){
  　 my.printName();
  });
  ```


## CMD 
> AMD 基础上改进的一种规范，主要区别是对依赖模块的执行时机处理不同。seaJS 是参照它实现的

### 区别
  * 依赖处理
    * AMD 推崇依赖前置，在定义模块时就要声明其依赖的模块 
    * CMD 推崇就近依赖，只有在用到某个模块时再去 require 
  * 方法    
    * AMD API 默认是一个当多个用，比如模块加载分为全局的和局部的 require
    * CMD 推崇职责单一而且简单，比如模块加载只有 seajs.use() 


### 语法
  ```js
  // 定义模块  index.js
  define(function(require, exports, module) {

      // 使用 exports
      exports.name = 'index';
      exports.hello = function() {
          console.log('Hello index');
      };

      // 使用 module.exports
      module.exports = {
          name: 'index',
          hello: function() {
            console.log('index');
          }
      };
  });

  // 加载模块
  seajs.use('index', function(my) {
      my.hello();
  });
  ```


## UMD
> 兼容 AMD 和 commonJS，提出了跨平台的解决方案

  ```js
  // 无导入导出规范，只有如下的常规写法来检测 JS 环境
  (function (root, factory) {
      if (typeof define === 'function' && define.amd) {
          // AMD
          define(['jquery'], factory);
      } else if (typeof exports === 'object') {
          // CommonJS
          module.exports = factory(require('jquery'));
      } else {
          // 挂载到全局：root 即 window
          root.returnExports = factory(root.jQuery);
      }
  }(this, function ($) {
      // 方法
      function myFunc(){ };
      // 暴露公共方法
      return myFunc;
  }));
  ```


## ES Module
> ES6 的最新语法支持规范

<div style="text-indent: 2em">CommonJS 和 AMD 规范都只能在运行时确定依赖。而 ES6 在语言层面提出了模块化方案, ES6 module 模块编译时就能确定模块的依赖关系，以及输入和输出的变量。ES6 模块化这种加载称为 编译时加载(异步按需加载)、静态加载。</div> 

  * 导入：`import {模块名A，模块名B...} from '模块路径'`
  * 导出：`export、export default`
  * 异步加载：`import('模块路径').then()`

  ```js
  /* 错误写法 */
  // 写法一
  export 1;

  // 写法二
  var m = 1;
  export m;

  // 写法三
  if (x === 2) {
    import MyModual from './myModual';
  }

  /* 正确写法 */
  // 写法一
  export var m = 1;

  // 写法二
  var m = 1;
  export {m};

  // 写法三
  var n = 1;
  export {n as m};

  // 写法四
  var n = 1;
  export default n;

  // 写法五
  if (true) {
      import('./myModule.js')
      .then(({export1, export2}) => {
        // ...·
      });
  }

  // 写法六
  Promise.all([
    import('./module1.js'),
    import('./module2.js'),
    import('./module3.js'),
  ])
  .then(([module1, module2, module3]) => {
    // ···
  });
  ```




