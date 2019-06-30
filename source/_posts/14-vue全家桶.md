---
title: Vue 全家桶系列
tags:
  - Vue.js
categories: Vue.js
top: false
keywords:
  - vue
date: 2019-04-14 17:14:11
description: 路由控制 Router、异步通信 Axios、状态管理 Vuex
---

# 一、路由控制 Router 

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

### CSS 标签跳转
  * __跳转标签__：`router-link`
    * 样式：tag="li"、active-class="active"、replace
    * 导航
      * 普通路由：`to= "/path"
      * 动态路由：to="/user/12"、:to="{ name: 'user', params/query: { id: 1 } }"
  * __渲染视图__：`router-view`
    * 命名：name="user" 属性
    * 缓存：keep-alive  包裹
    * 样式：transition  包裹
  

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
    * 通过 path/params 传参时，需要在路由配置中标识指定参数形式 `path: '/user/:id'`
    * 通过 params/query 传参时，参数必须是对象格式而且绑定 to 属性添加 `v-bind:`
    * 跳转后的组件中获取参数 `this.$route.params/query.id`


## 导航钩子
> 用于路由跳转时执行任务或不满足条件时取消跳转

### 分类
  * __全局钩子__：beforeEach，常用于登录权限等验证
  * __路由钩子__：beforeLeave / beforeEnter 
  * __组件钩子__：
    * beforeRouteLeave，用于 清除计时器, 提示保存内容, 存储信息等
    * beforeRouteEnter：用于在跳转完成前获取数据

    
### 参数
  * __to__：目标路由对象
  * __from__：当前路由对象
  * __next__：必须调用的函数，否则就是死循环
    * `next()`：执行下一个钩子，如果钩子全部执行完成则导航状态为 confirmed
    * `next(false)`：中断当前导航，即重置到 from 路由对应的地址
    * `next('/')、next({path: '/'})`：中断掉当前导航并跳转到新地址
    * `next(error)`：如果传入一个 Error 实例，则导航被终止并会将错误传递给 router.onError() 注册的回调



# 二、异步通信 Axios

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
　　this.$axios.post('url', formData, {
　　　　headers:{  
　　　　   'Content-Type':'multipart/form-data' 
　　　　}
　　})

  // 前端使用 qs 模块
  var qs = require('qs');
  this.$axios.post('/foo', qs.stringify({id: 12}))

  // Node环境使用 querystring 模块
  var querystring = require('querystring');
  this.$axios.post('url', querystring.stringify({f:'a'}));
  ```


## 请求 API
  * 请求：`axios(config)、axios(url, [config])`
  * 并发：`axios.all(arr)、axios.spread(callback)`
  * 别名：`axios.get(url, [config])、axios.post(url, [data], [config])`
  

## 自定义配置  

```js
// axios.js
import axios from 'axios'
import router from '@/router'
import store from './vuex'

// 创建axios实例
const service = axios.create({
    baseURL: process.env.VUE_APP_URL + "apigateway",
    // timeout: 10000,            // 请求超时时间
    responseType: "json",
})

// 请求拦截器
service.interceptors.request.use(
    config => {
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
export function indexQRCode(data){
    return _request('/pageSkipping/gas/getHomePageQRcode', 'post', data)
}
function _request (url, methods, data = undefined, params = {}) {
    return new Promise((resolve, reject) => {
        service({
        method: methods,
        url: url,
        data: data,
        params: Object.assign(params)
        }).then((response) => {
        return resolve(response.data)
        }).catch((error) => {
        return reject(error)
        })
    })
}

// main.js 全局引用
import http from './axios'
Vue.prototype.$axios = http

// 局部引用
import { indexQRCode } from '@/axios'
```
      


# 三、状态管理 Vuex

## 核心概念
> 本质是一个挂载到 vue 实例的全局变量对象 store，它的属性和方法负责管理所有组件的状态及其更新

  * __state__：包含所有应用级别状态的对象 (组件用到的数据)
  * __getters__：派生出 state 所需数据 (计算属性)
  * __mutators__：更新 state 的唯一方式 (同步更新)
  * __actions__：异步提交 mutaions 更新状态 (异步更新)
  * __module__：划分为不同模块管理 (方便维护大型项目)


## 单向数据流  
  <div style="text-indent: 2em">通过 vue Devtools 调试工具可以轻松查看状态变化，用户界面负责触发动作 Action 进而改变对应状态 State，从而反映到视图 View 上，所以 State 数据的流向为：`View --> Actions --> mutators --> State --> View`</div>

  <div align="center"> 
    ![Vuex 实现原理](/images/vue/vuex_base.png)
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
        },
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
        ]),
    },
    methods: mapActions(['add'])
    methods: mapMutations(['add'])
  }
  ```





