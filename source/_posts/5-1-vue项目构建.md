---
title: Vue 项目构建
tags:
  - Vue.js
categories: Vue.js
top: false
keywords:
  - vue
date: 2019-06-05 00:32:55
description: Vue 框架、项目构建、Router、Axios、Vuex
---

# 一、Vue 框架

  * __核心思想__：数据驱动的组件系统、轻量高效的前端组件化方案。
  * __开发理念__
    * `渐进式开发`：先构建基础的用户界面，然后再根据需求灵活添加功能。
    * `聚焦视图层`：它不是一个全能框架而只关注视图层，容易学习和集成其它 [开源库](https://segmentfault.com/p/1210000008583242/read?from=timeline)。
    * `组件化`：通过简洁 API 提供高效的组件系统，通过操作数据来更新页面。
    * `响应式`：页面根据不同分辨率改变大小，适应式则根据分辨率选择不同页面。
  * __MVVM 模式__
    * 组成部分：View 模板、Model 数据、Viewmodel 监听的中间件。
    * 主要特点：响应式、双向绑定、数据驱动、视图与数据分离、只需要关注 View 和 Model 而方便开发。

    <div align="center"> 
      ![Vue MVVM](/images/vue/vue_bing.png) 
    </div> 


## 全家桶
> vue 项目的核心构成。

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
    * `Vux`：采用微信的 weui 的设计样式，主要服务于微信页面。
    * `Vant`：由有赞前端团队基于有赞统一的规范实现，类似微信样式。
    * `mint-ui`：饿了么出品，文档详细，示例齐全，缺点是比较丑。
    * `Muse-UI`：兼容 PC 端和移动端，样式好看，建议使用在主打海外市场的应用。
    * `vonic`：采用了 ionic 样式，有部分组件被存放到 Vue 对象，比如 this.$tabbar。
    * `vue-material`：基于谷歌的 Material Design 规范构建，缺点是没有时间选择器、无中文文档。
    * `cube-ui`：由滴滴内部组件库精简提炼而来，核心目标是做到体验极致、灵活性强、易扩展以及提供良好的周边生态（后编译）。


## vue 文件渲染流程

  1. 使用 vue-loader 编译时，会将 vue 文件中的 template 转化成 render 函数，作为组件对象方法，供运行时调用。
  2. vue 编译器将 template 转化成 render 函数，template先转化成ast(抽象语法树)，再将 ast 转化成代码字符串 `(_c('div', [_c('span', [_v("1234")]), _c('app')], 1))`，再拼接该字符串 `"with(this){return " + code + "}"`，再将拼接后的字符串作为参数放到 `new Function(code)`，然后赋值给 render，render 调用时触发 `new Function(code)`。
  3. _c 是 creatElement 函数的引用，该函数返回标签对应的节点实例。调用 render 函数时，节点实例以树形结构的返回，子节点保存在 children。当存在 `_c('app')`，_c 会从components 中找到对应的组件对象，并根据组件对象使用 `Vue.extend()` 创建组件构造函数。在创建节点实例时，将构造函数保存在节点实例的属性上。
  4. 初始化 `new Vue()`，会执行 `vm.init() -> vm.$mount -> mountComponent` 。mountComponent 中的 `updateComponent = function () { vm._update(vm._render(), hydrating) }` 当页面初始化或者更新时调用，vm._render() 返回树形结构的节点实例，并作为参数传入 vm._update，在 vm._update 中会调用 `vm.__patch__` 更新页面并将节点信息传入。`vm.__patch__` 根据节点实例属性上的构造函数创建组件实例。


## 双向绑定实现流程

  1. __解析模板为 render 函数。__
    * 模板本质是一串包含指令等逻辑的字符串，最终必须转换成 JS 代码，原因为：必须使用 JS 来处理模板中的逻辑、必须使用 JS 将模板渲染到页面上。
    * render 函数包含所有的模板信息并返回一个虚拟 DOM，模板中用到的 data 属性和 vue 指令都分别变成了 JS 变量和 JS 逻辑，它的核心是设置对象属性和方法时可以简化代码的 with 函数。
  2. __响应式开始监听。__
    * Object.defineProperty 监听：将一个普通 Js 对象传给 Vue 实例的 data 选项时，vue 将遍历此对象的所有属性，并使用 Object.defineProperty 把这些属性全部转为 getter/setter。
    * 将 data 的属性代理到 vm：render 函数中 with(this){} this 指的是 vm (vm.title、vm.list)，其中 title、list 变量都是 data 的属性。
  3. __首次渲染显示页面，且绑定依赖。__
    * 初次渲染，执行 updateComponent() 函数，执行 vm._render() 函数。
    * 执行 render 函数，会访问到 vm.title、vm.list。
    * 访问 vm.title vm.list 就会被响应式的 get 方法监听到。
    * 执行 updateComponent()函数，会走到 vdom 的 patch 方法。
    * patch 将 vnode 渲染成 DOM，初次渲染完成。
  4. __data 属性变化，触发 rerender 函数。__
    * 修改属性，被响应式的 set 监听到。
    * set 中执行 updateComponent。
    * updateComponent 重新执行 vm._render()。
    * 生成的 vnode、prevVnode，通过 patch 进行对比。
    * 局部渲染 html。


  ```js
  // 1、解析模板为 render 函数：核心 with 的参数 this 表示 vm。
  <div id="app">
      <p>{{price}}</p>
  </div>
  with(this){
    // _c、_v、_l 函数分别用来创建一个 html 元素、文本节点、数组。
    return _c(
      'div',
        {
          attrs: {"id": "app"}
        },
        [
          _c('p', [_v(_s(price))])
        ]
    )
  }

  // 2、响应式监听
  var obj = {};   // 要传给 
  var data = {
      price: 100,
      name: 'zhangsan'
  };
  var key, value;
  for (key in data) {
      /**
       * @prame 闭包：保证 key 的独立作用域。
       * @prame getter/setter 在属性被访问和修改时通知而让 vue 追踪依赖，但对用户不可见。
       * @prame obj 传给 Vue 实例的 data 选项时，遍历它的所有属性并设置 getter/setter。
      **/
      (function (key) {
          Object.defineProperty(obj, key, {
              get: function() {
                  console.log('get', key);
                  return data[key];
              },
              set: function(newVal) {
                  console.log('set', newVal);
                  data[key] = newVal;
              }
          })
      })(key)
  }

  // 3、vue 模板被渲染为 html
  vm._update(vnode) {
    const prevVnode = vm._vnode;
    vm._vnode = vnode;
    if (!prevVnode) {    // 第一次没有值
        vm.$el = vm._patch_(vm.$el, vnode);
    } else {
        vm.$el = vm._patch_(prevVnode, vnode);
    }
  }
  function updateComponent() {
    vm._update(vm._render());
  }
  ```


# 二、项目构建

## 浏览器渲染 SPA
> 浏览器中直接渲染组件的单页面应用程序。整个项目只有一个完整页面 index.html、一个根组件 App.vue。具体流程为：各个组件通过路由拼接到 App.vue router-view，然后将拼接结果转换为代码片段并添加到 index.html 标签中展示出来。

  ```js
  // 初始化目录
  cnpm install vue @vue/cli webpack -g
  vue -V、vue create test

  // 自定义配置
  按钮：A -- 全选、空格键 -- 选择与取消、上下 -- 移动
  选项：Babel、Router、Vuex、CSS Pre-processors、Linter / Formatter
  校验：ESLint + Prettier、Lint on save
  保存：In dedicated config files、自定义文件名

  // 启动打包
  npm start、npm run build

  // vue.config.js：根目录新建，webpack 自动识别的自定义配置文件。
  let path = require('path')
  function resolve(dir) {
    return path.join(__dirname, dir)
  }
  module.exports = {
    publicPath: "./",    // 根路径
    outputDir: 'dist',   // 输出文件目录
    lintOnSave: false,   // 取消eslint验证
    configureWebpack: config => {
      Object.assign(config, {
        // 输出配置打包文件名：模块名称.版本号.时间戳
        output: {    // 
          filename: `[name]-${process.env.VUE_APP_Version}-${Timestamp}.js`,
          chunkFilename: `[name]-${process.env.VUE_APP_Version}-${Timestamp}.js`
        },
        // 配置目录别名：引用路径时减少复杂度
        resolve: {
          alias: {
            '@': path.resolve(__dirname, './src'),
            'views': path.resolve(__dirname, './src/views'),
            'assets': path.resolve(__dirname, './src/assets')
          } 
        }
      })
    },
    // 配置目录别名
    chainWebpack: config => {
      config.resolve.alias
        .set('@', resolve('src'))
        .set('assets', resolve('src/assets'))
        .set('views', resolve('src/views'))
    },
    devServer: {
      host: "127.0.0.1",
      port: 8000,
      https: false,
      open: true,
      hotOnly: true,   // 热更新
      // 配置跨域请求
      // proxy: 'http://localhost:8000'   // 只有一个代理
      proxy: {                            // 配置多个代理
        "/rest/*": {  // 请求地址
          target: "http://172.16.1.12:7071",  // 实际映射到的目标地址
          changeOrigin: true,  // 是否跨域
          // ws: true,//websocket支持
          secure: false,  // https 接口需要配置
        },
        '/api/a': {
          target: 'http://hongqiao-zatech-channel.test.za-tech.net',
          changeOrigin: true,
          pathRewrite: { '^/api': '/' }
        }
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


## 服务器端渲染 SSR

  * 两种模式：后端首次渲染的单页面应用模式、后端模板渲染模式，区别在于使用后端路由的程度。前者是后端模板渲染和单页面的组合，首次加载时 vue 服务器将当前页面的数据和组件模板生成 html 字符串返回给浏览器，js 资源加载完成后运行单页面应用。
  * 优点：更好的 SEO 而方便搜索引擎爬虫、更快的首屏渲染而无需等待 Js 加载完成。
  * 缺点：更多的服务器负载（CPU 和内存资源）、更复杂的开发部署（兼容 Node 环境）。
  * 实现
    * 选择单页面框架 react/vue。
    * 选择 node 服务端框架 express/koa2。
    * 实现核心逻辑：node 根据路由渲染单页面组件。
    * 优化开发和发布环境自动化构建工具 webpack。
 

## 常用配置

  ```js
  // 1、资源引用
  // 适配移动端的库：postcss.config.js
  cnpm install postcss-px-to-viewport -D
  module.exports = {
    plugins: {
      'autoprefixer': {},
      'postcss-px-to-viewport': { 
        viewportWidth: 750, // 视窗的宽度，对应的是我们设计稿的宽度，一般是750 
        viewportHeight: 1334, // 视窗的高度，根据750设备的宽度来指定，一般指定 1334，也可不配置 
        unitPrecision: 3, // 指定`px`转换为视窗单位值的小数位数（很多时候无法整除） 
        viewportUnit: 'vw', // 指定需要转换成的视窗单位，建议使用vw 
        selectorBlackList: ['.ignore', '.hairlines'], 
        // 指定不转换为视窗单位的类，可以自定义，可以无限添加,建议定义一至两个通用的类名
        minPixelValue: 1, // 小于或等于`1px`不转换为视窗单位，你也可以设置为你想要的值 
        mediaQuery: false // 允许在媒体查询中转换`px`
      }
    }
  }
  // Ace 代码编辑器插件
  cnpm install ace-builds -S
  https://blog.csdn.net/YoshinoNanjo/article/details/82978668
  // 静态资源
  @import './reset.css';
  绝对路径 <img src='/images/logo.png'>;
  import user from '@/components/user';
  import { getUserInfo } from './tool.js';

  // 2、环境接口配置
  // package.json：开发 development、生产 production、测试 test
  "scripts": {
    "serve": "vue-cli-service serve --mode dev",
    "build": "vue-cli-service build --mode prod",
    "test": "vue-cli-service build --mode test",
    "lint": "vue-cli-service lint"
  }
  // .env.dev
  NODE_ENV = "dev"
  VUE_APP_URL = "/apigateway"
  // .env.test
  NODE_ENV = "production"
  VUE_APP_URL = "http://116.228.196.136:10003/apigateway"
  // .env.prod
  NODE_ENV = "prod"
  VUE_APP_URL = "https://api.yongcheng.com:19090/apigateway"

  // 3、静态服务：可配置命令 "script":{ "server":"live-server ./dist --port=8080" }
  npm install -g live-server / http-server
  live-server / http-server
  ```

		
## 性能优化

  * 路由懒加载：`component: () => import("views/Login.vue")`。
  * 首屏加载：页面内容加载完成前使用 loading、进度条、骨架屏(使用一些空白内容的图形来展示未加载内容) 等方式来提升用户体验。
  * 通过 CDN 引入资源来减小服务器带宽压力：项目依赖会被全部打包到 js 文件而可能导致首屏加载较慢，而使用 cdn 文件代替就不会被打包进去。
  * 使用 v-if 减少不必要的组件加载：显示时才渲染弹窗等组件。
  * 将图片等一些静态资源放到云服务器，减小服务器压力。
  * 需加载三方资源：按需引入 ElementUI 等组件库。
  * 若首屏为登录页则可做成多入口。
  * webpack 开启 gzip 压缩。

      
# 三、路由控制 Router 
> 路由根据不同地址展示不同的页面或数据。前端路由多用于 SPA，通过 hash / HTML5 historyApi 实现而不涉及到服务器。

## 基础使用
> 将组件映射到路由，然后通过 vue-router 渲染

  1. 定义组件：`user.vue`
  2. 定义路由配置：`var routes = []`
  3. 生成路由对象：`var router = new VueRouter({ routes: routes })`
  4. 注入到实例对象：`var app = new Vue({ el: '#app', router, })`
  5. 组件中定义渲染标签：`router-link、router-view`


## 路由配置
  * 命名路由：`name: 'home'`
  * 嵌套路由：`children: [{ }]`
  * 动态路由：`path: '/user/:id'`
  * 命名视图：`components: { }`


  ```js
  // router/index.js
  import Router from 'vue-router'
  Vue.use(Router)

  import user from '@/components/User'
  import home from '@/components/Home'

  // 路由懒加载
  const home = () => import("views/extension/Home.vue")

  export default new Router({    
    mode: "history",            // 路径模式 
    base: process.env.BASE_URL  // 基路径(默认'/')           
    linkActiveClass: 'active',  // 链接激活时的class

    // history 模式下滚动行为，路由切换时页面滚动到指定位置
    scrollBehavior (to, from, savedPosition) { 
      if(savedPosition){  
          return savedPosition  // 原位置
      }else{
          return {x: 0,y: 0};   // 顶部
      } 
      
      if(to.hash){             // 锚点定位
          return {selector: to.hash} 
      }
    },
    
    routes: [     // 路由配置
      {
        title: '登录页',
        alias: '/b',       // 路径别名
        path: '/', 
        name: 'Index',
        redirect: '/home', // 重定向(解决首次进入页面时不显示内容的问题)
        component: home
      }, 
      {
        path:"/home",     // 静态路由
        name: 'home',
        component: home,
        children: [{       // 嵌套路由(须先进 home) 
            path: "/phone",
            name: "phone",
            component: phone
        }]
      },                  
      {
        name: "user",    
        path: '/user/:id',  // 动态路由(指定参数形式)   
        component: user,    
      },
      {
        path: '/name',
        components: {      // 命名视图(同时渲染多个组件)
          default: Foo,
          a: Bar,
          b: Baz
        }
      }
    ]
  }) 
  ```


## 路由模式

### 两种模式
> 应用于浏览器的历史记录站，在当前已有的 back、forward、go 的基础上提供了对历史记录进行修改的功能。只是当它们执行修改时，虽然改变了当前URL，但浏览器不会立即向后端发送请求。

  * __hash__：默认模式，浏览器地址中的 # 及其后面的字符串称为 hash，可以通过 window.location.hash 读取，它用来模拟一个完整 URL 但不会被包括在 HTTP 请求中，改变时不会重新加载页面。
  * __history__：利用了 HTML5 新增方法，pushState()、replaceState() 用来对浏览器历史记录栈进行修改，popState 事件用来监听到状态变化。	

       
### 404 错误
  * __hash 模式__：只有 # 前面的内容会被包含在请求中。因此对于后端来说，即使没有做到对路由的全覆盖也不会返回404错误
  * __history 模式__：前端的url必须和实际向后端发起请求的url 一致，比如 /book/id ，如果后端缺少对该路由的处理则返回 404 错误 

  
### 注意问题
  <div style="text-indent: 2em">app 开发时一般需要分享页面到第三方 app，但部分 url 不允许带 #。去除 # 需要使用 history 模式，但是有一个问题：在访问二级页面时，刷新操作则会出现 404 错误，那么就需要和后端一起配置 apache 或是 nginx url 重定向，重定向到你的首页路由上即可。</div>
            

## 路由跳转

### 组件标签
  * __跳转标签__
    * 样式：`<router-link tag="li"、active-class="active" replace></router-link>`。
    * 导航：普通路由 `to= "/path"`、动态路由：`to="/user/12"、:to="{ name: 'user', params/query: { id: 1 } }"`。
  * __渲染视图__
    * 标签：`<router-view name="user"></router-view>`。
    * 包裹：缓存 `keep-alive`、过渡样式 `transition`。
  

### JS 编程式导航
  ```js
  export default {
    methods: {
      to_home(){   // 字符串 和 对象
        this.$router.push("home")      
        this.$router.push({ path: '/login?url=' + this.$route.path })  
      },
      to_user(){  // 命名路由 和 查询参数
        this.$router.push({ name: 'user', params: { id: 123 }})  
        this.$router.push({ path: 'user', query: { id: 123 }})   
      },
      to_other(){
        this.$router.go(n)         // 前进
        this.$router.back()        // 后退
        this.$router.forward()
        this.$router.replace()     // 替换掉当前的history记录
      },
      get_obj(){
        console.log(this.$route)   // 路由对象(包括 path、params 等)
      }
    }   
  }
  ```


## 动态路由传参
  * 方式
    * __path__：`to="/user/12"`
    * __params__：`:to="{ name: 'user', params: { id: 1 } }"`
    * __query__：`:to="{ name: 'user', query: { id: 1 } }"`
  * 注意
    * 通过 path/params 传参时，需要在路由配置中指定参数形式 `path: '/user/:id'`。
    * 通过 params/query 传参时，参数必须是对象格式而且绑定 to 属性添加 `v-bind:`。
    * 跳转后的组件中获取参数 `this.$route.params/query.id`。


## 导航钩子
> 用于路由跳转时执行任务或不满足条件时取消跳转。

### 分类
  * __全局钩子__：beforeEach，常用于登录权限等验证。
  * __路由钩子__：beforeLeave / beforeEnter。
  * __组件钩子__：beforeRouteLeave 用于清除计时器、提示保存内容、存储信息等，beforeRouteEnter 用于在跳转完成前获取数据。

    
### 参数
  * __to__：目标路由对象。
  * __from__：当前路由对象。
  * __next__：必须调用的函数，否则就是死循环。
    * `next()`：执行下一个钩子，如果钩子全部执行完成则导航状态为 confirmed。
    * `next(false)`：中断当前导航，即重置到 from 路由对应的地址。
    * `next('/')、next({path: '/'})`：中断掉当前导航并跳转到新地址。
    * `next(error)`：如果传入一个 Error 实例，则导航被终止并会将错误传递给 router.onError() 注册的回调。



# 四、异步通信 Axios

## 基础使用
  ```js
  this.$axios.get('/user?id=12')
    .then( res => { })
    .catch( err => { })

  let prames = Object.assign({}, this.form_data)
  this.$axios({ 
    url: url,
    method: 'post',
    data: this.$qs.stringify(prames)
  }).then((res)=>{
    console.log(res)
  })

  // formData 形式上传数据
　let formData = new FormData();
  formData.append('file', file);    
　axios.post('url', formData, {
　　headers: { 'Content-Type': 'multipart/form-data' }
　})
  ```


## 请求 API
  * 请求：`axios(config)、axios(url, [config])`
  * 并发：`axios.all(arr)、axios.spread(callback)`
  * 别名：`axios.get(url, [config])、axios.post(url, [data], [config])`
  

## 自定义配置  

### 封装 axios

  ```js
  // axios.js
  import axios from 'axios'
  import router from '@/router'
  import store from './store'
  import { crypto, random, randomWord } from './assets/js/tool.js';

  // 创建axios实例
  const service = axios.create({
    baseURL: process.env.VUE_APP_URL,
    // timeout: 10000,            // 请求超时时间
    responseType: "json",
  })

  // 请求拦截器
  service.interceptors.request.use(
    config => {
      // 1：加密数据
      if(config.headers.level == 1){  
          const KP = {
            key: randomWord(true, 16, 16),  // 秘钥
            iv: randomWord(true, 16, 16)    // 偏移量
          };
          config.data = crypto.AESEnc(JSON.stringify(config.data), KP.key)

          Object.assign(config.headers, {
            "Content-Type": "application/json;charset=utf-8",
            dataFormat: "json",
            authMethod: "no",
            signAlgo: "NO",
            transAlgo: "AES",
            shareKey: KP.key,
            timestamp: new Date().getTime(),
            nonce: String(random(0, 100))
          })
      }
          
      // 让每个请求携带 token
      if (store.getters.token) {
          config.data = Object.assign({ token: store.getters.token }, config.data)
      }
  
      // 设置表头  'application/json'
      config.headers['Content-Type'] = 'application/json;charset=utf-8'

      // 异步请求的取消操作
      let CancelToken = axios.CancelToken;  
      let cancel
      config.cancelToken = new CancelToken( c => {
          cancel = c;
      })
      cancel()

      // 状态更新
      store.commit('showLoad', { status: 1, msg: "" })
      return config
    },
    error => {
      store.commit('showLoad', { status: 2, msg: "" })
      return Promise.reject(error)
    }
  )
  // 响应拦截器
  service.interceptors.response.use(
    response => {
      // 解密返回数据
      response.data = JSON.parse(crypto.AESDec(response.data, response.config.headers.shareKey))

      if(response.data.CResultCde == "0000"){
          store.commit('hideLoad')
      }else{
          store.commit('showLoad', { status: 2, msg: response.data.CResultMsg })
      }
      return Promise.resolve(response)
    },
    error => {
      store.commit('showLoad', { status: 2, msg: "" })
      return Promise.reject(error)
    }
  )
  // 导出对象
  export default service

  // 导出方法
  export function indexQRCode(data){  // 不加密
    return _request('/getHomePageQRcode', 'post', data, { level: 2 })
  }
  export function getProduct(data){   // 加密
    const headers = { 
      level: 1,     
      module: "prodAction", 
      method: "getProductInfoList"
    }
    return _request('', 'post', data, headers)
  }

  // 资料上传
  export function batchFileUpload(data, progressfn){
    return form_upload('/UploadController', data, progressfn)
  }
  
  // 文件数据上传
  function form_upload(url, data, progressfn){
    // formData 需要纯净的 axios 请求
    const formAxios = axios.create({
      baseURL: process.env.VUE_APP_URL,
      responseType: "json",
      // timeout: 10000,
    })

    return formAxios({
      method: 'post',
      data: data,
      headers: {
        'Content-Type': "multipart/form-data"
      },
      onUploadProgress(progressEvent) { // 原生获取上传进度的事件
        // lengthComputable 主要表明总工作量和已完成工作是否可以被测量
        progressEvent.lengthComputable && progressfn(progressEvent);
      }
    })
  }

  // 表单数据上传
  function _request (url, methods, data, headers = { level: 1 }, params = {}) {
    return service({
      method: methods,
      url: headers.level == 1 ? "/api" : url,
      data: data,
      params: Object.assign(params),
      headers: Object.assign(headers),
      responseType: headers.level == 1 ? "text" : "json"
    })
  }
  ```


### 引用方法
  ```js
  // main.js 全局引用
  import http from './axios'
  Vue.prototype.$axios = http

  // 局部引用
  import { indexQRCode } from '@/axios'
  ```
    

### 加密方法

  ```js
  // tool.js

  // 随机整数
  export function random(min, max){
    if(max == null){
      max = min;  
      min = 0;
    }
    return min + Math.floor(Math.random()*(max-min+1))
  }

  /*
  ** randomWord 产生任意长度随机字母数字组合
  ** randomFlag-是否任意长度 min-任意长度最小位[固定位数] max-任意长度最大位
  */
  export function randomWord(randomFlag, min, max){
    var str = "",
    pos = "",
    range = min,
    arr = ['0', '1', '2', '3', '4', '5', '6', '7', '8', '9', 
          'a', 'b', 'c', 'd', 'e', 'f', 'g', 'h', 'i', 'j', 'k', 
          'l', 'm', 'n', 'o', 'p', 'q', 'r', 's', 't', 'u', 'v', 
          'w', 'x', 'y', 'z', 'A', 'B', 'C', 'D', 'E', 'F', 'G', 
          'H', 'I', 'J', 'K', 'L', 'M', 'N', 'O', 'P', 'Q', 'R', 
          'S', 'T', 'U', 'V', 'W', 'X', 'Y', 'Z'];
    // 随机产生
    if(randomFlag){
        range = Math.round(Math.random() * (max-min)) + min;
    }
    for(var i=0; i < range; i++){
        pos = Math.round(Math.random() * (arr.length-1));
        str += arr[pos];
    }
    return str;
  }

  /*
  ** crypto-js 
  ** word：待加密或者解密的字符串
  ** keyStr：AES 加密需要用到的16位字符串的key
  */
  import CryptoJS from 'crypto-js'
  export const crypto = {
    aesKey: 'com.iescp.gate',
    AESEnc (content, key) {
      key = key || this.aesKey;
      key = CryptoJS.enc.Utf8.parse(key) // 加密密钥
      var srcs = CryptoJS.enc.Utf8.parse(content)
      var encrypted = CryptoJS.AES.encrypt(srcs, key, { 
        iv: key, 
        mode: CryptoJS.mode.CBC 
      })
      return encrypted.toString();
    },
    AESDec: function (content, key) {
      key = key || this.aesKey;
      key = CryptoJS.enc.Utf8.parse(key) // 解密密钥
      let decrypted = CryptoJS.AES.decrypt(content, key, { 
        iv: key, 
        mode: CryptoJS.mode.CBC 
      })
      let decryptedStr = decrypted.toString(CryptoJS.enc.Utf8)
      return decryptedStr;
    }
  } 
  ```


# 五、状态管理 Vuex

## 核心概念
> 本质是一个挂载到 vue 实例的全局变量对象 store，它的属性和方法负责管理所有组件的状态及其更新

  * __state__：包含所有应用级别状态的对象 (组件用到的数据)
  * __getters__：派生出 state 所需数据 (计算属性)
  * __mutators__：更新 state 的唯一方式 (同步更新)
  * __actions__：异步提交 mutaions 更新状态 (异步更新)
  * __module__：划分为不同模块管理 (方便维护大型项目)


## 主要特点  

  * 只能应用于 Vue，通过响应式机制更新视图。
  * 单一数据源：全局只有一个 Store 实例。
  * 单向数据流：`View、Action、Mutation、State`。

  <div align="center"> 
    ![Vuex 单向数据流](/images/vue/vuex_base.png)
  </div> 
          
      
## 目录结构
> 如果 store 文件太大则需要分割到单独文件

  ```js
  store
    ├── index.js         // 组装模块并导出store 
    ├── actions.js       // 根级别的 action
    ├── mutations.js     // 根级别的 mutation
    └── modules          // 大型应用时分割到模块
        ├── cart.js      // 购物车模块
        └── products.js  // 产品模块
  ```


## 自定义配置

### 单个文件
  ```js
  // vuex/store.js

  import Vue from "vue";
  import Vuex from "vuex";

  Vue.use(Vuex);

  export default new Vuex.Store({
    // state 状态变量
    state: {
      VIEWID: 0,
      LOADING: {
        status: 0,
        msg: ""
      }
    },
    // state 的计算属性：派生所需状态
    getters: {
      IDNew: state => {  
        return state.ID * 10;
      }
    },
    // 同步更新 state：store.commit('showLoad', { status: 1, msg: "" })
    mutations: {
      viewEntrance(state, id){
        state.VIEWID = id
      },
      showLoad (state, obj){
        state.LOADING = obj
      },
      hideLoad (state){
        state.LOADING = { status: 0, msg: "" }
      }
    },
    // 异步更新 state：this.$store.dispatch('viewEntrance', id)
    actions: {
      // 简写写法
      viewEntrance: ({ commit }, id) => commit('viewEntrance', id),
      // 完整写法
      viewEntrance: ( context, id) => { context.commit('viewEntrance', id) },
      // 异步操作
      entryAsync: ({ commit }, id) => {  
        setTimeout (() => {
            commit('viewEntrance', id)
        }, 1000);
      }
    },
    // 分割成不同模块以方便管理：使用时不再导出以上变量
    modules: {
      a: {
        state: {},
        getters: {},
        mutations: {},
        actions: {},
      },
      b: {}
    }
  })
  ```
  

## 全局引用
  ```js
  // main.js
  import store from './vuex'  
  new Vue({
    el: '#app',
    router,
    store,
  })
  ```


## 组件使用
  ```js
  import { mapState, mapGetters, mapMutations, mapActions } from 'vuex'
  export default {
    data(){
        return{
            localState: 2
        }
    },
    // 普通写法
    computed: {     
        count () {
            return this.$store.state.count;
        },
        isShow(){
            return this.$store.state.isShow;
        }
    },
    // 简洁写法：使用辅助函数返回对象并映射到组件实例
    computed: mapState({  
        count: state => state.count, 
        countAlias: 'count',      // 改变名称
        countPlus(state){         // 使this指向组件对象
            return state.count + this.localState; 
        },
    }),
    computed: mapState(['count', 'isShow']),  // 名称相同
    computed: {
      local(){         // 组件数据
        return this.localState;
      },
      // 使用对象展开运算符将数据混入 computed 对象
      ...mapState([    // 映射 State
        'count',
        'isShow'
      ]),
      ...mapGetters([  // 映射 Getters 
        'countNew'
      ])
    }, 
    methods: {   
      reduce(){     // 有参数时需单独定义
        this.$store.commit('add')           // 同步
        this.$store.dispatch('reduce', 2)   // 异步
      },
      ...mapActions([  // 映射 Mutations
        'add',  
        'tog',
        'addAsync'
      ])
    },
    methods: mapActions(['add'])
    methods: mapMutations(['add'])
  }
  ```


## 简单用法

  ```js
  // store.js
  export default new Vuex.Store({
    state: {
      LOADING: false
    },
    mutations: {
      setState(state, newState) {
        Object.assign(state, newState)
      }
    },
    actions: { }
  })

  // main.js
  Object.assign(Vue.prototype, {
    showLoading: function () {
      store.commit('setState', { LOADING: true })
    },
    hideLoading() {
      store.commit('setState', { LOADING: false })
    }
  })

  // 组件中使用
  this.showLoading()
  this.hideLoading()
  ```


# 六、组件化开发

## 组件实例化
> 组件只有挂载到某个 Vue 实例下才会生效，挂载的本质就是创建一个包含数据、方法和组件的 vue 实例对象。

  ```js
  // 1、直接创建
  var app1 = new Vue({
    el: "#app1",
    data: {}
  })

  // 2、首先创建无挂载点的实例
  var app2 = new Vue({    
    data: {          // 对象
      msg: "app2" 
    }
  })
  app2.$mount('#app2');

  // 3、使用基础 Vue 构造器创建子类
  var app3 = Vue.extend({
    data: function(){   // 函数
      return{ 
        msg:"app3" 
      }
    }
  })    
  new app3().$mount('#app3');
  ```


## 组件定义
> 三种书写方式：__单文件组件__、__template 标签__、__script 标签__。组件必须注册才能生效。组件和指令命名时注意：不能使用标签和关键字、尽量不使用驼峰命名 (因为 webpack 编译后会统一变为小写)，如果有大写字母则使用时必须都改为小写并添加 `-、v-`。

  ```js
  // 单文件组件配置选项：扩展名为 vue
  var myMixin = 
  export default {
    mixins: [        // 混入：扩展组件功能，可看作继承。
      {
        data(){
          return {
            msg:'mixins:msg',
            name:'mixins:name'
          }
        },
        methods: {
          hello() {
            console.log('mixin:hello')
          }
        },
        created() {
          this.hello()
        }
      }
    ],
    directive: {
      // 注册一个局部的自定义指令 v-auto-focus
      autoFocus: {
        bind(el, binding, vnode){ 
          // 创建：指令第一次绑定到元素时调用，比如添加事件监听器等
        },      
        inserted(el){    
          el.focus()
          // 插入：当元素被插入到 DOM
        },      
        update(el, binding, vnode, oldVnode){ 
          // 更新：当元素所在模板更新时
        },     
        componentUpdate(el, binding, vnode, oldVnode){ 
          // 当元素所在模板完成一次更新周期时
        },   
        unbind(el, binding, vnode){          
          // 解除绑定时只调用一次，比如移除 bind 时绑定的事件监听器
        }
      }
    },
    filters: {          // 过滤器：{{user.age | sum}} 
      sum (value) {
        return value == 0;
      }
    },
    components: { // 组件注册：使用时 <my-com>
      MyCom 
    },
    data(){      // 组件数据：组件内必须使用箭头函数，否则 this 不指向当前组件
      return {
        name: "tom"
        age: 20
      }
    },
    computed: { },   // 计算属性：派生所需数据
    methods: { },    // 事件绑定：事件名不区分大小写)
    watch:{          // 监听数据
      user: {        // 深度监听(可监听到对象内部属性变化)
        handler(val, oldval){ },  
        deep: true,     
        immediate: true    
      },  
      "user.name": function(val, oldval){ // 必须加引号  
        console.log(val)  
      },
      $route(to, from){            // 监听子组件路由变化
        console.log(to.name)
      }
    },
    mounted () {    // 生命周期
      this.$root.eventHub.$on('updateDash', this.getList)
      window.addEventListener('scroll', this.handleScroll)
      // 推迟回调到下次 DOM 更新循环之后执行
      this.$nextTick(() => {
        console.log(444);
        console.log(this.$refs['hello']);
      });
    },
    beforeDestroy(){
      this.$root.eventHub.$off('updateDash')
      window.removeEventListener('scroll', this.handleScroll)
    }   
  }

  // 全局
  Vue.component('myCom', { })

  Vue.use(MyPlugin, { someOption: true })

  Object.assign(Vue.prototype, {
    showLoading () {
      store.commit('setState', { LOADING: true })
    },
    hideLoading() {
      store.commit('setState', { LOADING: false })
    }
  })
  Vue.directive("autoFocus ", {
    inserted(el){  el.focus() }
  })

  Vue.filter('formatDate', function (value, fmt){})

  Vue.mixin({})

  Vue.nextTick()
  ```


## 生命周期   
> Vue 实例由创建到销毁的完整运行一次的过程中会触发的函数。

  * __beforeCreate__：初始化非响应式变量，比如添加 Loading。
  * __created__：页面初始化，比如关闭 Loading 和 Ajax 请求。
  * __mounted__：获取和操作 Dom 元素和 Ajax 请求。
  * __updated__：统一处理数据，但可能陷入死循环。
  * __beforeDestroy__：销毁定时器、解绑全局事件、销毁插件对象等操作。

  <div align="center"> 
    ![Vue 生命周期](/images/vue/vue_life.png)
  </div>


## 内置指令
  * 数据渲染：`双层 { }、v-text、v-html`
  * 条件渲染：`v-if、v-else`
  * 循环渲染：
    * 数组：`v-for="(item, index) in arr" :key="index"`
    * 对象：`v-for="(item, key, index) of obj" :key="index"`
  * 事件绑定：`v-on:` 可缩写为 `@`。
    * 绑定：`v-on:click = "todo"、@click = "todo"`
    * 修饰符：`@click.stop.self、@keyup.stop.enter`。阻止冒泡 stop、阻止默认 prevent、执行一次 once。
  * 动态绑定：`v-bind:`，注意可以省略 v-bind 
    * class：`:class=" ['a', 'b']、[ true ? 'a': 'b']、{ 'active': isActive(id) } "`。
    * style：`:style="{color: 'red'}、[{color: 'red'}, {fontSize: '16px'}]"`。
  * 双向绑定
    * 绑定：`v-model、v-model.trim`
    * 修饰符：trim 首尾空格过滤、lazy 监听change事件、number 字符串转为数字。
  * 性能优化
    * 只渲染一次：`v-once`
    * 防止页面闪烁：`v-cloak`
    * 不输出 data：`v-pre`


## 组件通信

### 父子组件通信
  * 子传父：父组件监听 `<child @updateList = "updateList"></child>`，子组件触发自定义事件 `this.$emit("updateList", data)`。
  * 父传子：在子组件中通过 props 定义父组件传递的属性如下：
  ```js
  // 父组件：<child name="Tom" :songs="songs" :age="age"></child>
  export default {
    // 简洁写法
    props: ["name", "songs"]

    // 普通写法
    props: {
      name:{  // 静态数据
        type: String
      }
      songs:{  // 动态数据
        type: Array
      },
      age:{     // 动态数据验证
        type: Number,   
        required: true,
        default(){     // 默认值, 可直接1000 
          return { time: 1000 } 
        },  
        validator(val){  // 自定义验证
          return val == 0
        }
      }
    }
  }
  ```


### 双向绑定
> 同步父子组件数据，注意不要在子组件中修改 props 接收的数据。

  * 普通写法
    * 父组件监听自定义事件：`<com @ut="val => pm = val" :m="pm"></com>`。
    * 子组件触发事件：`props: { m: String }、this.$emit('ut', newVal)`。
  * 使用 sync
    * 父组件：`<child :s.sync="startDate"></child>`。
    * 子组件：`props: { s: String }、this.$emit("update:s", val)`。


### 祖孙组件通信
> __$attrs__ 传递不被 prop 识别的属性 (class和style除外)、__$listeners__：传递组件的事件 (.native除外)。
    
  ```js
  // com.vue
  <child sex="男"></child>

  // child.vue
  <grand v-bind="$attrs" v-on="$listeners"></grand>
  
  // grand.vue
  export default {
    props: ['sex'],
    inheritAttrs: false  // 去掉默认行为
  }
  ```


## template 标签
> 可看作一个不可见的包裹元素，主要用于分组的条件判断和列表渲染。

  ```js
  // 条件渲染：通过 key 实现切换时清空内容。
  <template v-if="loginType === 'username'">
    <label>Username</label>
    <input placeholder="Enter your username">
  </template>
  <template v-else>
    <label>Email</label>
    <input placeholder="Enter your email address">
  </template>

  // 列表渲染
  <ul>
    <template v-for="item in items">
      <li>{{ item.msg }}</li>
      <li class="divider"></li>
    </template>
  </ul>
  ```


## 内置组件
  * __transition__：单个元素/组件的过渡效果
  * __transition-group__：多个元素/组件的过渡效果
  * __component__：支持两个属性 is、inline-template
  * __keep-alive__：保留组件状态或避免重新渲染
  * __slot__：替换 slot 元素实现内容嵌套


## 特殊特性   
  * __key__  
    * 管理可复用的元素：`<router-view :key="$route.fullPath"></router-view>`
    * 减少运算来提高循环性能：`v-for="item in items" :key="item.id"`
  * __is__
    * 使用 v-bind:is 实现动态组件的切换效果
    ```xml
    <!-- change: this.index = (++this.ndex)%3 -->
    <button @change="change"></button>
    <transition name="fade" appear>
        <keep-alive>
            <component :is="views[index]"></component>
        </keep-alive>
    </transition>
    <transition name="move" mode="out-in">
        <!-- include：字符串/正则，只有匹配的组件被缓存 -->
        <!-- exclude：字符串/正则，任何匹配的组件都不会被缓存 -->
        <keep-alive :include="tagList">
            <router-view></router-view>
        </keep-alive>
    </transition>
    ```
    * 用于在 ul 等受限制的 html 元素中绑定组件：`<li is="com1"></li>`
  * __slot__ 
    * 配合 slot 内置组件实现嵌套内容的相应显示
    * 没有 slot 时不显示嵌套内容，多个 slot 时需使用具名插槽 (添加 name 属性)
    ```xml
    <li is="lis_slot">
        <span slot="first" @click="print">first</span>
        <span slot="second">second</span>
    </li>
    <!-- lis_slot.vue -->
    <template>
        <li>
           <slot name="first"></slot> 
           <slot name="second"></slot> 
        </li>
    </template>
    ```
  * __ref__  
    * 获取当前 DOM 元素：`<li ref="cur"、this.$refs.cur></li>`
    * 获取子组件实例对象：`<my-com ref="com"></my-com>、this.$refs.com`



# 七、插件开发
> 插件可以实现很多组件无法实现的需求，它与组件的关系为：插件可以封装组件，组件可以暴露数据给插件。本质是一个必须提供一个公开接口的对象或函数，用来扩展 vue 功能。

## 常规写法
  ```js
  // toast.js
  var Toast = {};        
  Toast.install = function (Vue, options) { 
    Vue.prototype.$msg = 'Hello World';
  }
  module.exports = Toast;      

  // main.js：Vue.use() 会自动调用 install() 并阻止相同插件注册多次
  import Toast from './toast.js';
  Vue.use(Toast);  
  console.log(this.$msg);   // Hello World
  ```


## 模板写法
  ```js
  export default {
    install: function (Vue, options) { //options一般是对象
      // 添加全局方法和属性
      Vue.myProperty = 'Hello Vue',    
      Vue.myGlobalMethod = function () { }, 
              
      // 添加全局资源：指令、过滤器、过渡等
      Vue.directive('focus', function(el, binding, vnode, oldVnode){ })  
      Vue.filter('formatTime', function (value){ })

      // 添加组件选项：混入 Vue 实例本身没有的方法
      Vue.mixin({        
        created(){ },
        methods: { }
      }) 

      // 添加实例方法：调用时使用 this.$get()
      Vue.prototype.$get = function(){ }
    }
  }
  ```


