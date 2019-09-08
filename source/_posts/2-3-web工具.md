---
title: web 开发工具
tags:
  - 项目开发
categories: 项目开发
top: false
keywords:
  - 开发工具
date: 2019-02-20 15:41:48
description: 电脑、浏览器、代码编辑工具 vscode、代码调试工具 Postman、前端构建工具 (Npm、Gulp、WebPack)、版本管理工具 Git
---

# 一、电脑系统 Mac

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


## mac 
  1. __应用软件安装__
    * 通过 App Stroe 安装
    * 将浏览器中下载好的 dmg 安装包拖到"应用程序"目录
  2. __系统软件安装__
    * brew 即 Homebrew，是 Mac OSX 上的软件包管理工具，用来安装、更新、卸载软件
    * 命令行配置环境变量：vi ~/.bash_profile - 编辑文件 - esc键退出编辑 - :wq回车保存
  3. __实现翻墙__
    * 配置 VPN：VPN 即虚拟专用网络，可用来保护隐私和自由访问外国网站
    * 配置 proxy：即配置代理地址


## TeamViewer
> 远程控制

  1. 安装注册：https://www.teamviewer.cn/cn/download/mac-os/
  2. 登陆办公室、家中电脑上的 TeamViewer
  3. 伙伴ID处输入办公室电脑的 ID，点击连接
  4. 设置无人值守，然后电脑重启后的名称和密码不再改变


# 二、浏览器 chrome 
  1. __搜索__（Google输入框中所有空格都被理解为加号）
    * 完整匹配: `mysql foreign key`（引号）
    * 筛选: `mysql key - nodejs`（加减号）
    * 返回所有: `mysql connect error *`（加通配符）
    * 站内搜索: `mysql foreign key site:stackoverflow.com`
    * 加速: 输入网址后点击 Tab，这样可直接使用该站点的站内搜索
  2. __调试js代码__
    * Alert, Console等
    * 断点调试
      * 步骤：F12 开发者工具 ——> 点击Sources菜单 ——> 左侧树中找到相应文件 ——> 点击行号列(右键为条件断点) ——> 刷新页面 ——> JS执行到断点位置停住，此时可以跟随鼠标查看功能按钮
      * [相关技巧](http://blog.csdn.net/crper/article/details/50722753) 
    * debug 断点
      * 在触发文件中添加 `debugger;` 语句后触发，当代码执行到该语句时就会自动断点, 接下去的操作和在Sources面板添加断点调试几乎一模一样，唯一区别在于调试后需要删除该语句。
      * 由于有时会遇到异步加载html片段的情况，其JS代码在Sources中无法找到，因此无法直接在开发工具中直接添加断点时可用debug断点（F10一步一步执行，F8一下执行完成）
  3. __高阶调试功能__
    * [内置抓包工具等](https://www.cnblogs.com/guaidianqiao/p/7615430.html) 
  4. __扩展插件__
    * 谷歌访问助手
      * 安装：[下载安装包](http://www.ggfwzs.com/) —> 更多工具 —> 扩展程序 —> 直接拖拽 ——> 添加扩展
      * 扩展其它插件：谷歌访问助手 —> 谷歌网上商店 —> 搜索插件 —> 添加扩展
    * 常用插件推荐
      * 掘金
      * Google 翻译         
      * FeHelper 
      * JSONView   
      * What runs         
      * Clear Cache
      * Code Plunker    
      * Vue.js devtools         
      * AngularJS Batarang      
      * React Developer Tools  
  5. __实用黑科技__
    * __实现翻墙__
      * 配置 proxy 代理：setting system —> proxy settings —> LAN setting —> proxy server —> 配置 Address、Port
      * 配置 Chrome 扩展：下载 [Hoxx VPN Proxy](https://www.crx4chrome.com/crx/39922/) —> 注册登录
    * __解析 VIP 视频__
      * 打开网站 [Greasy Fork](https://greasyfork.org/zh-CN) —> 搜索VIP视频解析 —> 安装脚本
    * 其它：视频广告过滤、电脑管家上网防护等



# 三、代码工具

## 代码编辑 vscode
> 常用扩展插件如下：

  * 显示效果类
    * __Beautify__：代码高亮
    * __Dracula Official__：高亮主题
    * __vscode-icon__：让资源树目录加上图标
    * __Bracket Pair Colorizer__：每一对括号用不同颜色区别
    * __Open-In-Browser__：直接在浏览器中打开文件的快捷菜单 `alt + b`
  * 辅助编辑类
    * __ESlint__：检测js
    * __Prettier__：代码格式化 `alt + shift + F`
    * __CSS Peek__：追踪至定义处（右键选择前两个选项）
    * __Path Intellisense__：自动补全路径
    * __Auto Close Tag__：自动闭合 HTML 标签
    * __Auto Rename Tag__：自动修改匹配的标签
    * __HTML CSS Support__：CSS 的智能补全
    * __JavaScript (ES6) code snippets__：JS语法提示
  * Vue 插件类
    * __Vetur__：语法高亮
    * __VueHelper__：vue 代码提示插件
    * __Vue 2 Snippets__：语法高亮、代码补全
    * __HTML Snippets__：在.vue文件中使用html代码补全功能
  * 小程序插件类
    * __minapp__：标签、属性的智能补全
    * __wechat-snippet__：代码辅助
    * __wxml__：高亮显示、代码格式化
  *  其它类
    * __npm__：运行 npm 命令
    * __npm Intellisense__：导入时提示已安装模块
    * __Python__：添加对 py 文件的支持
    * __GitLens__：简单实现 git 提交代码
    * __Debugger for chrome__：调试 Debug
    

  ```js
  // 用户配置：文件 --> 首选项 --> 设置 --> User Settings

  // 解决 vscode 卡顿
  search.followSymlinks: false,
  git.enabled: false,
  git.autorefresh: false,
 
  // 窗口失去焦点自动保存    
  files.autoSave: "onFocusChange",    
  // 不再显示扩展建议的通知 
  extensions.ignoreRecommendations: true,    
  // 不再显示新版本消息    
  vsicons.dontShowNewVersionMessage: true,  
  // 是否将打开的编辑器显示为预览 
  workbench.editor.enablePreview: false,   
  // 字体字号
  editor.fontSize: 16, 
  // 代码缩进   
  editor.tabSize: 4,    
  // 是否自动设置键入后行的格式    
  editor.formatOnType: false,    
  // 保存时取消自动格式化    
  editor.formatOnSave: false,    
  // 编辑粘贴取消自动格式化    
  editor.formatOnPaste: false,  
  //设置 Eslint 验证的语言    
  eslint.validate: [        
      "javascript",        
      "javascriptreact",        
      "html",        
      "vue",        
      {            
        "language": "vue",            
        "autoFix": true        
      }
  ],
  // HTML Snippets 插件配置
  emmet.triggerExpansionOnTab: true,
  emmet.includeLanguages: {
      "vue-html": "html",
      "vue": "html"
  },
  ```
      

## 接口调试 Postman

### 基础功能
  <div align="center">
    ![Postman](/images/web/postman.png)
  </div> 


### 接口请求
  * GET 请求
    * 点击 Params，输入 key、value
    * 请求头与请求参数可以不填
  * POST请求
    * 参数值直接输入
    * 设置参数格式： Content-Type
      * `form-data`：即 multipart/form-data，它将表单的数据转换为 Key-Value，可以上传文件或数据
      * `x-www-form-urlencoded`：即 application/x-www-from-urlencoded，将表单内的数据转换为 Key-Value
      * `raw`：可以上传 text、json、xml、html 等任意格式的文本
      * `binary`：即 Content-Type:application/octet-stream，只能上传二进制文件，一次上传一个文件


### 管理用例 Collections
> Collections 集合就是将多个接口请求放在一起并管理起来，一个工程创建一个 Collection 方便查找及统一处理数据

  1. 创建 Collections：点击加号图标并输入内容
  2. 添加请求：可以通过 Add Folder 针对不同的请求方式做分组 


### 身份验证 Authentication
  * `Basic Auth`：基础验证，会直接将用户名、密码的信息放在请求的 Header
  * `Digest Auth`：使用当前填写的值生成 authorization header，如果 header 已经存在则会被移除
  * `OAuth 1.0`：基于身份验证的请求，不用获取 access token、可以在 header 或者查询参数中设置 value
  * `OAuth 2.0`：支持获得 token 并添加到 request



# 四、前端自动化构建

## 构建需求

  1. __预处理__
    <div style="text-indent: 2em">低层语言的更换或升级都因兼容性问题而面临着巨大困难，这催生了各种中间语言的预处理器，例如 通过 Babel 将 ES6 转换成可以在浏览器中运行的 js代码，通过 Sass/Less/Stylus 可以在线编程并转换成 css代码。</div> 

  2. __风格与测试__
    <div style="text-indent: 2em">在一个典型的工作流中，每次Push主分支或npm发布都应首先运行代码风格检查和单元测试。我们需要这些操作能够在合适的时候自动执行。</div> 

  3. __资源压缩__
    <div style="text-indent: 2em">在开发网站代码时，我们希望模块化地进行编码，即每个业务逻辑、通用工具或者架构元素都需要组织在单独的文件中。但是如果用户浏览网页时也载入这么多源文件则会影响页面打开速度，因此在网站发布时需要将源码合并压缩，js可能还需要模块化，CSS文件可能还需要合并、添加兼容性前缀等，这些重复性工作我们也希望写成脚本。   </div> 

  4. __静态资源的URL替换__
    <div style="text-indent: 2em">该需求最复杂, 因为生产环境中的资源地址可能和开发环境中很不同，可能是由于JS合并、CSS合并，也可能是由于应用了CDN加速。我们需要在部署时更改所有HTML文件中的静态资源地址。</div> 


## 构建工具
<div style="text-indent: 2em">"自动化构建"是指通过工具实现构建系统、编译和转换代码、压缩资源、部署配置等功能，优化"从源码到网页"的开发流程，这有利于提高开发效率并改善代码质量。</div>
<div style="text-indent: 2em">所有的开发工具都是为了使大量低技术含量任务完成自动化从而减轻工作，它们的组合使用完全看个人选择。开发过程一般会以Node和npm为核心，然后搭配 Gulp + Bower 或者 Webpack。</div>

  1. __执行通用任务__
    * NPM / Bower：基于NodeJS的包管理和分发工具，用来下载、安装、上传以及管理已经安装的包
    * Gulp / Grunt：基于NodeJS的自动化构建工具，用来自动化完成一些常见的、重复的Web开发任务，如网页自动刷新和预处理等。两者最大区别是Gulp采取流式接口。
  2. __模块绑定__
    * Webpack：前端资源模块化管理和打包工具，用来配置各种模块所需要的处理方式，常见配置有 scss 代码预处理、ES6 代码转换、模块打包的规则和地址等
    * Browserify：打包工具，常用于将 Node 项目模块打包成浏览器支持形式
    * RequireJS：Js模块加载器，常用于 NodeJS 中加载模块
  3. __代码分析__
    * ESLint / JSLint：检测工具，常用于分析代码中潜在错误。
  4. __单元测试__
    * Jasmine：测试工具，常用于测试自己的代码


  
# 五、安装工具 NPM
## 使用场景
  * 从NPM服务器下载别人编写的三方包到本地使用
  * 从NPM服务器下载并安装别人编写的命令行程序到本地使用
  * 将自己编写的包或命令行程序上传到NPM服务器供别人使用


## 常用命令
  ```js
  npm -v                    //查看npm版本
  npm list                  //当前目录已安装插件
  npm init                  //生成package.json文件

  npm i <name> -g           //安装包
  npm uninstall <name>      //卸载包
  npm update <name>         //更新包   
  /**
    * 可添加的修饰符
    *     -g                 全局(不加则为本地安装)
    *     -S / --save        需要发布到生产环境的依赖(默认)
    *     -D / --save-dev    只用于开发环境的依赖
    *     --no-save          不写入package.json的依赖
    */

  npm run script-key        // 执行package.json中 "scripts" 选项对应的js 
  npm start                 // 比较特殊
  ```

## 发布npm包

  ```js
  cd test                 // 选择目标文件夹
  npm init                // 配置包的相关信息
  module.exports = 1      // 编辑包 test/index.js

  npm adduser             // 注册，第一次发布包
  npm login               // 登录，非第一次发布包

  npm publish            // 发布/更新包
  npm install 包名        // npm官网搜索后验证下载

  // 撤销发布的包要用force强制删除。超过24小时就不能删除了
  npm --force unpublish 包名    
  ```


# 六、构建工具 Gulp

## 常用插件
  * gulp-minify-html：压缩html
  * gulp-minify-css：压缩css
  * gulp-uglify：压缩js
  * gulp-smushit：压缩图片
  * gulp-concat：文件合并
  * gulp-rename：重命名
  * gulp-babel：编译ES6
  * gulp-sass/less：css预处理
  * gulp-autoprefixer：自动添加css前缀
  * browser-sync：自动刷新页面(非gulp插件)


## API
  * task：创建任务
  * run：运行任务
  * watch：监听任务
  * src：设置需处理的文件路径(正则/数组)
  * dest：设置生成文件的路径，一个任务可有多个生成路径


## 设置任务
  1. __服务器__
    ```js
    var gulp = require('gulp'),
        browserSync = require('browser-sync');
    // 1.搭建静态服务器
    gulp.task('browser-sync', function () {
        browserSync.init({
            files:['**'],
            server:{
                baseDir:'./',             // 服务器的根目录
                index:'blink/blink.html'  // 默认打开的文件
            },
            port:8050  // 指定访问服务器的端口号
        });
    });
    // 2.使用代理（注意本地服务器必须自己搭建的）
    gulp.task('browser-sync', function () {
        browserSync.init({
            files:['**'],
            proxy:'localhost',  // 本地服务器的地址
            port:8080           // 访问的端口号
        });
    });
    ```
  
  2. __默认任务__
    ```js
    gulp.task('default',['serve']);  
    gulp.task('server',['sass','mihtml','minijs'],function(){
        bs.init({
            open:'external',
            server:'./build'  //服务根目录
        })
        // 监听html
        gulp.watch('./html/*.html',['mihtml'])
        gulp.watch('./build/html/*.html').on('change',reload)
    }) 
      

    gulp.task('default', function(){
        gulp.run('lint', 'sass', 'scripts');
          // 监听sass文件变化
        gulp.watch('src/sass/*.sass', function(){
            gulp.run('lint', 'sass', 'scripts');
        });
    });
    ```

## 基础使用
  1. __初始化目录__
    ```js
    ├── app              // 工作目录：源文件
    │   ├── js
    │   ├── Sass
    │   └── index.html
    │
    ├── dist            // 目标目录：编译、压缩后的文件
    └── release         // 可以发布到服务器的文件目录


    cnpm install gulp -g  // 全局安装，执行gulp任务
    cd GulpTest 
    cnpm init      // 生成package.json文件
    cnpm install gulp -S  // 本地安装，使用gulp插件并避免版本冲突
    gulp -v       // 查看版本
    ```
  
  2. __编写配置文件 gulpfile.js__
    ```js
    var gulp = require('gulp');

    var sass = require('gulp-sass');        //编译sass
    var maps = require('gulp-sourcemaps');  //在浏览器显示scss样式以便调试
    var autoprefixer = require('gulp-autoprefixer');  //自动添加前后缀

    var htmlmin = require('gulp-htmlmin');  //html压缩
    var uglify = require('gulp-uglify');    //压缩js文件
    var smushit = require('gulp-smushit');  //图片压缩

    var concat = require('gulp-concat');    //文件合并
    var babel = require('gulp-babel');      //将ES6代码编译成ES5

    var bs = require('browser-sync').create();  //页面同步
    var reload = bs.reload;


    // 默认任务
    gulp.task('default', ['server'])

    // 打包任务
    // gulp.task('build', ['html', 'css', 'less', 'js'])

    // sass 任务
    gulp.task('sass', function(){
      return gulp.src('./src/scss/*.scss')  // 导入文件
        .pipe(maps.init())        // 将页面样式定义到scss
          // 转译scss到css,报错时输出错误信息,不终止程序运行 
        .pipe(sass({"outputStyle": "compressed"}).on('error', sass.logError))
                                    
        .pipe(autoprefixer())       // 自动添加前后缀
        .pipe(maps.write('./'))     // 生成map文件（和css文件放在一起）
        .pipe(gulp.dest('./dist/css'))    // 将生成的文件放到css文件夹中
        .pipe(reload({stream:true}))       
        // 任务完成后（监听关闭后），再有修改时重新启动监听时会重新执行以防报错
    })

    // 压缩 html 
    gulp.task('mihtml', function(){
      var options = {
            removeComments: true,     // 清除HTML注释
            collapseWhitespace: true, // 压缩HTML
            minfyJS: true,     // 压缩JS
            minfyCss: true,    // 压缩CSS
        };
        return gulp.src('./src/html/*.html')  
            .pipe(htmlmin(options))  
            .pipe(gulp.dest('./dist/html/'))
    })

    // 压缩 js 
    gulp.task('minijs', function() {   
        gulp.src('!./src/js/*.js')       // !表示除了...之外 
        .pipe(babel())
        .pipe(uglify())//压缩  
        // .pipe(concat('all.min.js'))   //合并到all.min.js中  
        .pipe(gulp.dest('./dist/js/'));  //指定目录  
    }); 

    // 压缩 img 
    gulp.task('img', function () {
        return gulp.src('./src/img/*.*')
            .pipe(smushit({
                verbose: true
            }))
            .pipe(gulp.dest('./dist/img/'));
    });
    //生成服务并自动化监听项目
    gulp.task('server', ['sass','mihtml','minijs'], function(){
        bs.init({
          open: 'external',  //启动时自动打开的网址(local打开本地主机网址)
          server: './dist/html' //设置根目录dist(默认打开其中index.html)
          /*
            默认打开 test.html
            baseDir: "./",
            index: "test.html"
          */
        })
        // 监听html
        gulp.watch('./src/html/*.html', ['mihtml'])
        gulp.watch('./dist/html/*.html').on('change', reload)
        //监听scss
        gulp.watch('./src/scss/*.scss',['sass']);
        gulp.watch('./dist/css/*.css').on('change', reload)
        //监听js
        gulp.watch('./src/js/*.js', ['minijs'])
        gulp.watch('./dist/js/*js').on('change', reload)
    })
    ```

  3. __运行gulp__
    ```json
    // 定义 package.json 文件中的 scripts
    "scripts": {
      "test": "echo \"Error: no test specified\" && exit 1",
      "start": "gulp dev",                   //执行gulp dev命令
      "clean": "rimraf dist",                // 清空删除dist中所有文件
      "build": "rimraf dist && gulp build"   // 执行两个命令，先清空dist再重新打包
    },
    //注意先全局安装rimraf: npm install rimraf -g
    ```


# 七、打包工具 Webpack

## 模块打包工具
<div style="text-indent: 2em">分析项目结构，找到Js模块和浏览器不能直接运行的拓展语言（Scss、TypeScript 等），并将其转换和打包为合适的格式供浏览器使用。在很多场景下可以替代Gulp/Grunt类的工具，工作流程如下：</div>  

  * Gulp/Grunt：在一个配置文件中，指定对某些文件进行编译、组合、压缩等任务的具体步骤，工具会自动替你完成这些任务。
  * Webpack：把项目当做一个整体，从一个给定主文件(如index.js)开始找到项目的所有依赖文件，使用loaders处理它们，把所有的非js资源(css/img等)都转换成 js，然后用 CommonJS 的机制管理起来。


## 核心概念
  * 入口（entry）：即要编译的文件。指定后webpack会自行寻找依赖的文件打包编译
  * 输出（output）：编译转换好之后的文件写入位置
  * 加载器（loader）：转换scss、ES6等不同类型的文件，从而可以在浏览器中运行
  * 插件（plugin）：完成压缩文件等loader无法解决的功能


## 基础使用
  1. __初始化目录__
    ```js
    WebTest
      ├── app              // 工作目录：源文件
      │   ├── main.js
      │   └── hello.js
      │
      ├── dist            // 目标目录：打包生成的 js 文件
      │   └── index.html
      │
      ├── node_moudles
      └── package.json 

    // index.html
    div id="app"         
    script src="bundle.js"

    // hello.js
    moudle.exports = "hello world"

    // main.js
    const str = require("./hello.js")
    document.querySelector("#app").appendChild(str)

    // 打包执行
    webpack app/main.js dist/bundle.js


    cd WebTest                          
    cnpm install -g webpack   // 全局安装
    cnpm install -S webpack   // 本地安装
    cnpm init                 // 新建package.json
  ```

  2. __基础配置__
  <div style="text-indent: 2em">在项目根目录下新建 webpack.config.js 文件并配置选项，配置后在终端里执行 webpack 就会被自动引用。项目开发时可以创建多个配置文件：webpack.dev.config.js 和 webpack.pub.config.js 分别用于开发和生产环境，不过运行时需要使用命令 webpack --config webpack.dev.config.js</div>  

  ```js
  // webpack.config.js
  module.exports = {
    entry: __dirname + '/app/main.js', // 唯一入口文件
    output: {
        filename: 'bundle.js',         // 输出文件名
        path: __dirname + "/dist",     // 输出文件存放地址
        // path: path.resolve(__dirname, 'dist') 
      }
  }
  ```

  ```json
  // package.json文件中的scripts选项配置快捷执行方法
  "scripts": {
    "dev":"webpack --config webpack.dev.config.js",
    "pub":"webpack --config webpack.pub.config.js",
    "start": "webpack",
    "server": "webpack-dev-server --inline" 
  }
  ```


## 高级配置

### 配置 Babel
  * 功能：将 ES6、jsx等语法编译为浏览器能够运行的js
  * 配置：webpack.config.js 或单独的 .babelrc 文件（webpack会自动调用）

  
### 生成 Source Maps
<div style="text-indent: 2em">有时打包后文件出错了，却不容易找到对应代码的位置，而通过配置 devtool 选项后，webpack 就可以在打包时生成 source maps，这为我们提供了一种对应编译文件和源文件的方法，使得编译后的代码可读性更高，也更容易调试。可选的四种配置如下：</div>  

  * source-map：完整且功能完全的单独文件，但会减慢打包速度
  * cheap-module-source-map：不带列映射，只能对应到具体的行
  * eval-source-map：不影响速度且完整，但有隐患，可开发不可生产阶段
  * cheap-module-eval-source-map：生成Source Map最快且和js同行，不利于调试，推荐在大型项目考虑时间成本时使用


### 构建本地服务器
<div style="text-indent: 2em">Webpack 提供一个可选的本地开发服务器 webpack-dev-server，它基于 Node.js 构建而且可以实现浏览器监听代码修改并自动刷新等效果，配置前需要通过 npm 安装它作为项目依赖。</div> 

```js
module.exports = {
    entry: __dirname + '/app/main.js',
    output: {
        path: path.resolve(__dirname, 'dist'),
        filename: 'bundle.js'
    },
    devServer: {
        contentBase: "./dist",    // 加载页面的所在目录
        historyApiFallback: true, // 不跳转
        inline: true,   // 实时刷新
        hot: true  // 热更新，即不刷新浏览器的前提下刷新页面
    }
}

// 配置后执行 npm run server 
```



# 八、版本管理工具 Git

## Git 和 Svn
  1. Svn：集中式管理工具。所有版本文件都集中存放到一个服务器上，所有开发者都从该服务器上更新或上传修改代码，注意需要连网。
  2. Git：分布式管理工具，分布式指每台电脑都是一个版本仓库，但在实际工作中一般会有一个集中的服务中心 Github。与集中式的最大区别在于开发者可以离线在本地提交, 只需在连网时push到相应的服务器或其他用户, 而且每个开发者通过克隆(git clone)可以在本地机器上拷贝一个完整的Git仓库。</div>


## Git 和 Github的关系
  1. Git：指定了remote链接和用户信息（用户名+邮箱）之后，git可以帮你将提交过到你本地分支的代码push到远程的git仓库（任意提供了git托管服务的服务器上都可以，包括你自己建的服务器 或者 GitHub等网站提供的服务器）或者将远程仓库的代码 fetch 到本地。</div>
  2. Github：只是一个提供存储空间的服务器，用来存储git仓库。当然现在Github已经由一个存放git仓库的网站空间发展为了一个开源社区（不只具有存储git仓库的功能了），你可以参与别人的开源项目，也可以让别人参与你的开源项目。</div>


## 工作流程
<div style="text-indent: 2em">开发者在自己的机器上更新服务器上的最新代码，然后根据需要创建分支，在该分支上提交本地修改的代码到远程仓库并通知主开发者合并代码。如果主开发者发现代码有冲突则让开发者修改后重新提交，没有冲突则合并代码。注意本地的 .git文件是指本地仓库，.git目录下存放着所有文件的版本和关联信息但默认隐藏。开发流程图如下</div>
<div align="center">
  ![工作流程图](/images/web/git_work.png)
</div> 

## 常用命令
<div align="center">
<!-- git_order.png -->
![常用命令图](/images/web/git_order.png)
</div> 

## 基础配置
```js
git config --global --list         //查看全局配置
git config --global user.name "chuanggefighting"    //设置用户名
git config --global user.email "17621538916@163.com"    //设置邮箱
```

## ssh密钥配置
<div style="text-indent: 2em">Git使用https协议，每次pull/push都要输入密码比较麻烦，而使用git协议后使用ssh密钥，这样可以省去每次都输密码。配置步骤如下：</div>

  1. 检测SSH keys是否存在：`cd ~/.ssh`
  2. 创建ssh key(生成密钥对)：`ssh-keygen -t rsa -C "17621538916@163.com"`
    * 此时会提示自定义名称和push时的密码 (不是git登录密码), 一般推荐略过(直接三个回车), 如果看到成功保存信息则说明如果创建成功

  3. 添加公钥到github等个人的远程仓库
    1. 查看生成的公钥：`cat ~/.ssh/id_rsa.pub` 
    2. 登陆github：点击头像 --> Settings --> 左栏点击SSH and GPG keys --> 点击 New SSH key --> 复制上面的公钥内容，粘贴进"Key"，title自定义 --> 点击 Add key 
    3. 测试SSH的连接：`ssh -T git@github.com`  
    4. 输入yes后可看到信息
              

## 工作提交代码
  ```js
  /**
    *  远程分支   -->  本地分支
    *  orgin/master --> master
    *  orgin/dev/t_lishi/xx --> dev/t_lishi/xx
    */

  git fetch --all        //更新远程修改但不会merge
  git reset --hard orgin/master  //强制更新(慎用)

  git pull orgin master  // 拉取代码到本地并合并到本地


  git branch            // 查看本地分支
  git checkout master   //切换到主分支分支

  git branch -D BranchName    // 删除本地分支(--delete/-d)
  git branch -r -D origin/BranchName  // 删除远程分支
  git push origin :BranchName   // 删除远程分支简单方法

  git checkout -b dev/t_lishi/Navigation  // 创建并切换到该分支          
  // 相当于以下两个命令的合并
  git branch branchName     新建    
  git checkout branchName   切换

  git push origin dev/t_lishi/Navigation  // 推送到远端
  git branch -a      // 查看远程分支

  // 修改某个文件后操作
  git status  查看状态
  git add TaskDoc/t_lishi.log  // 指定需要提交到本地仓库的文件
  git status 
  git commit -m "create Navigation"      // 提交代码到本地
  git push origin dev/t_lishi/Navigation    // 推送到远程仓库


  // 下载远程代码
  git clone url                  // 拉取远程主分支代码到本地
  git clone -b my-branch url     // 拉取指定分支代码到本地
  git remote -v                  // 查询当前远程的版本

  // 从远程仓库获取最新代码到本地当前分支
  git pull
  git pull origin master
  git pull origin dev

  // 从远程仓库里拉取一条本地不存在的分支到本地
  git stash                 // 保存所做的更改
  git checkout -b 本地分支名 origin/远程分支名  // 拉取远程分支
  git stash pop             // 将所做的更改移到当前分支上

  // 提交代码
  git add .
  git commint -m ""
  git push
  ```


## 把个人网页挂到 Github
  1. __Github 上建立仓库，比如 resume__
  2. __进入新建仓库并选择 “settings” 进行仓库设置，滚动到页面底部 “Github Pages” 部分__
    * Source -- master branch，保存后就会自动生成网址
    * 生成网址拼接文件路径后访问：chuanggefighting.github.io/resume/dist/index.html
  3. __将仓库代码克隆到本地__
    * 删除除了 .git 以外的其他文件，然后通过以下命令测试
    ```js
    git add *               // 添加到暂存区
    git commit -m 'del'     // 上传到本地仓库
    git push origin master  // 上传到远程仓库
    ```
  4. __更新个人代码到远程仓库__










