---
title: Vue 常见场景的解决方案
tags:
  - Vue.js
categories: Vue.js
top: false
keywords:
  - vue
date: 2019-06-10 23:11:50
description: 路由控制、数据加密、数据更新、页面刷新、页面缓存、页面跳转
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


# 二、数据加密

## 安全需求
  <div style="text-indent: 2em;">前后端的传输通过 HTTP 进行传输，也带来了一些安全问题，如果抓包、模拟请求、洪水攻击、参数劫持、网络爬虫等都能够获取请求的数据包。所以项目可能需要在 http 请求中添加安全级别，常用方式如下：</div>

  * 采用 HTTPS 协议
    * 普通的 HTTP 协议是以明文形式进行传输，不提供任何方式的数据加密，很容易解读传输报文
    * HTTPS 协议在 HTTP 基础上加入了 SSL 层，而 SSL 层通过证书来验证服务器身份，并为浏览器和服务器之间的通信加密，保护了传输过程中的数据安全。
  * 对请求参数进行合法性校验
  * 对请求参数进行签名认证，防止参数被篡改
  * 请求隐私接口，利用 token 机制校验其合法性
  * 密钥存储到服务端而非客户端，客户端应从服务端动态获取密钥
  * 对输入输出参数进行加密，客户端加密输入参数，服务端加密输出参数


## 加密算法

### 对称加密算法
> 加密与解密密钥相同，可分为序列算法（对明文的一个单位运算）、分组算法（对明文的一组运算）。

  * `DES`：一种分组密码，以 64 位为分组对数据加密，密钥长度是 56 位。
  * `3DES`：基于 DES 的对称算法，对一块数据用三个不同的密钥进行三次加密，强度更高。
  * `AES`：密码学中的高级加密标准，它采用对称分组密码体制，密钥长度的最少支持为 128、192、256 ，分组长度 128 位，算法应易于各种硬件和软件实现。


### 非对称算法
> 加密密钥与解密密钥不同。

  * `RSA`：目前最有影响力的公钥加密算法，可用于签名和加密。
  * `DSA`：基于整数有限域离散对数难题，主要特点是两个素数公开，只用于签名。


### 散列算法
  * `MD5`：单向加密的消息摘要算法（不可以解密），常用于密码认证、钥匙识别、登陆认证。
  * `SHA1`：一种比 MD5 安全性强的消息摘要算法，主要用于标准的数字签名算法。


### 其它
> 不需要密钥。

  * `Base64`：并非真正的加密算法，是一种用于传输 8bit 字节代码的数据编码方式，可用于 http 传递较长的标识信息。
    


## AES 加密
> 项目要求使用的加密算法。

### 加密流程
> 密钥是用来加密明文的密码，不可以直接在网络上传输。

  <div align="center"> 
    ![Vue 生命周期](/images/vue/crypto.png)
  </div>


### 加密参数
  * `key length`：密钥位数、密码长度。
  * `key`：密钥、密码。
  * `IV`：初始向量（密钥偏移量），加密和解密需要相同值。
  * `mode`：加密模式，分为 ECB、CBC、CFB 等，只有 ECB 由于没有使用 IV 而不太安全。
  * `padding`：填充方式，分为 PKCS5、PKCS7、NOPADDING，加密和解密需要相同的模式。


## vue 前端
> npm i crypto-js -D


### 封装加密算法
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
    range = min,
    arr = ['0', '1', '2', '3', '4', '5', '6', '7', '8', '9', 'a', 'b', 'c', 'd', 'e', 'f', 'g', 'h', 'i', 'j', 'k', 'l', 'm', 'n', 'o', 'p', 'q', 'r', 's', 't', 'u', 'v', 'w', 'x', 'y', 'z', 'A', 'B', 'C', 'D', 'E', 'F', 'G', 'H', 'I', 'J', 'K', 'L', 'M', 'N', 'O', 'P', 'Q', 'R', 'S', 'T', 'U', 'V', 'W', 'X', 'Y', 'Z'];
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
  * crypto-js
  * word：待加密或者解密的字符串
  * keyStr：AES 加密需要用到的16位字符串的key
  * @author: shichuang
  */
  import CryptoJS from 'crypto-js'
  
  // 加密
  function encrypt(data, key, iv){
    key  = CryptoJS.enc.Utf8.parse(key);
    // iv = CryptoJS.enc.Utf8.parse(iv);

    var encrypted = CryptoJS.AES.encrypt(data, key, {
          // iv, mode: CryptoJS.mode.CBC
          mode: CryptoJS.mode.ECB, 
          padding: CryptoJS.pad.Pkcs7
    });
    return encrypted.toString();
  }

  // 解密
  function decrypt(data, key, iv){
    key  = CryptoJS.enc.Utf8.parse(key);

    var decrypt = CryptoJS.AES.decrypt(data, key, {
          // iv, mode: CryptoJS.mode.CBC
          mode: CryptoJS.mode.ECB,
          padding: CryptoJS.pad.Pkcs7
    });
    return CryptoJS.enc.Utf8.stringify(decrypt).toString();
  }

  // AES 对称秘钥加密
  const aes = {
    en: (data) => getAesString(data, KP.key),
    de: (data) => getDAesString(data, KP.key)
  };
  // BASE64
  const base64 = {
    en: (data) => CryptoJS.enc.Base64.stringify(CryptoJS.enc.Utf8.parse(data)),
    de: (data) => CryptoJS.enc.Base64.parse(data).toString(CryptoJS.enc.Utf8)
  };
  // SHA256
  const sha256 = (data) => {
    return CryptoJS.SHA256(data).toString();
  };
  // MD5
  const md5 = (data) => {
    return CryptoJS.MD5(data).toString();
  };

  /**
  * 签名
  * @param token 身份令牌：store.state.token
  * @param timestamp 签名时间戳：new Date().getTime()
  * @param data 签名数据
  */
  const sign = (token, timestamp, data) => {
    // 签名格式： timestamp + token + data (字典升序)
    let ret = [];
    for (let it in data) {
      let val = data[it];
      if (typeof val === 'object' && (!(val instanceof Array) || (val.length > 0 && (typeof val[0] === 'object')))) {
        val = JSON.stringify(val);
      }
      ret.push(it + val);
    }
    // 字典升序
    ret.sort();
    let signsrc = timestamp + token + ret.join('');
    return md5(signsrc);
  };
  export {
    aes,
    md5,
    sha256,
    base64,
    sign
  };
  ```


### 封装 axios
  ```js
  // axios.js
  import axios from 'axios'
  import router from '@/router'
  import store from './vuex'
  import {random, randomWord, aes} from "./tool.js"

  const ajax = axios.create({
    baseURL: process.env.VUE_APP_URL,   // url 前缀
    timeout: 10000,                     // 超时毫秒数
    withCredentials: true               // 携带认证信息 cookie
  });

  // 请求拦截器
  service.interceptors.request.use(
    config => {
      const KP = {
          key: randomWord(true, 16, 20),  // 秘钥
          iv: randomWord(true, 16, 20)    // 偏移量
      };
      // 加密
      config.data = aes.en(config.data)
  
      // 设置表头  'application/json'
      config.headers = {
        "Content-Type": "application/json;charset=utf-8",
        url: config.url,
        method: config.method,
        shareKey: KP.key,
        shareIv: KP.iv,
        timestamp: new Date().getTime(),  // 时间戳
        token: store.state.token,
        requestStr: random(100, 1)
      }

      return config
    },
    error => {
      return Promise.reject(error)
    }
  )


  /**
  * @param url 请求 url
  * @param method get、post
  * @param params 参数
  * @param level 0：无加密，1：参数加密，2：签名+时间戳，默认0
  */
  function _request (url, methods, data = undefined, params = {}, level = 0) {
    return new Promise((resolve, reject) => {
      service({
        method: methods,
        url: url,
        data: data,
        params: Object.assign(params),
        headers: { level }
          
      }).then((response) => {
          return resolve(response.data)
      }).catch((error) => {
          return reject(error)
      })
    })
  }
  ```



# 三、数据更新

## 重置当前组件的数据
  * 表单：`this.form = this.$options.data().form`
  * 组件：`Object.assign(this.$data, this.$options.data())`
    * this.$data：获取当前状态下的 data
    * this.$options.data()：获取该组件初始状态下的 data


## 更新路由变化后的数据
> 路由变化后页面数据并未更新，原因是跳转前后使用同一个组件，vue-router 默认复用组件而没有执行 created 等生命周期钩子。

  * 普通方案：监听 $route 变化来初始化数据 `watch: { '$route': {handler: 'resetData',immediate: true} }`。
  * 简单方案：监听路由地址变化来重新创建组件 `<router-view :key="$route.fullPath"></router-view>`。


# 四、页面刷新

## 实现方法
  * `router.go(0)`：兼容性不好
  * `window.location.reload()`：会清空 vuex 数据
  * `provice + inject`：推荐使用
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


## 数据更新问题
> 更新对象或数组后对应的视图值却不刷新

  * 更新对象：`this.$set(obj, key, value)`
  * 更新数组：`this.$set(arr, index, value)`


# 五、页面缓存

## 组件缓存
> 缓存组件只会在首次进入时执行一次生命周期钩子，再次进入则从缓存中获取数据等

  * 全部缓存：keep-alive 标签包裹 router-view
  * 部分缓存
    * 缓存 `keep-alive include="a, b"`、不缓存 `keep-alive exclude="c, d"`
    * 路由配置 `meta: { cache: true }`，渲染时 `keep-alive v-if="$route.meta.cache"`


## 旧版本缓存
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



# 六、页面跳转
> 只要用户从一个页面切换到另一个页面就会发生 unload 事件，一般用于清除引用，避免内存泄漏。

  ```js
  export default {
    created() {
      window.addEventListener("unload", this.stopTest);
    },
    destroyed() {
      this.stopTest();
      window.removeEventListener("unload", this.stopTest);
    }
  }
  function stopTest() {
    const stop = function(stream) {
      stream && stream.getTracks().forEach(item => item.stop());
    };
    stop(this.audioStream);
    stop(this.videoStream);
  }
  ```






