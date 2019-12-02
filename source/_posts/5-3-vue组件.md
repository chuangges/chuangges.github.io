---
title: Vue 组件化开发
tags:
  - Vue.js
categories: Vue.js
top: false
keywords:
  - vue
date: 2019-06-10 18:26:43
description: 实例对象、全局方法、组件编写、插件开发
---

# 一、实例对象
> 一个包含数据、方法和组件的 vue 实例对象

## 初始化
  ```js
  // 1、直接创建
  var app1 = new Vue({
    el: "#app1",
  })

  // 2、首先创建无挂载点的实例
  var app2 = new Vue({    
    data: {          // 对象
        msg:"app2" 
    }
  })
  app2.$mount('#app2');

  // 3、使用基础 Vue 构造器创建"子类"
  var app3 = Vue.extend({
    data: function(){   // 函数
        return{ 
            msg:"app3" 
        }
    }
  })    
  new app3().$mount('#app3');
  ```


## 配置选项
> 如果对 data、created 等属性使用箭头函数，this 将是 undefined

  ```js
  var app = new Vue({
    el: "#app",        // 挂载元素
    data: {            // 挂载数据
        user: {
            name: "tom"
            age: 20
        }  
    },
    computed: { },     // 计算属性：派生所需数据
    methods: { },      // 事件绑定：事件名不区分大小写)
    components: {      // 组件注册(组件名不要和已有标签冲突)
    },
    watch:{           // 监听数据
        
        user: {       // 深度监听(可监听到对象内部属性变化)
            handler: function(val, oldval){ },  
            deep: true,     
            immediate: true    
        },  
        "user.name": function(val, oldval){  // 必须加引号  
            console.log(val + "a")  
        },
        inputVal:{    // 组件创建和 input 变化时获取列表
            handler: 'getList',
            immediate: true  
        },
        $route(to, from){         // 监听子组件路由变化
            console.log(to.name)
        }
    },
    filters: {          // 过滤器：{{user.age | sum}} 
        sum: function (value) {
            return value == 0;
        }
    },
    mounted () {       // 监听
        this.$root.eventHub.$on('updateDash', this.getList)
        window.addEventListener('scroll', this.handleScroll)
    },
    beforeDestroy(){  // 解绑
        this.$root.eventHub.$off('updateDash')
        window.removeEventListener('scroll', this.handleScroll)
    }
  })
  ```


## 生命周期   
> Vue 实例由创建到销毁的完整运行一次的过程中会触发的函数

### 运行流程
  <div align="center"> 
    ![Vue 生命周期](/images/vue/vue_life.png)
  </div> 


### 常用处理
  * __beforeCreate__：初始化非响应式变量，比如添加 Loading
  * __created__：页面初始化，比如关闭 Loading 和 Ajax 请求
  * __mounted__：获取和操作 Dom 元素和 Ajax 请求
  * __updated__：统一处理数据，但可能陷入死循环
  * __beforeDestroy__：销毁定时器、解绑全局事件、销毁插件对象等操作
	
 
## 常用方法
  * __app.$watch__：监听
  * __app.$set__：添加属性，全局 Vue.set 的别名
  * __app.$delete__：删除属性，全局 Vue.delete 的别名
  * __app.$on__：监听自定义事件
  * __app.$emit__：触发事件
  * __app.$off__：移除事件
  * __app.$mount__：手动挂载
  * __app.$forceUpdate__：重新渲染
  * __app.$nextTick__：将回调延迟到下次 DOM 更新后执行
  * __app.$destroy__：完全销毁实例


## $nextTick
> Vue 数据驱动视图更新是异步的，即修改数据时视图不会立刻更新，而是等同一事件循环中的所有数据变化完成后再统一进行视图更新

  <div style="text-indent: 2em">主线程完成同步环境执行，读取任务队列 并提取队首的任务之后放入主线程中执行，不断重复该操作的过程就是 事件循环。主线程每次读取任务队列操作时是一个事件循环的开始，异步 callback 不可能处在同一事件循环中。而 nextTick 的触发时机：同一事件循环中的代码执行完毕 --> DOM 更新 -> nextTick callback 触发，所以它的 应用场景就是需要在视图更新之后 基于新视图进行操作时，常见如下：</div>
 
  * created 钩子中操作 DOM，因为此时的 DOM 其实并未渲染
  * 在数据变化后执行某个操作，而且该操作需要使用随数据改变而改变的最新 DOM 结构，如获取表格数据更新后的高度


# 二、全局 API
> 指令和组件命名时不能使用标签和关键字，而且尽量不使用驼峰命名 (因为 webpack 编译后会统一变为小写)。如果有大写字母，则使用时必须都改为小写并添加 v-、-
        
## 常用汇总
  ```js
  // 过滤器：必须放在 Vue 实例化前面：{{data | formatDate('yyyy-MM-dd hh:mm:ss')}}
  Vue.filter('formatDate', function (value, fmt) {
      let date = new Date(value);
      let obj = {
          'M+': date.getMonth() + 1, 
          'd+': date.getDate(), 
          'h+': date.getHours(), 
          'm+': date.getMinutes(), 
          's+': date.getSeconds(), 
          'q+': Math.floor((date.getMonth() + 3) / 3), // 季度
          'S': date.getMilliseconds() // 毫秒
      };
      // 匹配年
      if (/(y+)/.test(fmt)) {
          fmt = fmt.replace(RegExp.$1, (date.getFullYear() + '').substr(4 - RegExp.$1.length));
      }
      // 匹配月日 时 分 秒等
      for (let item in obj) {
          if (new RegExp('(' + item + ')').test(fmt)) {
          let str = '' + obj[item];
          fmt = fmt.replace(RegExp.$1, (RegExp.$1.length === 1) ? str : (('00' + str).substr(str.length)));
          }
      }
      return fmt;
  })          


  // 指令：input v-auto-focus
  Vue.directive("autoFocus ", {        
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
  })

  // 方法：组件使用通过 this.setCookie()、this.showLoading()
  Vue.prototype.setCookie = (name, value, expiredays) => {
    var exdate = new Date();　　　　
    exdate.setDate(exdate.getDate() + expiredays);　　　　
    document.cookie = c_name + "=" + escape(value) + ((expiredays == null) ? "" : ";expires=" + exdate.toGMTString());
  }
  Object.assign(Vue.prototype, {
    showLoading () {
      store.commit('setState', { LOADING: true })
    },
    hideLoading() {
      store.commit('setState', { LOADING: false })
    }
  })

  // 组件
  Vue.component('myCom', { })    // 语法糖方法

  var myCom2 = Vue.extend({ })   // 构造器方法 
  Vue.component('myCom2', myCom2) 


  // 插件：相当于 MyPlugin.install(Vue)
  Vue.use(MyPlugin)
  Vue.use(MyPlugin, { someOption: true })


  // 混入：为自定义对象注入处理逻辑但会影响之后的 vue 实例
  Vue.mixin({ })

  // 推迟回调
  Vue.nextTick()  
  ```



## mixins
  * __组件引用__：相当于在父组件内开辟了一块单独空间给子组件，本质上两者是相对独立的
  * __mixins 混合__：将子组件内部的属性和方法全部扩充到父组件，从而实现组件的复用

  ```js
  // mixin.js
  export default {
      methods: {
          formatDate (dateTime, fmt = 'YYYY年M月DD日 HH:mm:ss') {
              if (!dateTime) { return '' }
              moment.locale('zh-CN')
              return moment(dateTime).format(fmt)
          }
      }
  }

  // main.js 全局
  import mixin from './mixin'
  Vue.mixin(mixin)

  // 组件 局部
  import { formatDate } from './mixins'
  export default {
    name: 'modal',
    mixins: [formatDate],
    computed: {
        formatDate(){
            // 调用方法
            return this.formatDate(new Date())
        }
    }
  }
  ```


# 三、组件编写
> 组件只有挂载到某个 Vue 实例下才会生效，但组件内必须使用箭头函数，否则 this 不指向当前组件

## 基础使用

  * 书写方式
    * __通过 script__：`<script type="text/x-template" id="myCom"></script>`
    * __通过 template__：`<template id="myCom"></template>`
    * __单文件组件__：文件扩展名为 .vue，组成部分为 `template、script、style`
  * 配置选项
    * `name、template、props、data、computed、methods、components、created 等生命周期`
  * 对外接口
    * __props 状态__：单向传递数据
    * __events 事件__：触发事件
    * __slots 片断__：嵌套内容
  * 注册方式
    * 全局：所有实例和组件都可以直接调用
    * 局部：只有引用组件的实例或组件可以调用
    ```js
    // 全局注册：my-com
    Vue.component('myCom', { }) 

    // 局部注册：child
    import child from '@/components/child' 
    export default {
        components:{ child }              
    }
    ```
  * 扩展方法
    ```js
    // 定义一个混合对象
    export const myMixin = {
        data(){
            return { num: 1 }
        },
        methods: {
            hello() {
                console.log('hello from mixin')
            }
        },
        created() {
            this.hello()
        }
    }

    // 组件中引用
    import {myMixin} from "./mixin.js"
    var Component = Vue.extend({
        mixins: [myMixin]
    })
    ```



## 内置指令
  * 数据渲染：`双层 { }、v-text、v-html`
  * 条件渲染：`v-if、v-else`
  * 循环渲染：
    * 数组：`v-for="(item, index) in arr" :key="index"`
    * 对象：`v-for="(item, key, index) of obj" :key="index"`
  * 事件绑定
    * 普通：`v-on:click = "todo"`
    * 缩写：`@click = "todo"`
    * 修饰符：`@click.stop.self、@keyup.stop.enter`
      * 阻止冒泡：stop
      * 阻止默认：prevent
      * 执行一次：once
  * 动态绑定：`v-bind:`，注意可以省略 v-bind 
    * class：
      * :class="['a', 'b']"
      * :class="[ true ? 'a': 'b']"
      * :class="{ 'active': isActive(id) }"
    * style
      * :style="{color: 'red'}"
      * :style="[{color: 'red'}, {fontSize: '16px'}]"
  * 双向绑定
    * 绑定：`v-model、v-model.trim`
    * 修饰符
      * trim：首尾空格过滤
      * lazy：监听change事件
      * number：字符串转为数字
  * 性能优化
    * 只渲染一次：`v-once`
    * 防止页面闪烁：`v-cloak`
    * 不输出 data：`v-pre`


## 组件通信

### 父子组件通信
  * 子传父
    * 法一：通过ref属性在父组件中直接取得子组件的数据
    * 法二：子组件 $emit() 触发自定义事件，父组件 $on() 监听
      * 父组件：`<child @updateList = "updateList"></child>`
      * 子组件：`this.$emit("updateList", data)`
  * 父传子：在子组件中通过 props 定义父组件传递的属性
    * 父组件：`<child name="Tom" :songs="songs" :age="age"></child>`
    * 子组件：props 接收属性如下

  ```js
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
> 同步父子组件数据，注意不要在子组件中修改 props 接收的数据

  * 普通写法
    * 父组件监听自定义事件：`<child @update="val => pmsg = val" :msg="pmsg"></child>`
    * 子组件触发事件：`props: { msg: String }、this.$emit('update', newVal)`
  * 使用 sync
    * 父组件
      * 写法：`<child :startDate.sync="startDate"></child>`
      * 扩展：`<child :startDate="bar" @update:startDate="val => startDate = val"></child>`
    * 子组件
      * 属性：`props: { startDate: String }`
      * 事件：`this.$emit("update:startDate", startDate)`


### 祖孙组件通信
> $attrs 用来简化组件和后代组件的数据传递
    
  * 属性
    * __$attrs__：传递不被 prop 识别的属性 (class和style除外)
    * __$listeners__：传递组件的事件 (.native除外)
    * __inheritAttrs__：控制 vue 对非 prop 特性默认行为
        true：默认将组件中不被 props 识别的属性绑定到根元素
        false：可以通过 v-bind 显性绑定到非根元素
  * __com.vue__：child sex="男"
  * __child.vue__：grand v-bind="$attrs" v-on="$listeners"
    ```js
    export default {
        // props 没有注册 sex
        computed: {
            sex(){
                return this.$attrs.sex
            }
        },
        // 去掉默认行为
        inheritAttrs: false
    }
    ```
  * __grand.vue__
    ```js
    export default {
        computed: {
            sex(){
                return this.$attrs.sex
            }
        },
        inheritAttrs: false
    }
    ```
        

### 全局通信
  * 使用空实例作为事件中心
    1. 根组件中添加空实例，允许所有组件调用
    2. 某个组件中定义调用事件触发的方法
    3. 另一个组件中绑定事件监听，并在组件销毁前解除绑定
  * 借助Vuex的全局状态共享


  ```js
  // main.js
  new Vue({ 
      data: { 
          eventHub: new Vue() 
      } 
  })

  // com1 
  this.$root.eventHub.$emit('updateList', true)

  // com2
  export default {
      created(){
          this.$root.eventHub.$on('updateList', value => {
              console.log(value)
          })
      },
      beforeDestroy() {
          this.$root.eventHub.$off('to_sibiling')
      }
  }
  ```


## template 标签
> 可看作一个不可见的包裹元素，主要用于分组的条件判断和列表渲染


### 条件渲染
> 通过 key 实现切换时清空内容

  ```html
  <template v-if="loginType === 'username'">
    <label>Username</label>
    <input placeholder="Enter your username">
  </template>
  <template v-else>
    <label>Email</label>
    <input placeholder="Enter your email address">
  </template>
  ```


### 列表渲染
  ```html
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



# 四、插件开发
> 本质是一个必须提供一个公开接口的对象或函数，用来扩展 vue 功能
    
## 插件与组件
  * 区别：插件可以实现很多组件无法实现的需求
  * 关系：插件可以封装组件，组件可以暴露数据给插件


## 自定义开发
> Vue.use() 会自动调用 install() 并阻止相同插件注册多次

### 常规写法
  ```js
  // toast.js
  var Toast = {};        
  Toast.install = function (Vue, options) { 
      Vue.prototype.$msg = 'Hello World';
  }
  module.exports = Toast;      

  // main.js
  import Toast from './toast.js';
  Vue.use(Toast);  
  console.log(this.$msg);   // Hello World
  ```


### 模板写法
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


### 表单验证插件

  * html
    * input：必须设置唯一的 name 属性值
    * 错误：span class="error"、输入框父元素有 error_item 类名时显示 
  * 验证
    * 默认规则：v-validator:email.vanow= "['required', 'Mail']"
    * 自定义规则：v-validator:email= "[{required: '', msg: '不可以为空呦'}]"
    ```js
    // v-validator:password = "validatePassword"
    export default {
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
          return (password.length >= 6 && password.length <= 12)
        }
      }
    }
    ```
  * 方法调用
    ```js
    check () {
      let checkResult = this.$va.checkAll()
      if (checkResult) {
        console.log('请求接口去跳转页面')
      }
    }
    ```

    
  ```js
  // main.js：全局引入
  import Validator from './assets/js/validator'
  Vue.use(Validator)


  // validator.js：自定义封装

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
  // 挂在 vue 实例上面 $va
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
  // va 构造器挂在 vue 实例上面 $va 的 forms 上面
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
  // 挂载 va 上的方法 ============================================
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
  // 表单验证的方法 ========================================
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
      // 15 位身份证号校验
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
      // 18 位身份证号校验
      case 18:
        if ( parseInt(value.substr(6,4)) % 400 == 0 || (parseInt(value.substr(6,4)) % 100 != 0 && parseInt(value.substr(6,4))%4 == 0 )){ 
          // 闰年出生日期的合法性正则表达式 
          ereg=/^[1-9][0-9]{5}(19|([2-9][0-9]))[0-9]{2}((01|03|05|07|08|10|12)(0[1-9]|[1-2][0-9]|3[0-1])|(04|06|09|11)(0[1-9]|[1-2][0-9]|30)|02(0[1-9]|[1-2][0-9]))[0-9]{3}[0-9xX]{1}$/;
        } else { 
          // 平年出生日期的合法性正则表达式 
          ereg=/^[1-9][0-9]{5}(19|([2-9][0-9]))[0-9]{2}((01|03|05|07|08|10|12)(0[1-9]|[1-2][0-9]|3[0-1])|(04|06|09|11)(0[1-9]|[1-2][0-9]|30)|02(0[1-9]|1[0-9]|2[0-8]))[0-9]{3}[0-9xX]{1}$/;
        }	
        
        if(ereg.test(value)){   // 测试出生日期的合法性  
          // 计算校验位  
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
          M = JYM.substr(Y,1);  // 判断校验位
          if(M == idcard_array[17].toUpperCase()){
            return true;    // 检测 ID 的校验位 false
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

        // 需要立即校验的框
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
  // 自动注册 vue
  if (typeof window !== 'undefined' && window.Vue) {
    window.Vue.use(plugin)
  }
  export default {
    install: plugin
  }
  ```



## 全局组件
> 将组件以插件形式封装

### 组件封装
  ```js
  // 初始化目录
  vue init webpack-simple test
  cd test && cnpm install  
  src              
    ├── App.vue               
    ├── main.js               
    └── lib                 
        ├── Loading.vue  
        └── index.js     

  // index.js
  import Load from './Loading.vue'
  const Loading = {
      install: function (Vue) {
          Vue.component('Loading', Load)
      }
  }
  if (typeof window !== 'undefined' && window.Vue) { 
      window.Vue.use(Loading) 
  }
  export default Loading

  // main.js 
  import Loading from './lib/index.js'
  Vue.use(Loading)
  ```

### 修改配置
  ```js
  // webpack.config.js
  module.exports = {
      entry: './src/lib/index.js',
      output: {
          path: path.resolve(__dirname, './dist'),
          publicPath: '/dist/',
          filename: 'vue-loading.js',
          library: 'loading',     // 指定 require 时的模块名
          libraryTarget: 'umd',   // 生成不同 umd 的代码
          umdNamedDefine: true    // 构建过程中对 AMD 模块进行命名
      }
  }

  // package.json 
  {
    "name": "vue-loading",         // 组件名, 不能与已有npm包重复
    "private": false,              // 组件包改为公用
    "main":"dist/vue-slider.js",   // 配置 import 引入的检索地址
    "repository": {                // 指定代码所在的仓库地址
        "type": "git",
        "url": "git+https://github.com/chuang/loading.git"
    },
  }
  ```


### 打包发布
  * 打包：`npm run build`
  * 登录：`npm login`
  * 发布：`npm publish`
  
  
### 安装使用
  ```js
  // 安装插件
  npm install vue-loading   

  // main.js 
  import Loading from 'vue-loading'
  Vue.use(Loading)
  ```








