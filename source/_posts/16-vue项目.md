---
title: Vue 项目开发
tags:
  - Vue.js
categories: Vue.js
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
  export const  scrollAnimation = (currentY, targetY, dom) =&gt; {

    // 计算需要移动的距离
    let needScroll = targetY - currentY
    let _currentY = currentY

    // 注意 window 滚动
    dom = dom ? dom : 'window'
    
    setTimeout(() =&gt; {
        const dist = Math.ceil(needScroll / 10)
        _currentY += dist
        dom.scrollTo(_currentY, currentY)
        // 如果移动幅度小于十个像素，直接移动，否则递归调用，实现动画效果
        if (needScroll &lt; 10 || needScroll &gt; -10) {
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



# 二、UI 框架

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


# 三、常用方法

## 页面参数
  ```js
  export function getUrlKey(name) {
    return decodeURIComponent((new RegExp('[?|&]' + name + '=' + '([^&;]+?)(&|#|;|$)').exec(location.href) || [, ""])[1].replace(/\+/g, '%20')) || null
  }
  ```


## 计算鉴权
  ```js
  import md5 from 'js-md5'

  export function getSign (prameArr) {
    const hashStr = prameArr.join("=&")
    return md5(hashStr)
  }
  ```

## 计算日期
  ```js
  // 日期格式转换
  export function DateFormat (date, fmt = 'yyyy-MM-dd') {
    var o = {
      'M+': date.getMonth() + 1, // 月份
      'd+': date.getDate(), // 日
      'h+': date.getHours(), // 小时
      'm+': date.getMinutes(), // 分
      's+': date.getSeconds(), // 秒
      'q+': Math.floor((date.getMonth() + 3) / 3), // 季度
      'S': date.getMilliseconds() // 毫秒
    }
    if (/(y+)/.test(fmt)) {
      fmt = fmt.replace(RegExp.$1, (date.getFullYear() + '').substr(4 - RegExp.$1.length))
    }
    for (var k in o) {
      if (new RegExp('(' + k + ')').test(fmt)) {
        fmt = fmt.replace(RegExp.$1, (RegExp.$1.length === 1) ? (o[k]) : (('00' + o[k]).substr(('' + o[k]).length)))
      }
    }
    return fmt
  }

  // 获取几 天/年 后的日期
  export function getDateLater(type, num){
    let d = new Date();
    switch(type){
      case "day":
        d.setDate(d.getDate() + num)
        break
      case "year":
        d.setDate(d.getDate())
        d.setFullYear(d.getFullYear() + num)
        break
      default:
        break
    }
    return DateFormat(d)
  }
  ```


## 身份证信息
  ```js
  // 根据身份证号获取信息：性别、出生日期、年龄
  export function getIdentity(id){
      var result = {};
      var _birthDate;
      switch(id.length){
          case 18:
              _birthDate = {
                  year : id.substr(6, 4), 
                  month : id.substr(10, 2), 
                  date : id.substr(12, 2)
              };
              //第 17 位
              result['sex'] = parseInt(id.substr(16, 1))%2==0 ? "106001":"106002"; 
              break;
          case 15:
              _birthDate = {
                  year : "19" + id.substr(6, 2), 
                  month : id.substr(8, 2), 
                  date : id.substr(10, 2)
              };
              // 第 15 位
              result['sex'] = parseInt(id.substr(14, 1))%2==0 ? "106001":"106002";
              break;
          default:
              break;
      }
      var birthDate = new Date(Number(_birthDate.year),Number(_birthDate.month) - 1,Number(_birthDate.date));
      result['birthDay'] = DateFormat(birthDate)
          
      var _currDate = new Date();
      if(Number(_birthDate.month) - 1 &lt; _currDate.getMonth()){
        result['age'] = _currDate.getFullYear() - birthDate.getFullYear();
      }else if(Number(_birthDate.month) - 1 == _currDate.getMonth()){
        if(Number(_birthDate.date) &lt;= _currDate.getDate()){
          result['age'] = _currDate.getFullYear() - birthDate.getFullYear();
        }else{
          result['age'] = _currDate.getFullYear() - birthDate.getFullYear() - 1;
        }
      }else{
        result['age'] = _currDate.getFullYear() - birthDate.getFullYear() - 1;
      }
      return result;
  }
  ```



## 四、插件封装

### 表单验证

  ```js
  // validator.js

  /**
   *  验证
   *     默认规则：v-validator:email.vanow= "['required', 'Mail']"
   *     自定义规则：v-validator:email= "[{required: '', msg: '不可以为空呦'}]"
   * 
   *        v-validator:password= "validatePassword" 
            
            data () {
              return {
                validatePassword: [
                    { required: '', msg: '请输入内容' },
                    { limit: this.limit, msg: '字符长度在 6 和 12 之间' }
                ]
              }
            },
            methods: {
              limit: function () {
                let password = this.$data.password
                return (password.length &gt;= 6 && password.length &lt;= 12)
              }
            }

  * 
  *  方法调用
            check () {
              let checkResult = this.$va.checkAll()
              if (checkResult) {
                console.log('请求接口去跳转页面')
              }
            }
  *
  *  注意：必须设置唯一的 name 属性值
  *  错误：span class="error"、输入框父元素有 error_item 类名时显示  
  * 
  *  引入：main.js
          import Validator from './assets/js/validator'
          Vue.use(Validator)
  */


  // 默认规则正则表
  let regList = {
    required: true,
    idCard: 'function',
    // 英文与数字组成
    Code: /^[A-Za-z0-9]+$/,
    // 英文姓名中间支持有空格，中文姓名支持空格有“·”的匹配
    Name: /^[\u4E00-\u9FA5A-Za-z\s]+(·[\u4E00-\u9FA5A-Za-z]+)*$/,
    // 中英文、下划线、# 号
    Address: /^[\u4E00-\u9FA5A-Za-z0-9_#]{2,200}$/,
    Mobile: /^1[3|4|5|7|8]\d{9}$/,
    Mail: /^([a-zA-Z0-9_\.\-])+\@(([a-zA-Z0-9\-])+\.)+([a-zA-Z0-9]{2,4})+$/
  }
  // 默认规则错误信息
  let msgList = {
    required: '必填',
    idCard: "请输入正确的身份证号",
    Code: '请输入正确的编码',
    Name: '请输入正确的姓名',
    Address: '请输入正确的地址',
    Mobile: '请输入正确的手机号',
    Mail: '请输入正确的邮箱'
  }
  // 规则构造器
  function Rule (ruleName, ruleValue, ruleMsg) {
    this.ruleName = ruleName
    this.ruleValue = ruleValue
    this.msg = ruleMsg
  }
  // 挂在vue实例上面$va
  function createVa (vm, domSelector) {
    let va = {
      ruleListRank: [],
      forms: {},
      checkRule: checkRule,
      checkAll: checkAll
    }
    if (vm.$va) {
      return vm.$va
    } else {
      vm.$va = va
      return vm.$va
    }
  }
  // va构造器挂在vue实例上面$va的forms上面
  function VaForm (el, finalRule) {
    this.dom = el
    this.rules = finalRule
  }
  // 断言函数
  function assert (condition, message) {
    if (!condition) {
      console.error('[validator-warn]:' + message)
    }
  }
  // 挂载va上的方法================================================
  // 校验表单的全部字段
  function checkAll () {
    let checkFlag = true
    let forms = forms

    for (let name of this.ruleListRank) {
      let flag = this.checkRule(this.forms[name].dom, this.forms[name].rules)
      if (flag === false) {
        checkFlag = flag
      }
    }
    return checkFlag
  }
  // 校验表单的一个字段的第一个报错信息
  function checkRule (dom, rules) {
  console.log(rules)

    let ruleCheckers = {
      required: checkEmpty,
      reg: checkReg,
      idCard: checkIdCard
    }
    for (let rule of rules) {
      let {ruleName, ruleValue, msg} = rule
      let checkResult
      if (typeof ruleValue === 'function') {
        checkResult = ruleValue()
      } else {
        checkResult = ruleCheckers[ruleName]
          ? ruleCheckers[ruleName](ruleValue, dom)
          : ruleCheckers.reg(ruleValue, dom)
      }
      if (!checkResult) {
        setError(dom, msg, 'add')
        return false
      }
    }
    setError(dom, '', 'remove')
    return true
  }
  // 表单验证的方法================================================
  // 检测非空
  function checkEmpty (ruleValue, vaForm, va) {
    return vaForm.value.trim() !== ''
  }
  // 检测正则
  function checkReg (ruleValue, vaForm, va) {
    return ruleValue.test(vaForm.value)
  }
  // 验证身份证号
  function checkIdCard(value) {
    value=value.trim();
    if(!value){ return false }

    var ereg
    var Y,JYM;  
    var S,M;  
    var idcard_array = new Array();  
    idcard_array = value.split("");
    var area={11:"北京",12:"天津",13:"河北",14:"山西",15:"内蒙古",21:"辽宁",22:"吉林",23:"黑龙江",31:"上海",32:"江苏",33:"浙江",34:"安徽",35:"福建",36:"江西",37:"山东",41:"河南",42:"湖北",43:"湖南",44:"广东",45:"广西",46:"海南",50:"重庆",51:"四川",52:"贵州",53:"云南",54:"西藏",61:"陕西",62:"甘肃",63:"青海",64:"宁夏",65:"新疆",71:"台湾",81:"香港",82:"澳门",91:"国外"};
    if(value!=""&&value.length!=15&&value.length!=18){
      return false;
    }
    if(value!=""&&area[parseInt(value.substr(0,2))]==null){
      return false;
    }
    
    switch(value.length){
      //15位身份证号校验
      case 15:
        if ((parseInt(value.substr(6,2))+1900) % 400 == 0 || ((parseInt(value.substr(6,2))+1900) % 100 != 0 && (parseInt(value.substr(6,2))+1900) % 4 == 0 )){ 
          ereg=/^[1-9][0-9]{5}[0-9]{2}((01|03|05|07|08|10|12)(0[1-9]|[1-2][0-9]|3[0-1])|(04|06|09|11)(0[1-9]|[1-2][0-9]|30)|02(0[1-9]|[1-2][0-9]))[0-9]{3}$/; 
        } else { 
          ereg=/^[1-9][0-9]{5}[0-9]{2}((01|03|05|07|08|10|12)(0[1-9]|[1-2][0-9]|3[0-1])|(04|06|09|11)(0[1-9]|[1-2][0-9]|30)|02(0[1-9]|1[0-9]|2[0-8]))[0-9]{3}$/; 
        }
        if(!ereg.test(value)){
          return false;	
        }
        break;
      //18位身份证号校验
      case 18:
        if ( parseInt(value.substr(6,4)) % 400 == 0 || (parseInt(value.substr(6,4)) % 100 != 0 && parseInt(value.substr(6,4))%4 == 0 )){ 
          //闰年出生日期的合法性正则表达式 
          ereg=/^[1-9][0-9]{5}(19|([2-9][0-9]))[0-9]{2}((01|03|05|07|08|10|12)(0[1-9]|[1-2][0-9]|3[0-1])|(04|06|09|11)(0[1-9]|[1-2][0-9]|30)|02(0[1-9]|[1-2][0-9]))[0-9]{3}[0-9xX]{1}$/;
        } else { 
          //平年出生日期的合法性正则表达式 
          ereg=/^[1-9][0-9]{5}(19|([2-9][0-9]))[0-9]{2}((01|03|05|07|08|10|12)(0[1-9]|[1-2][0-9]|3[0-1])|(04|06|09|11)(0[1-9]|[1-2][0-9]|30)|02(0[1-9]|1[0-9]|2[0-8]))[0-9]{3}[0-9xX]{1}$/;
        }	
        
        if(ereg.test(value)){//测试出生日期的合法性  
          //计算校验位  
          S = (parseInt(idcard_array[0]) + parseInt(idcard_array[10])) * 7  
          + (parseInt(idcard_array[1]) + parseInt(idcard_array[11])) * 9  
          + (parseInt(idcard_array[2]) + parseInt(idcard_array[12])) * 10  
          + (parseInt(idcard_array[3]) + parseInt(idcard_array[13])) * 5  
          + (parseInt(idcard_array[4]) + parseInt(idcard_array[14])) * 8  
          + (parseInt(idcard_array[5]) + parseInt(idcard_array[15])) * 4  
          + (parseInt(idcard_array[6]) + parseInt(idcard_array[16])) * 2  
          + parseInt(idcard_array[7]) * 1   
          + parseInt(idcard_array[8]) * 6  
          + parseInt(idcard_array[9]) * 3 ;
          
          Y = S % 11;
          M = "F";
          JYM = "10X98765432";
          M = JYM.substr(Y,1);/*判断校验位*/
          if(M == idcard_array[17].toUpperCase()){
            return true; /*检测ID的校验位false;*/  
          }  
          else {
            return false;  
          }
        }
        else {
          return false;  
        }
        break;
      default:
        return true;
    }
  }



  /**
   * 设置错误的方法
   * @param {*} dom 当前dom元素
   * @param {*} msg 报错信息
   * @param {*} type 是添加报错信息还是移除
   */
  function setError (dom, msg, type) {
    if (dom.parentNode.classList) {
      if (type === 'add') {
        dom.parentNode.classList.add('error_item')
      } else {
        dom.parentNode.classList.remove('error_item')
      }
    }
    dom.nextElementSibling.innerHTML = msg
  }

  let installed = false
  function plugin (Vue, options) {
    if (installed) {
      assert(installed, 'already installed')
      return
    }
    Vue.directive('validator', {
      bind: function (el, binding, vnode) {
        let vm = vnode.context  // 基本的校验规则
        let finalRule = []      // 最终的校验规则
        let domSelector = binding.arg === undefined ? el.getAttribute('name') : binding.arg
        let options = binding.value ? binding.value : [] // 特殊配置（允许非空，编辑新增共用等）
        assert(domSelector, 'not set name or binding.arg')
        let va = createVa(vm, domSelector)  // 单例模式创建va，绑定在vm上
        va.ruleListRank.push(domSelector)   // 表单检验的顺序

        for (let option of options) {
          if (typeof option === 'object') {
            // 用户自定义的校验规则
            let ruleName = Object.keys(option)[0]
            let ruleValue = option[ruleName] ? option[ruleName] : regList[ruleName]
            assert(ruleValue, domSelector + " selector's " + ruleName + ' not set verification mode')
            finalRule.push(new Rule(ruleName, ruleValue, option.msg))
          } else {
            // 配置项定义的校验规则
            if (regList[option]) {
              finalRule.push(new Rule(option, regList[option], msgList[option]))
            }
          }
        }

        //需要立即校验的框
        if(binding.modifiers.vanow){
          el.addEventListener('blur', function(){
            checkRule(el, finalRule)
          })
        }

        let vaForm = new VaForm(el, finalRule)
        va.forms[domSelector] = vaForm
      }
    })
    installed = true
  }
  // 自动注册vue
  if (typeof window !== 'undefined' && window.Vue) {
    window.Vue.use(plugin)
  }
  export default {
    install: plugin
  }
  ```








