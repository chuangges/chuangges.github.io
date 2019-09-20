---
title: Vue 自定义开发
tags:
  - Vue.js
categories: Vue.js
top: false
keywords:
  - vue
date: 2019-04-14 22:11:50
description: 方法和插件的封装方式、常用汇总
---


# 一、方法封装

## 全局注册

  ```js
  // main.js：全剧引入
  import validator from 'assets/js/validator.js'
  Vue.use(validator)
  // validator.js：以插件形式封装
  export default {
    install (Vue, options) => {
        Vue.prototype.validator = { 
          checkDate: (rule, value, callback){ },
          checkEmpty: (rule, value, callbac) { }
        }
    }
  }
  // 组件中使用：
  this.validator.checkDate()

  // main.js：直接注册
  Vue.protytpe.print = function(val) {
      console.log(val);
  }
  Object.assign(Vue.prototype, {
    showLoading () {
      store.commit('setState', { LOADING: true })
    },
    hideLoading() {
      store.commit('setState', { LOADING: false })
    }
  })
  // 组件中使用
  this.print(222)
  this.showLoading()
  ```


## 单独封装

  ```js
  // tool.js：利用 CommonJS 思想单独封装
  export function getUserInfo () { }

  import { getUserInfo } from 'assets/js/tool.js'
  getUserInfo()
  ```


# 二、常用方法

## 设备判断
  ```js
  export function getDevice(){
    
    var browser = {
      versions: function () {  
        var u = navigator.userAgent, app = navigator.appVersion;  
        return {//移动终端浏览器版本信息   
          trident: u.indexOf('Trident') > -1, //IE内核  
          presto: u.indexOf('Presto') > -1, //opera内核  
          webKit: u.indexOf('AppleWebKit') > -1, //苹果、谷歌内核  
          gecko: u.indexOf('Gecko') > -1 && u.indexOf('KHTML') == -1, //火狐内核  
          mobile: !!u.match(/AppleWebKit.*Mobile.*/) || !!u.match(/AppleWebKit/), //是否为移动终端  
          ios: !!u.match(/\(i[^;]+;( U;)? CPU.+Mac OS X/), //ios终端  
          android: u.indexOf('Android') > -1 || u.indexOf('Linux') > -1, //android终端或者uc浏览器  
          iPhone: u.indexOf('iPhone') > -1 || u.indexOf('Mac') > -1, //是否为iPhone或者QQHD浏览器  
          iPad: u.indexOf('iPad') > -1, //是否iPad  
          webApp: u.indexOf('Safari') == -1 //是否web应该程序，没有头部与底部  
        };  
      }(),
      language: (navigator.browserLanguage || navigator.language).toLowerCase()  
    } 

    let device
    if (browser.versions.ios || browser.versions.iPhone || browser.versions.iPad) {  
      device = "IOS"
    }  
    else if (browser.versions.android) {  
      device = "android"  
    } else {  
      device = "other" 
    }
    return device
  }
  ```


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
      'M+': date.getMonth() + 1,   // 月份
      'd+': date.getDate(),        // 日
      'h+': date.getHours(),       // 小时
      'm+': date.getMinutes(),     // 分
      's+': date.getSeconds(),     // 秒
      'q+': Math.floor((date.getMonth() + 3) / 3), // 季度
      'S': date.getMilliseconds()  // 毫秒
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
      if(Number(_birthDate.month) - 1 < _currDate.getMonth()){
        result['age'] = _currDate.getFullYear() - birthDate.getFullYear();
      }else if(Number(_birthDate.month) - 1 == _currDate.getMonth()){
        if(Number(_birthDate.date) <= _currDate.getDate()){
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


## 页面滚动
  ```js
  // 垂直滚动到页面指定位置
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
        if (needScroll < 10 || needScroll > -10) {
            scrollAnimation(_currentY, targetY, dom)
        } else {
            dom.scrollTo(currentY, targetY)
        }
    }, 10)
  }
  ```


# 三、插件开发
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


# 四、常用插件

## 表单验证

### 定义
  ```js
  // validator.js

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


### 引入

  ```js
  // main.js：全局引入
  import Validator from './assets/js/validator'
  Vue.use(Validator)
  ```


### 应用

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
    




























