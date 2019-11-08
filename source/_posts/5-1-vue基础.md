---
title: Vue 基础及其项目构建
tags:
  - Vue.js
categories: Vue.js
top: false
keywords:
  - vue
date: 2019-04-12 00:32:55
description: 框架、SPA、SSR、模块引用、常用配置、性能优化、打包问题、静态服务
---

# 一、JS 框架


## 基础介绍

  * __核心思想__：`数据驱动的组件系统、轻量高效的前端组件化方案`
  * __开发理念__
    * `渐进式开发`：先构建基础的用户界面，然后再根据需求灵活添加功能。
    * `聚焦视图层`：它不是一个全能框架而只关注视图层，容易学习和集成其它 [开源库](https://segmentfault.com/p/1210000008583242/read?from=timeline)。
    * `组件化`：通过简洁 API 提供高效的数据驱动的组件系统，通过操作数据来更新页面。
    * `响应式`：页面根据不同分辨率改变大小，适应式则是根据分辨率去选择不同的页面。
  * __MVVM 模式__
    * `Model`：对应 js 对象的数据部分
    * `View`：对应 DOM 元素的视图部分
    * `Viewmodel`：绑定数据和 DOM 的中间件

    <div align="center"> 
      ![Vue MVVM](/images/vue/vue_bing.png) 
    </div> 


## 双向绑定

### 实现方式

  * __发布者-订阅者模式__
    * 一般通过 sub、pub 的方式实现数据和视图的绑定监听。
    * 更新数据方式通常是 `vm.set('property', value)`，但这种方式现在比较 low。
    * 我们更希望通过 `vm.property = value` 更新数据，同时自动更新视图，于是有了下面两种方式。
  * __脏值检查__
    * angular 通过脏值检测的方式比对数据是否有变更来决定是否更新视图。
    * 最简单的方式是通过 setInterval() 定时轮询检测数据变动。
    * angular 只有在指定事件触发时进入脏值检测：DOM 事件、XHR 响应事件等。
  * __数据劫持__
    * vue 采用数据劫持结合发布者-订阅者模式的方式
    * 通过 `Object.defineProperty()` 来劫持各个属性的 setter、getter。
    * 在数据变动时发布消息给订阅者，触发相应的监听回调。


### vue 实现

  1. 实现一个数据监听器 __Observer__，能够对数据对象的所有属性进行监听，如有变动可拿到最新值并通知订阅者。
  2. 实现一个指令解析器 __Compile__，对每个元素节点的指令进行扫描和解析，并根据初始化模板 data 数据和相应的订阅器。
  3. 实现一个 __Watcher__，作为连接 Observer 和 Compile 的桥梁，能够订阅并收到每个属性变动的通知，然后执行相应回调函数，从而更新视图。
  4. mvvm __入口函数__，整合以上三者。


  <div align="center"> 
    ![Vue 双向绑定](/images/vue/data_bing.png)
  </div> 
 

### vue 核心代码
> 核心就是通过 Object.defineProperty() 重新定义了对象获取属性值 get 和设置属性值 set 的操作，该方法接收三个参数：操作对象、对象属性名、属性描述符 (对象)。

  ```js
  // Observer.js
  function defineReactive(data, key, val) {
    observe(val); // 监听子属性
    Object.defineProperty(data, key, {
      enumerable: true, // 可枚举
      configurable: false, // 不能再 define
      get: function() {
        // 由于需要在闭包内添加 watcher，所以通过 Dep 定义一个全局 
        // target 属性来暂存 watcher, 添加完移除
        if (Dep.target) dep.addSub(Dep.target)
        return val;
      },
      set: function(newVal) {
        if (val === newVal) return
        val = newVal
        // 通知所有订阅者
        dep.notify()   
      }
    })
  }
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


## UI 框架
  * PC端：`Element、iView、vue-element-admin`
  * 移动端
    * `Vux`：采用微信的 weui 的设计样式，主要服务于微信页面
    * `Vant`：由有赞前端团队基于有赞统一的规范实现，类似微信样式
    * `mint-ui`：饿了么出品，文档详细，示例齐全，缺点是比较丑
    * `Muse-UI`：兼容 PC 端和移动端，样式好看，建议使用在主打海外市场的应用
    * `vonic`：采用了 ionic 样式，有部分组件被存放到 Vue 对象，比如 this.$tabbar
    * `vue-material`：基于谷歌的 Material Design 规范构建，缺点是没有时间选择器、非中文文档
    * `cube-ui`：由滴滴内部组件库精简提炼而来，核心目标是做到体验极致、灵活性强、易扩展以及提供良好的周边生态（后编译）



# 二、浏览器渲染 SPA
> 将浏览器中直接生成和操作 DOM 的单页面应用程序
		
  <div style="text-indent: 2em">整个项目只有 index.html 一个完整页面，页面内容是通过不同的 vue 组件拼接后生成代码片段并添加到该页面的标签中展示出来。src/App.vue 是所有组件的父组件，所有其它组件都通过路由渲染到它的 router-view，然后再将 App.vue 渲染到 index.html。</div> 
						
				
## 单页面项目
> 查看版本：vue -V。eslint + prettier 统一代码风格：https://yq.aliyun.com/articles/667333

  1. __vue-cli 2__
    * 安装：`cnpm i vue webpack vue-cli -g`
    * 初始化：`vue init webpack project`
    * 启动打包：`npm start && npm run build`
  2. __vue-cli 3__
    * 安装：`cnpm install @vue/cli webpack -g`
    * 初始化：`vue create test`
      * 默认配置
      * 自定义配置
        * 按钮：`A -- 全选、空格键 -- 选择与取消、上下 -- 移动`
        * 选项：`Babel、Router、Vuex、CSS Pre-processors、Linter / Formatter`
        * 校验：`ESLint + Prettier && Lint on save`
        * 保存：`In dedicated config files && 自定义`
    * 启动打包：`npm start && npm run build`


  ```js
  // vue.config.js：根目录新建，webpack 自动识别的自定义配置文件

  // cnpm i path -S
  let path = require('path')
  function resolve(dir) {
    return path.join(__dirname, dir)
  }

  module.exports = {
    // 根路径
    publicPath: "./",
    // 输出文件目录
    outputDir: 'dist',
    // 取消eslint验证
    lintOnSave: false,
    
    configureWebpack: config => {
      Object.assign(config, {
        // 开发生产共同配置
        output: {            // 输出重构，打包编译后的文件名称：模块名称.版本号.时间戳
          filename: `[name]-${process.env.VUE_APP_Version}-${Timestamp}.js`,
          chunkFilename: `[name]-${process.env.VUE_APP_Version}-${Timestamp}.js`
        },
        // 别名配置
        resolve: {
          alias: {
            '@': path.resolve(__dirname, './src'),
            '@c': path.resolve(__dirname, './src/components'),
            '@p': path.resolve(__dirname, './src/pages')
          } 
        }
      })

      // vux 引用配置
      require('vux-loader').merge(config, {
        options: {},
        plugins: ['vux-ui']
      })
    },
    // 配置目录别名：用于以后引用路径时减少复杂度
    chainWebpack: config => {
      config.resolve.alias
        .set('@$', resolve('src'))
        .set('assets', resolve('src/assets'))
        .set('views', resolve('src/views'))
        .set('components', resolve('src/components'))
    },
    devServer: {
        // host: 'localhost',
        host: "127.0.0.1",
        port: 8000,      // 端口号
        https: false,    // https:{type:Boolean}
        open: true,      // 配置自动启动浏览器  http://172.16.1.12:7071/rest/mcdPhoneBar/ 
        hotOnly: true,   // 热更新
        // 配置跨域请求
        // proxy: 'http://localhost:8000'   // 只有一个代理
        proxy: {                            // 配置多个代理
          "/rest/*": {
            target: "http://172.16.1.12:7071",
            changeOrigin: true,
            // ws: true,//websocket支持
            secure: false
          },
          "/pbsevice/*": {
            target: "http://172.16.1.12:2018",
            changeOrigin: true,
            //ws: true,//websocket支持
            secure: false
          },
        },
        // 模拟本地数据
        before(app) {  
            app.get('/api/seller', (req, res) => {
                res.json({ errno: 0, data: requare('./data/seller.json') }) 
            }),
            app.post('/api/foods', (req, res) => { 
                res.json({ errno: 0, data: foods })
            })
        }
    }
  }
  ```


          
## 预渲染
> 在构建阶段简单地生成生成匹配特定路由的静态 html 文件

  * 插件安装：`npm i prerender-spa-plugin -S`
  * 插件配置：`build/webpack.prod.conf.js`
  * 路由配置：`route/index.js`

  ```js
  // vue-cli2：build/webpack.prod.conf.js
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
    ![Vue SSR](/images/vue/vue-ssr.png)
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
  ```js
  // server.js

  // 1.创建 vue 实例
  const Vue = require("vue")
  const app = new Vue({
      data: {
          hello: "hello world"
      },
      template: "<div>{{hello}}</div>"
  })

  // 2.创建 renderer 实例
  const renderer = require("vue-server-renderer").createRenderer({
      // 模版插值：注释部分为模版插入的占位符
      template: `
          <!DOCTYPE html>
          <html lang="en">
          <head>
          <meta charset="UTF-8">
          <meta name="viewport" content="width=device-width, initial-scale=1.0">
          <meta http-equiv="X-UA-Compatible" content="ie=edge">
          <title>Document</title>
          <body> 
              <!--vue-ssr-outlet-->
              <div>{{person}}</div>
          </body>
          </html>
      `
  })

  // 3.将 vue 渲染为 html
  renderer.renderToString(app, { person: "Mike" }, (err, html)=>{
      if(err) throw err
      console.log(html)
  })
  ```
  

### 目录文件
  
  ```html
  <!DOCTYPE html>
  <html lang="en">
  <head>
    <!-- 三个大括号不进行 html 转义 -->
    {{{meta}}}
    <title>{{title}}</title>
  <body> 
      <!--vue-ssr-outlet-->
  </body>
  </html>
  ```
  

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
        <meta charset="UTF-8">
        <meta name="description" content="基于webpack、koa搭建的SSR">
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
								
  * SPA：前端打包后的 html 只包含 head 部分，body 部分都是动态插入到 `<div id = "app"></div>`
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


### 适配移动端

  * 安装：cnpm install postcss-px-to-viewport -D
  * 配置
    ```js
    // postcss.config.js
    module.exports = {
      plugins: {
        'autoprefixer': {},
        'postcss-px-to-viewport': { 
          viewportWidth: 750, // 视窗的宽度，对应的是我们设计稿的宽度，一般是750 
          viewportHeight: 1334, // 视窗的高度，根据750设备的宽度来指定，一般指定1334，也可以不配置 
          unitPrecision: 3, // 指定`px`转换为视窗单位值的小数位数（很多时候无法整除） 
          viewportUnit: 'vw', // 指定需要转换成的视窗单位，建议使用vw 
          selectorBlackList: ['.ignore', '.hairlines'], // 指定不转换为视窗单位的类，可以自定义，可以无限添加,建议定义一至两个通用的类名 
          minPixelValue: 1, // 小于或等于`1px`不转换为视窗单位，你也可以设置为你想要的值 
          mediaQuery: false // 允许在媒体查询中转换`px`
        }
      }
    }
    ```


### 集成 Ace 代码编辑器
  * 安装：cnpm install ace-builds -S
  * 配置：[Vue Ace](https://blog.csdn.net/YoshinoNanjo/article/details/82978668)


## npm 不能安装的插件
> 直接引入插件文件

  * index.html：`<script src="/static/bootstrap.min.js"></script>`
  * main.js：`import '@/assets/css/bootstrap.min.css'`


## 静态资源

  * js
    * 组件：`import user from '@/components/user'`
    * 方法：`import { getUserInfo } from './tool.js'`
  * css
    * `@import './swiper.css'`
    * `@import '../../assets/scss/mixin.scss'`
    * `@import url('../assets/css/reset.css')`
  * template
    * 绝对路径：`<img src='/static//images/logo.png'>`    
    * 相对路径：`<img src='../../assets//images/1.png'>`  
									

 
# 五、常用配置

## 快捷键
  ```json
  // package.json文件
  "scripts": {
      // 开发环境 development、生产环境 production、test 测试环境
      "serve": "vue-cli-service serve --mode dev",
      "build": "vue-cli-service build --mode prod",
      "test": "vue-cli-service build --mode test",
      "lint": "vue-cli-service lint"
  }

  // .env.dev
  NODE_ENV = "dev"
  VUE_APP_URL = "/apigateway"

  // .env.test
  NODE_ENV = "production"  // 打包环境
  VUE_APP_URL = "http://116.228.196.136:10003/apigateway"
    
  // .env.prod
  NODE_ENV = "prod"
  VUE_APP_URL = "https://api.yongcheng.com:19090/apigateway"
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


# 六、性能优化

  * 路由懒加载：`const login = resolve => require(['common/Login.vue'], resolve)`。
  * 首屏加载
    * 页面内容加载完成前使用 loading、进度条、骨架屏 可以提升用户体验。
    * 骨架屏：vue-skeleton 使用一些空白内容的图形来展示未加载内容。
  * 通过 CDN 引入资源，减小服务器带宽压力
    * 项目依赖会被全部打包到 vender.js，文件很大时首屏加载较慢
    * 使用 cdn 文件代替就不会被打包进去，加载速度较快
  * 使用 v-if 减少不必要的组件加载：显示时才渲染弹窗等组件
  * 将图片等一些静态资源放到云服务器，减小服务器压力
  * 需加载三方资源：按需引入 ElementUI 等组件库
  * 若首屏为登录页则可做成多入口
  * webpack 开启 gzip 压缩



# 七、打包问题
> npm run build 执行打包操作后生成 dist 文件夹  

## 资源路径
> 打包后资源加载错误，因为绝对路径加载的资源打包后会报错

  * static 目录
    * 特点：文件不会被 wabpack 编译处理，会被直接复制到打包目录
    * 使用方式
      * HTML 使用相对路径：`../static//images/bg.png`
      * JS 使用打包后路径：`./static//images/bg.png`
      * CSS 使用根目录路径：`../../static//images/bg.png`
  * assets 目录
    * 文件会经过 webpack 打包并重新编译，一般只能使用相对路径
    * 图片等不能用于动态绑定 因为 webpack 使用 ` commenJS ` 规范而必须使用 require


  ```js
  // 文件 config/index.js  
  build: {
    assetsSubDirectory: './static',// 解决 css 路径问题
    assetsPublicPath: './',      // 解决 js 路径问题
    productionSourceMap: false   // 加快编译速度
  }

  // 静态资源 build/utils.js  
  if (options.extract) {
    return ExtractTextPlugin.extract({
      use: loaders,
      publicPath:'../../',   // 打包时提取 CSS
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


# 八、静态服务
> 打包后快速搭建静态服务器

  * 全局安装：`npm install -g live-server / http-server`
  * 运行项目：`live-server / http-server`
  * 快捷命令：`"script":{ "server":"live-server ./dist --port=8080" }`
 

