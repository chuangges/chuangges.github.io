---
title: web 入门
tags:
  - Skill
categories: Web Skills
top: false
keywords:
  - tool
date: 2019-02-17 15:41:48
description: 代码编辑的工具配置 和 web 开发的技能要求
---

# 一、电脑系统

## 操作系统

1. __Windows__
    * 人妻，什么都有，还常附带不想要的东西。
    * 微软的系统，基于Basic开发，是最普及的桌面系统，主要特点是功能强大但安全堪忧，适用于正常办公以及游戏。


2. __Mac OS/OSX__
    * 女友，带着有面子但必须按照它的规则。
    * 苹果系统，基于linux开发，主要特点是应用软件较少和较安全，适用于办公的高端移动化处理。


3. __Linux__
    * 小萝莉，你让它干嘛就干嘛。
    * 一种自由和开放源码的类Unix操作系统，可安装在手机电脑等各种计算机硬件设备中，主要特点是网络功能强大且对内存等硬件的消耗小，多用于网络服务器中，优点是其易用性和丰富的应用软件。
          

## 操作接口

1. __终端 terminal__ 
    * 用于人与计算机进行交互的输入输出接口
    * 本质是通过电缆、网络等连接主机的外部附加设备，一台计算机可以连接个终端
    

2. __控制台 console__ 
    * 用于管理主机的特殊终端，只允许管理员使用
    * 本质是直接和计算机相连接的原生设备，一台计算机一般只能有一个控制台


3. __命令解释器 shell__
    * 提供用户界面的程序，用于与计算机内核交互
    * 本质是 接受用户指令后，调用其他程序与内核交互完成指令
    * 一般分为 命令行shell 和 图形shell，分别提供 命令行界面 CLI 和 图形用户界面 GUI
    * 批处理：将一系列命令按照一定顺序集合为一个可执行的文本文件，在window系统的扩展名为 bat/cmd，双击文件即可运行


4. __终端与 shell 的分工__
    * 终端从用户接受 鼠标键盘等设备的输入信息，然后发送给 shell
    * shell 从终端获取用户指令，解析后发给计算机内核执行，并返回结果给终端


# 二、工作站

## mac 工作站

1. __应用软件安装__
    * 通过 App Stroe 安装
    * 将浏览器中下载好的 dmg 安装包拖到"应用程序"目录


2. __系统软件安装__
    * brew 即 Homebrew，是 Mac OSX 上的软件包管理工具，用来安装、更新、卸载软件
    * 命令行配置环境变量：vi ~/.bash_profile - 编辑文件 - esc键退出编辑 - :wq回车保存

3. __实现翻墙__
    * 配置 VPN：VPN 即虚拟专用网络，可用来保护隐私和自由访问外国网站
    * 配置 proxy：即配置代理地址


## chrome 工作站

1. __搜索__（Google输入框中所有空格都被理解为加号）
    * 完整匹配: "mysql foreign key"（引号）
    * 筛选: "mysql key" - "nodejs"（加减号）
    * 返回所有: "mysql connect error *"（加通配符）
    * 站内搜索: "mysql foreign key" site:stackoverflow.com
    * 加速: 输入网址后点击Tab，这样可直接使用该站点的站内搜索


2. __调试js代码__
    * Alert, Console等
    * 断点调试
        * 步骤：F12开发者工具 ——> 点击Sources菜单 ——> 左侧树中找到相应文件 ——> 点击行号列(右键为条件断点) ——> 刷新页面 ——> JS执行到断点位置停住，此时可以跟随鼠标查看功能按钮
        * [相关技巧](http://blog.csdn.net/crper/article/details/50722753) 
    * debug断点
        * 在触发文件中添加 "debugger;" 语句后触发，当代码执行到该语句时就会自动断点, 接下去的操作和在Sources面板添加断点调试几乎一模一样，唯一区别在于调试后需要删除该语句。
        * 由于有时会遇到异步加载html片段的情况，其JS代码在Sources中无法找到，因此无法直接在开发工具中直接添加断点时可用debug断点（F10一步一步执行，F8一下执行完成）


3. __高阶调试功能__
    * [内置抓包工具等](https://www.cnblogs.com/guaidianqiao/p/7615430.html) 
    

4. __扩展插件__
    * 谷歌访问助手
        * 安装：[下载安装包](http://www.ggfwzs.com/) ——> 更多工具 ——> 扩展程序 ——> 直接拖拽 ——> 添加扩展程序
        * 扩展其它插件：谷歌访问助手 ——> 谷歌网上商店 ——> 搜索插件 ——> 点击添加扩展
    * 常用插件推荐
        * 掘金
        * Google翻译         
        * FeHelper 
        * JSONView   
        * What runs         
        * Clear Cache
        * Code Plunker    
        * Vue.js devtools         
        * AngularJS Batarang      
        * React Developer Tools  
        


5. __Chrome黑科技__
    * 实现翻墙
        * 配置proxy代理：setting  system ——> proxy settings ——> LAN setting ——> proxy server ——> 配置 Address 和 Port
        * 配置Chrome扩展：下载[Hoxx VPN Proxy](https://www.crx4chrome.com/crx/39922/) ——> 注册登录

    * 解析VIP视频 
        * 打开网站 [Greasy Fork](https://greasyfork.org/zh-CN) ——> 搜索VIP视频解析 ——> 安装脚本

    * 其它：视频广告过滤、电脑管家上网防护等



# 三、代码编辑器
1. __sublime__
2. __vscode__
    * 基础配置
        ```json
        // 配置中文：ctrl + shift + p --> Display Language
            "locale":"zh-CN"

        // 用户配置：文件 --> 首选项 --> 设置 --> User Settings
            // 指定工作台中使用的颜色主题。    
            "workbench.colorTheme": "Monokai",    
            // 指定在工作台中使用的图标主题    
            "workbench.iconTheme": "vscode-icons",    
            // 窗口失去焦点自动保存    
            "files.autoSave": "off",    
            // 如果设置为 "true"，将不再显示扩展建议的通知。   
            "extensions.ignoreRecommendations": true,    
            // 如果设置成 true，关于新的版本消息将不再显示    
            "vsicons.dontShowNewVersionMessage": true,    
            // 控制是否将打开的编辑器显示为预览。    
            "workbench.editor.enablePreview": false,   
            // 字体字号
            "editor.fontSize": 18, 
            //代码缩进风格4个字符    
            "editor.tabSize": 4,    
            // 控制编辑器是否应在键入后自动设置行的格式    
            "editor.formatOnType": false,    
            // 保存时取消自动格式化    
            "editor.formatOnSave": false,    
            // 编辑粘贴取消自动格式化    
            "editor.formatOnPaste": false,    
            // 控制编辑器中呈现空白字符的方式为“边界”，不会在单词之间呈现单空格。    
            "editor.renderWhitespace": "boundary",    
            // 控制光标动画样式    
            "editor.cursorBlinking": "smooth",    
            //设置Eslint需要验证的语言    
            "eslint.validate": [        
                "javascript",        
                "javascriptreact",        
                "html",        
                "vue",        
                {            
                    "language": "vue",            
                    "autoFix": true        
                }
            ],
            //每列显示内容长多，超出时控制编辑器列的换行。
            "editor.wordWrap": "wordWrapColumn",
            "editor.wordWrapColumn": 150,
            //创建人，修改人    
            "fileheader.Author": "klierbyck",    
            "fileheader.LastModifiedBy": "klierbyck",    
            // 在函数参数括号前定义空格处理。需要 TypeScript >= 2.1.5。    
            "typescript.format.insertSpaceBeforeFunctionParenthesis": true,    
            // 在函数参数括号前定义空格处理。需要 TypeScript >= 2.1.5。    
            "javascript.format.insertSpaceBeforeFunctionParenthesis": true,
            // HTML Snippets 插件配置
            "emmet.triggerExpansionOnTab": true,
            "emmet.includeLanguages": {
                "vue-html": "html",
                "vue": "html"
            },
        ```
    * 常用扩展插件
        * 显示效果类
            * Dracula Official ：高亮主题
            * Beautify ：代码高亮
            * vscode-icon ：让资源树目录加上图标
            * Bracket Pair Colorizer ：每一对括号用不同颜色区别
            * Open-In-Browser ：直接在浏览器中打开文件的快捷菜单 alt + b
        * 辅助编辑类
            * ESlint ：检测js
            * Prettier ：代码格式化 alt + shift + F 
            * CSS Peek ：追踪至定义处（右键选择前两个选项）
            * Path Intellisense ：自动补全路径
            * Auto Close Tag ：自动闭合HTML标签
            * Auto Rename Tag ：自动修改匹配的标签
            * HTML CSS Support ：CSS的智能补全
            * JavaScript (ES6) code snippets ：JS语法提示
        * Vue插件类
            * Vetur ：语法高亮
            * VueHelper ：vue代码提示插件
            * Vue 2 Snippets ：语法高亮 + 代码补全
            * HTML Snippets ：在.vue文件中使用html代码补全功能
        *  其它类
            * npm ：运行npm命令
            * npm Intellisense ：导入时提示已安装模块
            * Python ：添加对.py文件的支持
            * GitLens ：简单实现git提交代码
            * Debugger for chrome ：调试Debug
      

# 四、程序员网站       
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



# 五、web 开发

> 呈现用户可理解的界面并响应用户操作。

## 框架

### 框架和库
* 类库：一个有组织的功能集合，用于提供特定功能的接口并被调用
* 框架：构建应用程序的整体架构，使开发者可以专注于逻辑处理而提高开发效率
* 联系：框架一般会集成大量库并在合适地方调用，但有时也可称之为库，两者区别只在于思考角度


### 框架模式

1. __MVC__
* 实现思路：所有的终端用户请求被发送到 Controller，Controller 依赖请求去选择加载哪个模型并附加到对应 View，最终 View 作为响应发送给终端用户。
* 缺陷：包含大量的业务逻辑后，代码就会难以阅读和维护。

<div align="center">
![MVC架构图](/images/frame/mvc.png)
<!-- <img src="/images/post/technology.png" alt=""> -->
</div> 

2. __MVP__
* 实现思路：切断的 View 和 Model 的联系, 让 View 只和 Presenter（原Controller）交互，减少在需求变化中需要维护的对象的数量，相比 MVC 成本更低而且更容易理解。
* 缺陷：最接近用户的界面是需要根据需求变化而频繁更改的。

<div align="center">
![MVP架构图](/images/frame/mvp.png)
<!-- <img src="/images/frame/mvp.png" alt=""> -->
</div> 

3. __MTV__
* 实现思路：Model 负责业务对象和数据库的关系映射(ORM)，Template 负责如何把页面展示给用户(html)，View 负责业务逻辑并在适当时候调用 Model和Template。此外，通过一个URL分发器将一个个URL的页面请求分发给不同的View处理，View再调用相应的Model和Template。

<div align="center">
![MTV架构图](/images/frame/mtv.png)
<!-- <img src="/images/frame/mtv.png" alt=""> -->
</div> 


4. __MVVM__
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


### 框架分类
* 可视化组件：Echarts
* UI框架：Bootstrap、EasyUI、ElementUI
* JS框架：jQuery、Zepto.js、NodeJs、requirejs、VueJs、angularJS、reactJS


## 常用规范
1. __命名__
    * 统一使用小写英文字母、数字和下划线的组合，而且要语义化
    * 标签命名时应该考虑代码的可维护性，注意id名字须保证唯一性

2. __js注释__
    ```js
    // 单行注释

    /*
    多行注释
    */

    /**
    * ---------------------------------------------------
    * 模块描述说明
    * ---------------------------------------------------
    */

    /**
    * 模块内的小函数方法归类
    * ---------------------------------------------------
    */

    /**
    * 单个函数功能简述
    *
    * 具体描述一些细节
    *
    * @param    {string}  address     地址
    * @param    {array}   com         商品数组
    * @param    {string}  pay_status  支付方式
    * @returns  void
    *
    * @date     2019-02-12
    * @author   QETHAN<qinbinyang@zuijiao.net>
    */

    ```


## Restful
<div style="text-indent: 2em">RESTful 是一种架构的规范与约束、原则而并非架构或标准，符合这种规范的架构就是 RESTful架构。基于这个风格设计的软件可以更简洁，更有层次，更容易实现缓存等机制，常用于客户端和服务器交互类的软件。</div>

### 架构
* RESTful架构：面向资源的架构，即针对资源设计接口。客户端操作通过资源对应的 URL 进行操作，从而访问服务器端资源。
* SOA架构：面向服务的体系结构。SOA将不同的功能组件视为一种服务并分别封装，降低了组件之间的耦合程度，增加了代码的复用程度。


### 特点
* 资源；作为资源的标识，比如 html / jpg / json
* URI：统一资源定位符，即每个 URI 对应一个特定的资源
* 无状态：所有资源都可以通过 URI 去定位，而不与其他资源产生耦合
* 统一接口：数据元操作CURD 分别对应http的 get、post、put、delete

### URI 

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



## 技术栈
<div align="center">
![前端技能图](/images/post/technology.png)
<!-- <img src="/images/post/technology.png" alt=""> -->
</div> 





