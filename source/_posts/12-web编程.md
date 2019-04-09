---
title: Web 编程模式
tags:
  - Javascript
categories: Javascript
top: false
keywords:
  - js
  - 编程
date: 2019-04-02 21:38:29
description: 函数式编程、模块化编程、面向对象编程、面向切面编程、异步编程
---



# 四、模块化编程

<div style="text-indent: 2em">早期引用 JS 文件时直接通过 script 即可，但是随着项目的复杂度越来越大，这种方式带来了 逻辑混乱、可维护性差等问题。为了编写可维护的代码，我们采用将代码合理拆分到不同的文件里，每一个文件就是一个模块，文件路径就是模块名，这种组织代码的方式就是模块化。</div> 

## 优势
  * 开发效率高，有利于多人协同开发
  * 解决项目中的全局变量污染的问题
  * 功能单一，方便代码的复用和维护
  * 解决文件依赖问题，无需关注文件引用顺序


## JS 环境
  * 服务器端
    * 相同的代码需要多次执行
    * CPU 和内存资源是瓶颈
    * 加载时从磁盘中加载
  * 浏览器端
    * 代码需要从一个服务器端分发到多个客户端执行
    * 带宽网络是瓶颈
    * 加载时需要通过网络加载


## 发展进程
  1. 命名空间形式的代码封装 (函数封装)
  2. 通过 立即执行函数(IIFE) 创建的命名空间
  3. 服务器端运行时 Nodejs 的 CommonJS 规范
  4. 将模块化运行在浏览器端的 AMD/CMD 规范
  5. 兼容 AMD 和 CommonJS 的 UMD 规范
  6. 通过语言标准支持的 ES Module

  ```js
  // 命名空间
  function square(x) {
      if(typeof x !== "number") return;
      return x * x;
  }
  var utils = {
      add: function(x, y) {
          return x + y
      }
  }

  // IIFE
  var module = (function(){
      var count = 0;
      return {
          inc: function(){
              count += 1;
          },
          dec: function(){
              count += -1;
          }
      }
  })()
  module.inc();
  ```


## CommonJS
> 服务器端的模块化开发规范，主要特点是 同步加载模块，Nodejs 的模块规范就是参照它实现的

<div style="text-indent: 2em">CommonJS 会在启动时把内置模块和加载过的模块放在本地硬盘，文件读取时不受限于网络而等待时间很短，所以在 Node 环境中使用同步加载的方式不会有很大问题。但是 CommonJS 如果应用在浏览器端，同步加载的机制会使得 JS 阻塞 UI 线程，造成页面卡顿，网速不好时还会导致浏览器假死。</div> 


### 模块定义
<div style="text-indent: 2em">为了方便，NodeJs 为每个模块提供一个 exports 变量指向 module.exports，相当于在每个模块头部执行 `var exports = module.exports`。exports 默认是个空对象，可以添加属性和方法但不能被赋值，因为这样会切断了两者联系，而且不能导出需要new实例化的类。使用时建议 module.exports 导出对象和类、exports 导出普通函数和变量</div> 

  * 一个单独的文件就是一个模块
  * 每个模块都是一个单独的作用域
  * 每个模块内部都有一个 module 对象来代表当前模块
  * 暴露模块中的内容可以通过 module.exports、exports 
    

### 模块加载
  * 本质：读取模块内部的 module.exports 变量
  * 特点
    * 缓存结果：模块第一次加载并执行之后就会缓存结果，再次加载时就会直接读取缓存结果，想要再次运行则必须清除缓存
    * 同步加载：模块加载会阻塞接下来代码的执行，需要等到模块加载完成才能继续执行
  * 方法
    * require()：当前模块中读取其它模块并执行
    * require.resolve()：解析模块标识符的绝对路径

### 模块标识
> require() 的参数，require 会根据其格式使用不同的路径加载原则去寻找模块文件

  * 模块名：符合小驼峰命名的字符串
    * 核心模块：系统安装目录
    * 安装模块：node_modules 目录
  * 绝对路径：以 "/" 开头
  * 相对路径：以 "./" 开头


  ```js
  // 导出一个要实例化的类  module.exports = exports = function (){ }

  // rocker.js 
  module.exports = function(name, age) {
      this.name = name;
      this.age = age;
      thisl.about = function() {
          console.log(this.name + 'is' + this.age + 'years old');
      };
  };

  // 引用
  var Rocker = require('./rocker.js')
  var r = new Rocker('Ozzy', 62)
  r.about()   // Ozzy is 62 years old


  // 导出一个静态类 exports.funcName = function (){}

  // hello.js
  function hello() {
      console.log('Hello, world');
  }
  function greet(name) {
      console.log('Hello, ' + name + '!');
  }
  // 写法一
  module.exports = {
      hello: hello,
      greet: greet
  }
  // 写法二
  exports.hello = hello;
  exports.greet = greet;
  // 错误写法
  exports = {
      hello: hello,
      greet: greet
  }

  // 引用
  var hello = require('./hello.js')
  hello.hello()  // Hello, world
  ```


## AMD
> 异步模块定义，浏览器端模块化开发规范。它允许异步和按需加载模块，requireJS 是参照它实现的

### 特点
  * 异步加载方式，模块加载不影响后面语句的运行
    * 实现方式：依赖模块的所有语句都放置在回调函数中并在模块加载完成后执行
    * 解决问题：js 加载时浏览器会停止页面渲染，加载文件越多则页面失去的响应时间越长
  * 管理模块之间的依赖性，便于代码的编写和维护
    * 解决问题：js 文件需要它的被依赖文件早于它加载到浏览器 


### 语法 
  * 模块定义：`define(可选模块标识, 依赖模块数组, 模块初始化要执行的函数或对象)`
  * 模块加载：`require(依赖模块数组, 模块加载成功后的回调函数)`

  ```js
  // 定义模块 myModule.js
  define(['dependency'], function(){
      var name = 'Byron';
      function printName(){
          console.log(name);
      }
      return {
          printName: printName
      };
  });

  // 加载模块
  require(['myModule'], function (my){
  　 my.printName();
  });
  ```


## CMD 
> AMD 基础上改进的一种规范，主要区别是对依赖模块的执行时机处理不同。seaJS 是参照它实现的

### 区别
  * 依赖处理
    * AMD 推崇依赖前置，在定义模块时就要声明其依赖的模块 
    * CMD 推崇就近依赖，只有在用到某个模块时再去 require 
  * 方法    
    * AMD API 默认是一个当多个用，比如模块加载分为全局的和局部的 require
    * CMD 推崇职责单一而且简单，比如模块加载只有 seajs.use() 


### 语法
  ```js
  // 定义模块  index.js
  define(function(require, exports, module) {

      // 使用 exports
      exports.name = 'index';
      exports.hello = function() {
          console.log('Hello index');
      };

      // 使用 module.exports
      module.exports = {
          name: 'index',
          hello: function() {
            console.log('index');
          }
      };
  });

  // 加载模块
  seajs.use('index', function(my) {
      my.hello();
  });
  ```


## UMD
> 兼容 AMD 和 commonJS，提出了跨平台的解决方案

  ```js
  // 无导入导出规范，只有如下的常规写法来检测 JS 环境
  (function (root, factory) {
      if (typeof define === 'function' && define.amd) {
          // AMD
          define(['jquery'], factory);
      } else if (typeof exports === 'object') {
          // CommonJS
          module.exports = factory(require('jquery'));
      } else {
          // 挂载到全局：root 即 window
          root.returnExports = factory(root.jQuery);
      }
  }(this, function ($) {
      // 方法
      function myFunc(){ };
      // 暴露公共方法
      return myFunc;
  }));
  ```


## ES Module
> ES6 的最新语法支持规范

<div style="text-indent: 2em">CommonJS 和 AMD 规范都只能在运行时确定依赖。而 ES6 在语言层面提出了模块化方案, ES6 module 模块编译时就能确定模块的依赖关系，以及输入和输出的变量。ES6 模块化这种加载称为 编译时加载(异步按需加载)、静态加载。</div> 

  * 导入：`import {模块名A，模块名B...} from '模块路径'`
  * 导出：`export、export default`
  * 异步加载：`import('模块路径').then()`

  ```js
  /* 错误写法 */
  // 写法一
  export 1;

  // 写法二
  var m = 1;
  export m;

  // 写法三
  if (x === 2) {
    import MyModual from './myModual';
  }

  /* 正确写法 */
  // 写法一
  export var m = 1;

  // 写法二
  var m = 1;
  export {m};

  // 写法三
  var n = 1;
  export {n as m};

  // 写法四
  var n = 1;
  export default n;

  // 写法五
  if (true) {
      import('./myModule.js')
      .then(({export1, export2}) => {
        // ...·
      });
  }

  // 写法六
  Promise.all([
    import('./module1.js'),
    import('./module2.js'),
    import('./module3.js'),
  ])
  .then(([module1, module2, module3]) => {
    // ···
  });
  ```

  
# 一、面向对象编程
> 用于定义一个类，使用时通过 new 创建出一个包含了自定义的方法和属性的对象

<div style="text-indent: 2em">`字面量方式 和 new Object()` 是两种最常用的对象创建方式，但是在使用同一接口创建多个对象时会产生大量重复代码，为了简化代码量并提高可维护性，项目开发时一般使用面向对象写法。</div>


## 三大特征
  * 封装：函数
  * 继承：重用代码
  * 多态：增加属性方法


## 实现方式
  * __工厂模式__：解决了重复实例化多个对象的问题，但是不能识别各自的实例化对象
  * __构造函数模式__：把函数当作一个类，实例化时它会隐式创建一个空对象并最后返回
    * 建议首字母大写以区分普通函数
    * 实例化的对象可以通过 instanceof 判断它的实例
  * __原型模式__：好处是可以让 `所有对象实例` 共享 `原型对象` 所包含的属性和方法
  * __混合模式__：将可变属性写入构造函数，将方法和固定属性写入原型对象，这样可以节省内存
  * __ES Module__：以上四种模式都是 ES5 方法，ES6 规范提供了更简单的定义类的方法


  ```js
  // 工厂模式
  function person(name, age){
      var obj = {};     // 原料
      obj.name = name;  // 加工
      obj.age = age;    
      obj.say = function(){ 
        console.log(this.name)    
      }
      return obj     
  }
  var p1 = person("tom", 18);


  // 构造函数模式
  function Person(name, age){     // 类：模具
      this.name = name;  
      this.age = age;    
      this.say = function(){ 
        console.log(this.name)    
      }  
  }
  var p2 = new Person("tom", 20);  // 实例化对象：产品
  console.log(p2 instanceof Person);


  // 原型模式
  function Person(){  }
  Person.prototype.name = "tom";
  Person.prototype.age = 24;
  Person.prototype.say = function(){
      console.log(this.name) 
  }
  var p3 = new Person()


  // 混合模式
  function Person(name, age){
      this.name = name;   
      this.age = age;   
  }
  Person.prototype.say = function(){
      console.log(this.name);
  }
  var p4 = new Person();


  // ES6 写法
  class Point(x,y){
      constructor(x,y){
          this.x=x;
          this.y=y;
      }
      toString(){
          return '('+this.x+', '+this.y+')';  
      }
  }
  ```
 

# 二、面向切面编程


# 三、异步编程
  
### 回调函数
  * 实现：作为参数传递到其它函数执行
  * 优点：简单、容易理解和部署
  * 缺点
    * 不利于代码的阅读和维护
    * 每个任务只能指定一个回调函数
    * 各个部分之间高度耦合，流程会很混乱
    
  
  ```js
  // 把同步操作变成异步
  f1(f2);

  function f1(callback){
  　　setTimeout(function () {
  　　　　　callback();
  　　}, 1000);
  }
  ```
    

### 事件监听
  * 实现：采用事件驱动模式 
  * 优点
    * 容易理解
    * 可以绑定多个事件
    * 每个事件可以指定多个回调函数
    * 可以去耦合，有利于实现模块化
  * 缺点：整个程序变成事件驱动型，运行流程不清晰 
  * 监听函数：on、bind、listen、observe、addEventListener


  ```js
  // f1 执行完成后立即触发 done 事件从而执行 f2
  f1.on("done", f2);

  function f1(){
    setTimeout(function(){
      //f1的代码量
      f1.trigger("done");
    }, 1000)
  }
  ```
 

### 观察者模式
> 即发布订阅模式，它定义了一种一对多的关系

  * 实现：让多个观察者同时对监听某个对象，对象变化会通知多个观察者对象而自动更新
  * 优点
    * 支持简单的广播通信，自动通知所有已经订阅过的对象
    * 目标对象与观察者之间的抽象耦合关系能够单独扩展以及重用
    * 页面载入后，目标对象很容易与观察者存在一种动态关联，增加灵活性


  ```js
  // f1 执行完成后向信号中心 jQuery 发布 done 信号，从而引发 f2 的执行
  jQuery.subscribe("done", f2);

  function f1(){
    setTimeout(function(){
      //f1的代码
      jQuery.publish("done");
    }, 1000)
  } 
  ```


### promise 模式
> CommonJS 工作组提出的一种规范，目的是为异步编程提供统一接口

  * 实现机制
    * 代理对象：Promise 对象代表一个异步操作
    * 对象状态：只有异步操作的结果，可以决定 Promise 对象的当前状态
    * 回调函数：`then()` 用来注册当 promise 完成或者失败时调用的回调函数
  * 对象状态
    * 三种状态：等待 `pending`、已完成 `fulfilled`、已失败 `rejected`
    * 状态改变：只能从 pending 转变为 fulfilled/rejected，而且状态一旦改变就不会再变
  * 主要优势
    * 解决回调地狱问题
    * 更好地进行错误捕获 
  * 规范图解
    <div align="center">
    ![Promise 规范](/images/post/promise.png) 
    </div>   


  ```js
  // 创建实例
  var promise = new Promise((resolve, reject) => {
      resolve()
  })
  promise.then(val => {   // 抛出错误
      console.log(val)
      console.log(1)
  }).then(() => {         // 不会执行
      console.log(111)
  }).catch(error => {     // 接收错误
      console.log(error)
  }).then(() => {         // 继续执行
      console.log(333)
  })


  // 语法糖
  Promise.resolve(42).then(value =>{
      console.log(value);
  })  // 等同于
  new Promise(resolve => {
      resolve(42)
  }).then(value => {
      console.log(value)
  })

  Promise.reject(value) // 等同于
  new Promise(function(resolve, reject){
      reject(new Error("出错了"));
  });


  // 合并多个实例
  function timerPromisefy(delay) {
      return new Promise(function (resolve) {
          setTimeout(function () {
              resolve(delay);
          }, delay);
      });
  }
  // 当数组中所有 Promise 对象被 resolve 之后，该方法才执行 
  Promise.all([
      timerPromisefy(1),
      timerPromisefy(10)
  ]).then(function (value) {
      console.log(value);    
  });
  // 任何一个 promise 变为 resolve/reject，程序就停止运行
  Promise.race([
      timerPromisefy(1),
      timerPromisefy(10)
  ]).then(function (value) {
      console.log(value);    
  });
  ```




