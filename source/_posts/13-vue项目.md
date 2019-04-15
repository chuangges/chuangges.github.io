---
title: Vue 基础及其项目构建
tags:
  - Vue.js
categories: Vue.js
top: false
keywords:
  - vue
date: 2019-04-12 00:32:55
description: Vue 框架、SPA、SSR、模块引用、基础配置、打包问题、静态服务
---

# 一、vue 框架

  <div style="text-indent: 2em">Vue 是一套构建用户界面的渐进式 js 框架，它不是一个全能框架而只聚焦于视图层，容易学习和与其它库或已有项目整合。Vue 框架是一个轻量高效的前端组件化方案，它通过简洁的 API 提供高效的数据驱动的组件系统，通过操作数据来实现页面的更新与展示。</div> 

  * 核心思想：`数据驱动的组件系统`
  * 开发理念：`渐进式开发、只关注视图层、组件化、响应式`
    * 渐进式开发：先构建基础的用户界面，然后再根据需求灵活添加功能
    * 响应式：一个网页，根据不同的分辨率改变网页大小
    * 适应式：多个不同的网页，根据不同的分辨率自动选择不同页面
  * [开源项目库](https://segmentfault.com/p/1210000008583242/read?from=timeline)


## MVVM 模式
  * __Model__：对应 js 对象的数据部分
  * __View__：对应 DOM 元素的视图部分
  * __Viewmodel__：绑定数据和 DOM 的中间件

  <div align="center">
    ![Vue MVVM](/images/frame/vue_bing.png) 
  </div> 

  <div style="text-indent: 2em">DOM Listeners、Data Bindings 是实现双向绑定功能的关键，实现原理是Object.defineProperty 中的 get、set 方法 和消息订阅模式。DOM Listeners 监听页面所有 View 层 DOM 元素的变化，发生变化时 Model 层的数据随之变化。Data Bindings 监听 Model 层的数据，数据发生变化时 View 层的 DOM 元素随之变化。</div> 

  ```js
  // 对数据对象的每个属性添加对应的 get、set 方法 
  Object.defineProperty(data, key, {
      enumerable: true,
      configurable: true,
      // 读取数据时调用
      get: function() {

          // do something

          return val;
      },
      // 对数据进行赋值操作时调用
      set: function(newVal) {

          // do something
      }
  });
  ```


## 全家桶
> vue 项目的核心构成

  * __vue-cli__：快速搭建大型单页应用的脚手架工具
    * 自动生成 Vue 项目的目录和文件，
    * 通过热加载方式快速启动项目
    * 利用本地 node 服务器实现页面的实时刷新效果
  * __vue-router__：路由控制工具
    * 用于配置路径和组件的映射关系
    * 通过路径之间的切换而不是传统的超链接方式来实现页面切换
  * __vuex__：状态管理工具
    * 为了解决多个组件间的数据通信和状态管理的维护问题而产生
    * 采用集中存储的方式统一管理项目中所有组件的状态 (组件通信的数据)
  * __axios__：异步通信工具
    * 基于Promise的 HTTP 请求客户端
    * 可以同时在浏览器和 nodejs 中使用



# 二、浏览器渲染 SPA
> 将浏览器中直接生成和操作 DOM 的单页面应用程序
		
  <div style="text-indent: 2em">整个项目只有 index.html 一个完整页面，页面内容是通过不同的 vue 组件拼接后生成代码片段并添加到该页面的标签中展示出来。src/App.vue 是所有组件的父组件，所有其它组件都通过路由渲染到它的 router-view，然后再将 App.vue 渲染到 index.html。</div> 
						
				
## 单页面项目

  ```js
  // 构建流程
  cnpm install vue webpack vue-cli -g   

  cd workspace
  vue init webpack project  // 生成目录 
  cd project && npm i       // 安装依赖包

  npm start                 // 热加载启动服务
  npm run build             // 打包生成静态文件


  // App.vue，兼容 pc 和移动端方案
  export default {
    name: "App",
    created(){
      if(document.documentElement.clientWidth > 640){
          require('./style/pc.scss'); 
          this.plaform = 'pc';     // 父类类名为 .pc
      }else{
          require('./style/mobile.scss'); 
          this.plaform = 'mobile';  // 父类类名为.mobile
      }
    }
  }

  // 项目目录
  ├── README.md              // 说明文档
  ├── node_modules           // 项目依赖包
  ├── .editorconfig          // 定义代码格式
  ├── .babelrc 　            // ES6 语法编译配置
  ├── .postcssrc.js          // css 代码转换配置
  ├── .gitignore             // git 上传需要忽略的文件格式
  ├── .eslintrc.js           // eslint 是管理和检测 js 代码风格
  |
  ├── index.html             // 项目入口文件
  ├── package.json           // 项目基本信息
  ├── package-lock.json      // 优化性能的新增文件
  │
  ├── build                  // webpack 编译打包配置
  │   ├── build.js                 // 打包执行文件
  │   ├── check-versions.js        // 检查 node、npm 等版本
  │   ├── utils.js                 // 构建常用工具
  │   ├── vue-loader.conf.js       // css 加载器配置
  │   ├── webpack.base.conf.js     // 基础配置
  │   ├── webpack.dev.conf.js      // 开发环境
  │   └── webpack.prod.conf.js     // 生产环境
  │
  ├── config                // 项目配置
  │   ├── dev.env.js               // 开发环境
  │   ├── prod.env.js              // 生产环境
  │   └── index.js                 // 基础配置
  │
  ├── src                   // 项目源码
  │   ├── App.vue                  // 根组件
  │   ├── main.js      			       // 主配置文件
  │   ├── assets                   // 需要打包的组件静态资源
  │   │    └── img/js/css
  │   ├── components               // 公共组件目录                 
  │   │    └── common/page                     
  │   │            
  │   ├── router           // 路由配置
  │   │    └── index.js           
  │   │
  │   └── vuex             // Vuex store相关
  │        ├── index.js
  │        ├── action.js             
  │        ├── getter.js           
  │        └── mutation.js 
  │
  └── static              // 不需要打包的插件和公共库资源
      └── css/js/font           
  ```


## 多页面项目

  ```js
  // 目录调整
  src
  ├── assets
  ├── components 
  └── pages
        ├── index
        │    ├── App.vue
        │    ├── index.html
        │    └── index.js
        |
        └── login
            ├── Login.vue
            ├── login.html
            └── login.js


  // 修改webpack配置
  
  // 1、utils.js 末尾添加
  /* 添加部分 ---------------------------- 开始 */

  // glob 模块用于路径匹配配置 (使用 * 等符号)，安装后引用：npm i glob -S 
  var glob = require('glob')
  var HtmlWebpackPlugin = require('html-webpack-plugin')  // 页面模板
  var PAGE_PATH = path.resolve(__dirname, '../src/pages')  // 页面路径
  var merge = require('webpack-merge')    // merge处理

  // 多入口配置：如果 pages 中存在 js 文件就存在就作为入口处理
  exports.entries = function () {
    var entryFiles = glob.sync(PAGE_PATH + '/*/*.js')
    var map = {}
    entryFiles.forEach((filePath) => {
        var filename = filePath.substring(filePath.lastIndexOf('\/') + 1, filePath.lastIndexOf('.'))
        map[filename] = filePath
    })
    return map
  }

  // 多页面输出配置
  exports.htmlPlugin = function () {
    let entryHtml = glob.sync(PAGE_PATH + '/*/*.html')
    let arr = []
    entryHtml.forEach((filePath) => {
        let filename = filePath.substring(filePath.lastIndexOf('\/') + 1, filePath.lastIndexOf('.'))
        let conf = {
            template: filePath,  // 模板来源
            filename: filename + '.html',   // 文件名称
            // 页面模板需要添加对应的 js 脚本，不加则每个页面都会引入所有 js 脚本
            chunks: ['manifest', 'vendor', filename],
            inject: true
        }
        if (process.env.NODE_ENV === 'production') {
            conf = merge(conf, {
                minify: {
                    removeComments: true,
                    collapseWhitespace: true,
                    removeAttributeQuotes: true
                },
                chunksSortMode: 'dependency'
            })
        }
        arr.push(new HtmlWebpackPlugin(conf))
    })
    return arr
  }
  /* 添加部分 ---------------------------- 结束 */


  // 2、webpack.base.conf.js 修改
  entry: utils.entries(),  

  // 3、webpack.dev.conf.js、webpack.prod.conf.js
  plugins: [
      /* 注释 ------------- 开始 */
      // new HtmlWebpackPlugin({
      //   filename: 
      // }),
      /* 注释 ------------- 结束 */

      /* 添加 ------------------ */
  ].concat(utils.htmlPlugin())


  // 初始化页面
  // 1、index.js
  import index from './App.vue'
  new Vue({
    el: '#app',
    router,
    components: { index },
    template: '&lt;index/&gt;'
  })

  // 2、login.js
  import LoginView from './Login.vue'
  new Vue({
    el: '#app',
    render: h => h(LoginView)
  })

  // 3、index.html 页面跳转：a href='login.html'
  ```


## 首屏加载优化

  * 首屏加载效果
    * 页面内容加载完成前使用 loading、进度条、骨架屏 可以提升用户体验
    * 骨架屏：vue-skeleton 使用一些空白内容的图形来展示未加载内容  
  * 路由懒加载
    * `const login = resolve => require(['common/Login.vue'], resolve)`
  * 使用 v-if 减少不必要的组件加载：显示时才渲染弹窗等组件
  * 通过 CDN 引入资源，减小服务器带宽压力
    * 项目依赖会被全部打包到 vender.js，文件很大时首屏加载较慢
    * 使用 cdn 文件代替就不会被打包进去，加载速度较快
  * 将图片等一些静态资源放到云服务器，减小服务器压力
  * 需加载三方资源：按需引入 ElementUI 等组件库
  * 若首屏为登录页则可做成多入口
  * webpack 开启 gzip 压缩

          
## 预渲染
> 在构建阶段简单地生成生成匹配特定路由的静态 html 文件

  * 插件安装：`npm i prerender-spa-plugin -S`
  * 插件配置：`build/webpack.prod.conf.js`
  * 路由配置：`route/index.js`

  ```js
  // build/webpack.prod.conf.js
  const PrerenderSPAPlugin = require('prerender-spa-plugin')
  const Renderer = PrerenderSPAPlugin.PuppeteerRenderer

  const webpackConfig = merge(baseWebpackConfig, {
      plugins: [
          new PrerenderSPAPlugin({
              // 生成文件的路径，该目录只能有一级，
              // vue-cli3.0 时修改为 '/dist'
              staticDir: path.join(__dirname, '../dist'),
              
              // 路由文件，如果有参数则比如 /index/
              routes: ['/', '/index', '/home'],
              
              // 没有配置则不会进行预编译
              renderer: new Renderer({
                  inject: {
                    foo: 'bar'
                  },
                  headless: false,
                  renderAfterDocumentEvent: 'render-event'
              })
          })
      ]
  })

  // route/index.js
  export default new Router({
      mode: 'history', 
      routes: []
  })
  ```


# 三、服务器端渲染 SSR
> 服务端拼接出带数据的 html 并发送到前端，前端把 html 插入到指定节点并渲染页面

  <div style="text-indent: 2em">Vue 是一个构建客户端应用的框架，即 vue 组件是在浏览器中进行渲染的。所谓服务端渲染，是指 Vue 服务端通过预拉取数据实现组件的初始化，然后启用虚拟 DOM 渲染为初始化的 HTML 字符串并发送到客户端，最后需要将这些静态标记"激活"为客户端上完全可交互的应用程序，从而实现前后端同构应用。服务器和客户端都会运行的代码为 通用代码，即 同构。</div> 

  * 优点
    * 更好的SEO，搜索引擎爬虫可以抓取渲染好的页面
    * 更快的首屏渲染时间，产生更好的用户体验，无需等待 Js 加载完成后才显示
  * 缺点 
    * 更多的服务器端负载（CPU 和内存资源）
    * 更复杂的开发和部署（兼容 Node 执行环境）


## 渲染过程

  * 利用 webpack 需要分别对客户端代码和服务器端代码分别打包成两个 json 文件
  * server-bundle 发送给服务器用于生成首屏页面 html
  * client-bundle 发送给浏览器用于混合静态标记
  * server.js 文件将两个打包文件和 HTML 模板混合生成 HTML 返回给浏览器渲染

  <div align="center">
    ![Vue SSR](/images/frame/vue-ssr.png) 
  </div> 

				 
## 开发约束
> 通用代码应该兼容浏览器和服务端 Node 的执行环境
  
  * 不可使用全局环境变量：window、document 等只能在浏览器中运行
  * 生命周期钩子函数只有 beforeCreate、created 能同时运行在前后端
  * 避免状态单例
    * Node 服务器是一个长期运行的进程，代码进入该进程时就会取值并留存在内存。如果创建一个实例对象，它将在每个传入的请求之间共享，容易导致交叉请求状态污染。所以应该暴露一个可以重复执行的工厂函数，为每个请求创建新实例
    * 同样的规则也适用于 router、store、event bus 实例。你不应该直接从模块导出并将其导入到应用程序中。而是需要在 createApp 中创建一个新实例并从根实例注入
    
     
## 模板插值

### 安装模块
	
  * 安装：`cnpm i vue vue-server-renderer koa koa-router -S`
  * vue-server-renderer：获取 vue 实例并渲染成 html 结构，并配合 webpack 构建项目


### 单个文件 
  <div align="center">
    ![vue-server-renderer](/images/frame/vue-render-1.png) 
  </div> 
  

### 目录文件
  
  <div align="center">
    ![vue-render-index](/images/frame/vue-render-2.png) 
  </div> 
  

  ```js
  // server.js
  const Vue = require('vue')
  const Koa = require('koa')
  const Router = require('koa-router')
  const renderer = require('vue-server-renderer').createRenderer({
    template: require('fs').readFileSync('./index.html', 'utf-8')
  }) 

  // 1、创建 koa、koa-router 实例
  const app = new Koa()
  const router = new Router()

  // 2、路由中间件
  router.get('*', async (ctx, next) => {
    // 渲染上下文对象：title、meta 会插入模板中
    const context = {
      title: ctx.url,
      meta: `
        &lt; meta charset="UTF-8" &gt;
        &lt; meta name="description" content="基于webpack、koa搭建的SSR" &gt;
      `
    }

    // 创建Vue实例
    const vue_app = new Vue({
      data: {
        url: ctx.url
      },
      template: `{{ url }}`
    })

    // 有错误返回 500，无错误返回 html
    try {
      const html = await renderer.renderToString(vue_app, context)
      ctx.status = 200
      // html 结构会插入到 template 中的 !--vue-ssr-outlet-->
      ctx.body = html
    } catch (error) {
      ctx.status = 500
      ctx.body = 'Internal Server Error'
    }
  })
  app.use(router.routes()).use(router.allowedMethods())

  // 3、启动服务
  app.listen(3000, () => {
    console.log(`server started at localhost:3000`)
  })
  ```


### 结果分析
								
  * SPA：前端打包后的 html 只包含 head 部分，body 部分都是动态插入到 `div id = "app"`
  * SSR：服务器提前编译 Vue 资源生成 HTML 并返回给浏览器，这样网络爬虫爬取的内容就是网站上所有可呈现的内容



## 项目构建
> vue-ssr 代码库  

### 基础构建
  * webpack 配置
    * 新增：`webpack.server.conf.js`
    * 修改：`vue-loader.conf.js、webpack.base/prod.conf.js`
  * 入口配置：`main.js、entry-client.js、entry-server.js`
  * 组件修改
    * 修改 router 路由文件，给每个请求一个新的 router 实例
    * 修改状态管理 vuex 文件，给每个请求一个新的 store 实例
    * 通过 asyncData 方法获取异步数据
  * 服务器配置
    * 根目录新建 server.js 文件
    * 修改 package.json 文件
    * 修改 index.html 文件
  * 启动项目
    * 安装依赖：`cnpm install` 
    * 打包文件：`npm run build`
    * 启动服务：`node server.js`   


### 服务端预拉取数据

  <div style="text-indent: 2em">如果应用程序依赖于一些异步数据，那么在开始渲染过程之前，需要先预取和解析好这些数据，并将数据拼装到全局 Vuex状态中。但是需要注意：在挂载到客户端应用程序之前，需要获取到与服务器端应用程序完全相同的数据，否则 客户端应用程序会因为使用与服务器端应用程序不同的状态 导致混合失败。</div>
  <div style="text-indent: 2em">目前较好的解决方案是：给路由匹配的一级子组件一个 asyncData 方法，asyncData 是约定的函数名，表示渲染组件需要预先执行它获取初始数据，它返回一个 Promise，以便后端渲染时可以知道什么时候该操作完成。注意，由于此函数会在组件实例化之前调用，所以它无法访问 this，需要将 store 和路由信息作为参数传递进去。</div> 
									


# 四、模块引用

## npm 安装的模块
> 默认全局引用，局部引用就是在组件中按需引用

### 常用模块

  * 安装：`cnpm install axios vuex element-ui vue-awesome-swiper echarts -S`
    * -D / --save: 生产环境还要用到的包 (package.json/dependencies )
    * -S / --save-dev: 只在开发环境用到的包 (package.json/devDependencies)
  * 配置
    ```js
    // main.js 

    // axios
    import axios from 'axios'
    Vue.prototype.$axios = axios

    // 自定义配置 axios
    import ajax from './axios/http'
    Vue.prototype.$get = ajax.get
    Vue.prototype.$post = ajax.post

    // 组件库
    import ElementUI from 'element-ui'
    import 'element-ui/lib/theme-chalk/index.css'
    Vue.use(ElementUI)

    // 轮播插件
    import VueAwesomeSwiper from 'vue-awesome-swiper'
    import 'swiper/dist/css/swiper.css'
    Vue.use(VueAwesomeSwiper)

    // 图表插件
    import echarts from "echarts"		
    Vue.prototype.$echarts = echarts 

    // vuex
    import store from './vuex/index'
    new Vue({
      el: '#app',
      router,
      store,
      data: {
        // 根组件的空实例
        eventHub: new Vue()
      }
    })
    ```

				
### Sass						
  * 安装：`cnpm install sass-loader node-sass css-loader -S`
  * 使用：`style lang="scss" scoped`
  * 配置
    ```js
    // build/webpack.base.conf.js
    module.exports = {
      resolve: {
        // 添加扩展名 .scss
        extensions: ['.js', '.vue', '.json', '.scss'],
      },
      module: {
        rules: {
          // 最后添加
          {  
            test: /\.scss$/,  
            loaders: ['style', 'css', 'sass']  
          }
        }
      }
    }
    ```


### 对象展开运算符 
  * 安装：`cnpm install babel-plugin-transform-object-rest-spread -S`
  * 使用：`...mapGetters([ 'count' ])`
  * 配置：`.babelrc 文件 -- "plugins": ["transform-object-rest-spread"]`


### 集成 Ace 代码编辑器
  * 安装：cnpm install ace-builds -S
  * 配置：[Vue Ace](https://blog.csdn.net/YoshinoNanjo/article/details/82978668)


## npm 不能安装的插件
> 直接引入插件文件

  * index.html：`script src="/static/bootstrap.min.js"`
  * main.js：`import '@/assets/css/bootstrap.min.css'`


## 静态资源

### global.scss
  * 安装：cnpm install sass-resources-loader --save-dev
  * 注意：如果要导入多个文件则全部导入到 global.scss：@import './reset.scss'
  * 配置 

  ```js
  // build/utils.js -- scss: generateLoaders('sass') 
  scss: generateLoaders('sass').concat(
    {
      loader: 'sass-resources-loader',
      options: {
        resources: path.resolve(__dirname, '../src/assets/global.scss')
      }
    }
  )
  ```


### 静态文件
  * js：`import user from '@/components/user'`
  * css
    * `@import './swiper.css'`
    * `@import '../../assets/scss/mixin.scss'`
    * `@import url('../assets/css/reset.css')`
  * template
    * 绝对路径：`img src='/static/images/logo.png'`    
    * 相对路径：`img src='../../assets/images/1.png'`  
									

 
# 五、基础配置

## 快捷键
  ```json
  // package.json文件
  "scripts": {
      "build": "node build/build.js",
      "start": "rimraf dist && npm run build"
  }
  ```


## 路径别名
> 用于以后引用路径时减少复杂度：import getList from 'api/index'

  ```js
  // build/webpack.base.conf.js
  resolve: {
    alias: {
      'vue$': 'vue/dist/vue.esm.js',
      '@': resolve('src'),
      'api': path.resolve(__dirname, '../src/api')
    }
  }
  ```

		
## 代码调试
  * 开发工具：Chrome vue-devtools
  * debugger 模式：修改 config/index.js 文件 `devtool: '#eval-source-map'`

## 手机查看
> 将手机连接到与电脑相同的 Wifi

  1. config/index.js -- dev/host：当前电脑的IP地址
  2. 重新启动服务后，在手机上直接打开地址，或者转为二维码之后使用手机扫描

		
## 请求代理
> 服务器代理网络用户去获取网络信息，可以解决开发环境中的跨域问题

  ```js
  // config/index.js
  dev: {  
    proxyTable: {   
      '/ajaxUrl': {      // 请求地址
          target: 'http://cangdu.org:8000/', // 实际映射到的目标地址
          changeOrigin: true,                // 是否跨域
          secure: false,                     // https 接口需要配置
          pathRewrite: {
              '^/ajaxUrl': ''                // target 地址的别名
          }
      } 
    }  
  }
  ```

		
## 全局接口
> 开发环境和生产环境配置不同接口

  ```js
  // config/dev.env.js 
  module.exports = merge(prodEnv, {
      NODE_ENV: '"development"',
      API_ROOT: '"http://10.148.221.210:5000/"',
      WS_ROOT: '"ws://10.148.221.210:5001"'
  })


  // config/prod.env.js 
  module.exports = {
      NODE_ENV: '"production"',
      API_ROOT: '"http://10.148.134.54:5000/"',
      WS_ROOT: '"ws://10.148.134.54:5001"'
  }


  // axios baseURL
  axios.defaults.baseURL = process.env.API_ROOT

  // 组件 url
  this.websocket = new WebSocket(process.env.WS_ROOT) 
  ```


# 六、打包问题
> npm run build 执行打包操作后生成 dist 文件夹  

## 打包路径
> 打包后资源加载错误，需要在打包前将 绝对路径改成相对路径

  ```js
  // 文件 config/index.js  
  build: {
    assetsPublicPath: './',      //路径改为相对路径
    productionSourceMap: false   //加快编译 
  }

  // 静态资源 build/utils.js  
  if (options.extract) {
    return ExtractTextPlugin.extract({
      use: loaders,
      publicPath:'../../',
      fallback: 'vue-style-loader'
    })
  }
  ```


## 路由模式
> history 路由模式的项目打包后的页面没有渲染

  * 使用hash模式
  * 设置routes里的路由name 

			
## IE 浏览器
> 打包后在 IE 浏览器不显示

* 原因：Promise在ie上的支持不是很好导致的
* 解决
  * 安装插件：cnpm install --save-dev babel-polyfill
  * main.js 引入：import 'babel-polyfill'   
  * 配置
    ```js
    // build/webpack.base.conf.js
    module.exports = {
      entry: {
        app: ["babel-polyfill", "./src/main.js"]  
      }
    }
    ```
          

## favicon.ico 
> 地址栏左边的图标文件无法加载，它是代表网站的标志 

  * 问题：打印台报错，原因是图标文件不存在或路径错误
  * index.html：`link rel="shortcut icon" type="image/x-icon" href="favicon.ico"`


# 七、静态服务
> 打包后快速搭建静态服务器

  * 全局安装：`npm install -g live-server / http-server`
  * 运行项目：`live-server / http-server`
  * 快捷命令：`"script":{ "server":"live-server ./dist --port=8080" }`
 

