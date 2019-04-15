---
title: Vue 项目开发
tags:
  - Vue
categories: Vue
top: false
keywords:
  - vue
date: 2019-04-14 22:11:50
description: 常见场景、ElementUI
---

# 一、常见场景

## 路由控制
> Token 是包含用户名、有效期和某些专有信息并通过共享密钥加密的信息字符串

### 登录验证
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
  router.beforeEach((to, from, next) => {
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
    config => {
        if (store.state.token) {  
            config.headers['Token'] = store.state.token
        }
        return config;
    })
  axios.interceptors.response.use(
    error => {
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


### 权限控制
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
        component: () => import('@//components/login')
      },
      {
        path: '*',
        name: '404',
        component: () => import('@//components/404')
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
    data.forEach((item)=>{
        let menu = Object.assign({}, item)
        menu.component = () => import(`@/components/${menu.component}.vue`)
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


## 全局方法

### CommonJS 方法
> 利用 CommonJS 思想单独封装，然后在组件中引用

  ```js
  // 引用
  import { myMethod } from 'assets/js/tool.js'
  myMethod()
  ```


### 全局注册
> 在组件中直接使用

  ```js
  // 注册
  Vue.protytpe.$myMethod = function(options) {
      console.log("this is my method");
  }

  // 使用
  this.myMethod()
  ```

## 页面滚动
> 垂直滚动到页面指定位置

  ```js
  export const  scrollAnimation = (currentY, targetY, dom) => {

    // 计算需要移动的距离
    let needScroll = targetY - currentY
    let _currentY = currentY

    // 注意 window 滚动
    dom = dom ? dom : 'window'
    
    setTimeout(() => {
        const dist = Math.ceil(needScroll / 10)
        _currentY += dist
        dom.scrollTo(_currentY, currentY)
        // 如果移动幅度小于十个像素，直接移动，否则递归调用，实现动画效果
        if (needScroll &gt; 10 || needScroll &lt; -10) {
            scrollAnimation(_currentY, targetY, dom)
        } else {
            dom.scrollTo(currentY, targetY)
        }
    }, 10)
  }
  ```

## 页面刷新
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


## 组件数据

### 组件数据重置
  * 表单：`this.form = this.$options.data().form`
  * 组件：`Object.assign(this.$data, this.$options.data())`
    * this.$data：获取当前状态下的 data
    * this.$options.data()：获取该组件初始状态下的 data


### 数据更新问题
> 路由变化后页面数据不更新，原因是跳转前后使用同一个组件，vue-router 默认复用组件而没有执行 created 等生命周期钩子

  * 普通方案
    * 监听 $route 变化来初始化数据 
    * `watch: { '$route': {handler: 'resetData',immediate: true} }`
  * 简单方案
    * 监听路由地址变化来重新创建组件  
    * `router-view :key="$route.fullpath"`


## 组件缓存				
> 缓存组件只会在首次进入时执行一次生命周期钩子，再次进入则从缓存中获取数据等

  * 全部缓存：keep-alive 标签包裹 router-view
  * 部分缓存
    * 缓存 keep-alive include="a, b"、不缓存 keep-alive exclude="c, d"
    * 路由配置 meta: { cache: true }，渲染时 keep-alive v-if="$route.meta.cache"






# 二、ElementUI

## el-input
  * 监听回车事件：`@keyup.enter.native="doSearch"`
  * 监听输入值
    * `v-on:input="doSearch"`
    * 封装 input 之后无法直接监听原生事件，需要添加修饰符
  * 获取改变前的数据
    * @change 方法一般传递参数之后就会覆盖原本的数据
    * 两种方案
      * `el-input @change="value => changeVal(value, scope.row)"`
      * `el-input @change="changeVal($event, scope.row, scope.$index)"`


 








