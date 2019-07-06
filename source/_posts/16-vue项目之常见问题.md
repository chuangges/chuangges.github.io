---
title: Vue 项目之常见问题的解决方案
tags:
  - Vue.js
categories: Vue.js
top: false
keywords:
  - vue
date: 2019-04-14 22:11:50
description: 常见问题的解决方案
---

# 一、路由控制
> Token 是包含用户名、有效期和某些专有信息并通过共享密钥加密的信息字符串

## 登录验证
  1. 客户端发送用户名和密码请求登录，服务端收到进行验证
  2. 服务端验证成功后生成唯一的 Token 存储在数据库并返回给客户端
  3. 客户端收到 Token 后通过 Cookie、Vuex 等方式存储
  4. 客户端每次向服务端请求资源时都携带 Token 
  5. 服务端收到请求后去验证 Token，验证成功则返回数据


  ```js
  // 前端路由验证
  const router = new VueRouter({
      routes: [{
          path: '/',
          component: require('./views/Home'),
          meta: { requiresAuth: true  }
      }]
  })
  router.beforeEach((to, from, next) =&gt; {
      if (to.meta.requireAuth) {    
          if (store.state.token) {  
              next();
          } else {
              // 记录目标路由，用于登录成功后直接跳转
              next({
                  path: '/login',
                  query: {redirect: to.fullPath}
              })
          }
      }else{
          next()
      }
  })

  // 服务端验证
  axios.interceptors.request.use(
    config =&gt; {
        if (store.state.token) {  
            config.headers['Token'] = store.state.token
        }
        return config;
    })
  axios.interceptors.response.use(
    error =&gt; {
        if (error.res.status == 401) {
            // 清除 token 信息并跳转到登录页面
            store.commit("token_clear");
            router.replace({
                path: 'login',
                query: {redirect: router.currentRoute.fullPath}
            })
        }
        return Promise.reject(error.response.data)   
    })
  ```


## 权限控制
> 路由表一般由 Vuex 管理

  1. 初始化路由时挂载登录、注册等公共组件
  2. 用户登录后，客户端获取服务器返回的路由数据
  3. 客户端触发 vuex 相关方法生成用户可访问的路由表
  4. 动态挂载路由方法 router.addRoutes 
        

  ```js
  // 前端初始化路由
  export default new Router({
    routes: [
      {
        path: '/login',
        name: 'login',
        component: () =&gt; import('@//components/login')
      },
      {
        path: '*',
        name: '404',
        component: () =&gt; import('@//components/404')
      }
    ]
  })

  // 后端返回的路由数据 addRouter
  [
    {
      name: "home",
      path: "/",
      component: "home"
    },
    {
      name: "home",
      path: "/userinfo",
      component: "userInfo"
    }
  ]

  // vuex 添加路由表
  const mutations = {
    [ADD_ROUTES](state, addrouter) {
        let routes = []
        generaRouter(routes, addRouter)
        router.addRoutes(routes)  
        router.push('/')
    }
  }
  const actions = {
    add_Routes({commit}, addrouter) {
        commit(ADD_ROUTES, addrouter)
    }
  }

  function generaRouter(routers, data){
    data.forEach((item)=&gt;{
        let menu = Object.assign({}, item)
        menu.component = () =&gt; import(`@/components/${menu.component}.vue`)
        if(item.children){
            menu.children = []
            generaRouter(menu.children,item.children)
        }
        routers.push(menu)
    })
  }

  // main.js 用户手动刷新页面时重新新增路由
  if (sessionStorage.getItem('user')) {
      let routes = JSON.parse(sessionStorage.getItem('routes'))
      store.dispatch("add_Routes", routes)
  }
  ```



# 二、组件数据问题

## 重置组件数据
  * 表单：`this.form = this.$options.data().form`
  * 组件：`Object.assign(this.$data, this.$options.data())`
    * this.$data：获取当前状态下的 data
    * this.$options.data()：获取该组件初始状态下的 data


## 更新路由变化后的数据
> 路由变化后页面数据不更新，原因是跳转前后使用同一个组件，vue-router 默认复用组件而没有执行 created 等生命周期钩子。

  * 普通方案
    * 监听 $route 变化来初始化数据 
    * `watch: { '$route': {handler: 'resetData',immediate: true} }`
  * 简单方案
    * 监听路由地址变化来重新创建组件  
    * `router-view :key="$route.fullPath"`


# 三、页面缓存问题

## 组件缓存
> 缓存组件只会在首次进入时执行一次生命周期钩子，再次进入则从缓存中获取数据等

  * 全部缓存：keep-alive 标签包裹 router-view
  * 部分缓存
    * 缓存 keep-alive include="a, b"、不缓存 keep-alive exclude="c, d"
    * 路由配置 meta: { cache: true }，渲染时 keep-alive v-if="$route.meta.cache"


## 旧版本打包文件的缓存
> 浏览器缓存会导致 vue 打包的文件偶尔会出现不能更新最新的打包代码，因此在打包的文件名中添加一个版本号以便浏览器能区分。

  ```js
  // vue.config.js：解决 js 缓存问题
  const Timestamp = new Date().getTime();
  module.exports = {
    configureWebpack: {    // webpack 配置
      output: {            // 输出重构，打包编译后的文件名称：模块名称.版本号.时间戳
        filename: `[name]-${process.env.VUE_APP_Version}-${Timestamp}.js`,
        chunkFilename: `[name]-${process.env.VUE_APP_Version}-${Timestamp}.js`
      },
    },

    // 其他写法
    configureWebpack: (config) => { 
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
    }
  }

  /**
   * @desp nginx 配置：不缓存 index.html 
   * @desp no-cache、no-store 可以只设置一个 
   * 
   * no-cache：浏览器会缓存，但刷新页面或者重新打开时会请求服务器
   * no-store：浏览器不缓存，刷新页面需要重新下载页面
   * 
  **/
  location = /index.html {
    add_header Cache-Control "no-cache, no-store";
  }
  ```


# 四、刷新页面功能

## 实现方法
  * __router.go(0)__：兼容性不好
  * __window.location.reload()__：会清空 vuex 数据
  * __provice + inject__：推荐使用
    1. 根组件定义
      ```js
      // router-view v-if="isRouterAlive"

      export default {
        name: 'App',
        provide (){
          return {
            reload: this.reload
          }
        },
        data(){
          return {
            isRouterAlive:true
          }
        },
        methods:{
          reload (){
            this.isRouterAlive = false
            this.$nextTick(function(){
                this.isRouterAlive = true
            })
          }
        }
      }
      ```
    2. 子组件引用
      ```js
      export default {
        inject: ['reload'],
        method: {
            fn(){
                this.reload()
            }
        }
      }
      ```


## 数据丢失问题
> 刷新网页后 vuex 数据丢失的解决方案  

  ```js
  created(){
    if (sessionStorage.getItem("store") ) {
        this.$store.replaceState(Object.assign({}, this.$store.state, JSON.parse(sessionStorage.getItem("store"))))
    }

    window.addEventListener("beforeunload",()=>{
        sessionStorage.setItem("store", JSON.stringify(this.$store.state))
    })
  }
  ```


# 五、文件预览和下载

## 文件下载功能
> ios 设备上的页面指向下载地址后会直接预览，返回 vue 页面时会丢失 vuex 数据而导致页面报错

  ```js
  function download (fileUrl) {

    // ios 
    if (/(iP)/g.test(navigator.userAgent)) {
      // 打开新页面
      let routeData = this.$router.resolve({
        path: "/new_page",
        query: { "fileUrl": fileUrl }
      });
      window.open(routeData.href, '_blank');
      return false
    }
    
    const isChrome = navigator.userAgent.toLowerCase().indexOf('chrome') > -1;
    const isSafari = navigator.userAgent.toLowerCase().indexOf('safari') > -1;

    // Chrome、Safari
    if (isChrome || isSafari) {
        var link = document.createElement('a');
        link.href = fileUrl;

        if (link.download !== undefined) {
            var fileName = fileUrl.substring(fileUrl.lastIndexOf('/') + 1, fileUrl.length);
            link.download = fileName;
        }

        if (document.createEvent) {
            var e = document.createEvent('MouseEvents');
            e.initEvent('click', true, true);
            link.dispatchEvent(e);
            return true;
        }
    }

    if (fileUrl.lastIndexOf('.pdf') == -1) {
      fileUrl += '.pdf';
    }
    
    window.open(fileUrl, '_blank');
    return true;
  }

  // new_page.vue：页面为空
  export default {
    created(){
        let fileUrl = this.$route.query.fileUrl

        if(fileUrl){
            if (fileUrl.lastIndexOf('pdf') === -1) {
                fileUrl += '.pdf';
            }
            window.location.href = fileUrl
        }    
    }
  }
  ```



# 六、UI 框架

## ElementUI

### el-input
  * 监听回车事件：`@keyup.enter.native="doSearch"`
  * 监听输入值
    * `v-on:input="doSearch"`
    * 封装 input 之后无法直接监听原生事件，需要添加修饰符
  * 获取改变前的数据
    * @change 方法一般传递参数之后就会覆盖原本的数据
    * 两种方案
      * `el-input @change="value =&gt; changeVal(value, scope.row)"`
      * `el-input @change="changeVal($event, scope.row, scope.$index)"`


## Vux

### 引用
  * 安装：`cnpm i vux less less-loader vue-loader@14.2.2 -D`
  * 注意：vue-loader 版本号必须加上，否则报错
  * 配置
    ```js
    // vue.config.js
    module.exports = {
      configureWebpack: config => {
        require('vux-loader').merge(config, {
          options: {},
          plugins: ['vux-ui']
        })
      },
    }
    ```
  * 样式
    ```scss
    // App.vue：style lang="less"
    @import '~vux/src/styles/index.less';
    ```
  * 点击延迟
    ```js
    // main.js：添加 Fastclick 移除移动端点击延迟
    import FastClick from 'fastclick'
    FastClick.attach(document.body)
    ```

















