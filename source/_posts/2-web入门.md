---
title: web 开发入门
tags:
  - Skill
categories: Web Skills
top: false
keywords:
  - tool
date: 2019-02-17 15:41:48
description: 前端技能和常用网站、浏览器结构和渲染、渲染方式和框架模式、Restful 规范
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
    * 重排一定重绘
    * 重绘：简单外观的改变就会引起重绘，比如颜色变化等
    * 重排(回流)：重新计算布局，通常由元素的结构、增删、位置、尺寸变化引起，比如 img 下载成功后替换填充页面 img 元素而引起尺寸变化。也会由 js 的属性值读取引起，比如读取 offset、scroll、cilent、getComputedStyle 等信息。


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


# 四、前后端渲染

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



# 五、框架

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



# 六、Restful 
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









