---
title: Javascript 常用的数据类型
tags:
  - Javascript
  - js 数据
categories: Javascript
top: false
keywords:
  - js
  - javascript
date: 2019-03-19 23:11:12
description: 字符串、对象、数组
---

# 一、字符串
> 用于存储和处理文本，可看作类数组

## 创建
  ```js
  var str_1 = 'hello'
  var str_2 = String('123')
  var str_3 = new String('hello str')
  ```

## 属性
  * length：长度
  * prototype：原型属性，保存当前对象的原型对象
  * constructor：引用属性，保存当前对象的构造函数

## 常用方法
  * 字符索引：`charAt、indexOf`
  * 正则匹配：`match、search、replace`
  * 分割为数组：`split`
  * 字符串连接：`concat`
  * 裁剪字符串：`slice、substr、substring`


## 扩展方法
  ```js
  // 调用方式
  var eg1 = " ab ".trim();

  // 去除左边的空格
  String.prototype.LTrim = function(){
      return this.replace(/(^\s*)/g, "");
  }

  // 去除右边的空格
  String.prototype.Rtrim = function(){
      return this.replace(/(\s*$)/g, "");
  }

  //去除前后空格
  String.prototype.Trim = function(){
      return this.replace(/(^\s*)|(\s*$)/g, "");
  }
  ```

　
# 二、对象
> 键值对的无序集合

## 分类
  1. 内部对象
    <div style="text-indent: 2em">包括`Array、Boolean、Date、Function、Global、Math、Number、Object、RegExp、String以及 Error、ReferenceError等各种错误类对象`。其中 `Global 和 Math` 这两个对象又被称为“内置对象”，这两个对象在脚本程序初始化时被创建，不必实例化这两个对象。</div>
  2. 宿主对象
    <div style="text-indent: 2em">执行JS脚本的环境提供的对象。对于嵌入到网页中的JS来说，其宿主对象就是浏览器提供的对象，所以又称为浏览器对象，如`Window、Documen、Element、form、image`等。不同的浏览器提供的宿主对象可能不同，即使提供的对象相同，其实现方式也大相径庭，这也带来了浏览器兼容问题，增加开发难度。</div>
  3. 自定义对象
    <div style="text-indent: 2em">开发人员自己定义的对象，用于扩展 JS 应用和功能。</div>
 

## 创建
  ```js
  // 1、字面量创建
  var p1 = {
          name: "lisa",
          say: function(){
              console.log(this.name);
          }
      }
  // 报错, null / "" 都可以
  var obj = { "name": None }  


  // 2、Object.create 将对象继承到 __proto__ 属性上
  var p2 = Object.create({ x: 1 })
  console.log(p2.__proto__)   


  // 3、new 继承构造函数的属性

  // 创建系统内置对象
  var p3 = new Object({ x: 1 })  
  console.log(p3) 

  var arr = new Array();
  var date = new Date();
  var exp = new RegExp("ys");

  // 创建自定义对象 
  function Person(name, age){
      this.name = name;
  }
  var p4 = new Person("tom");
  ```


## 拷贝
  * __赋值__
    * `=、new Object` 
    * 原始对象和目标对象指向同一内存地址，任何修改都会相互影响
  * __浅拷贝__
    * `Object.assign / $.extend (target, [objN])` 
    * 只有目标对象的内层对象修改时才会影响原始对象
  * __深拷贝__
    * `for in / JSON.parse(JSON.stringify(obj)) / $.extend(true, target, [objN])`
    * 目标对象的任何修改都不会影响到原始对象

  ```js
  // 赋值
  var obj = { age: 18 };
  var _obj = obj;
  _obj.age = 20;
  console.log(obj.age, _obj.age); // 20 20

  // 浅拷贝
  var boy = {
    age: 18,
    address: { home: '北京' }
  }
  var _boy = Object.assign({}, boy);
  _boy.age = 20;
  _boy.address.home = "上海";
  console.log(boy.age, _boy.age);   // 18 20
  console.log(boy.address.home, _boy.address.home); // 上海 上海
  ```
 


## 基础操作
  ```js
  var obj = { name: "tom", age: 20 }

  // 获取
  var name = obj.name
  var age = obj['age']   

  // 修改
  obj.name = "新属性值"  

  // 方法
  obj.say = function(){ console.log(1) }    

  // 删除
  delete obj.age          

  // 判断类型
  obj instanceof Object  

  // 判断对象实例是否包含某个属性
  obj.hasOwnProperty("name")

  // 判断对象实例及其原型中是否包含某个属性
  "name" in obj

  //判断`obj`是否为空
  function isEmptyObject(obj){  
    if(!obj || typeof obj !== 'object' || Array.isArray(obj)){
        return false
    }
    return ! Object.keys(obj).length
  }
  ```


## 对象序列化
<div style="text-indent: 2em">JSON 是一种轻量级的数据交互方式，可以存储和传输数据而显示更丰富的信息，一般作为前后端交互的数据格式。</div>

  ```js
  // json 对象转为字符串
  var json_1 = { name: "tom", age: 18 }
  var strJson = JSON.stringify(json)


  // json 字符串转为对象
  var strJson = '{ name: "tom", age: 18 }'
  var json_2 = eval('(' +strJson+ ')');   // 不建议使用
  var json_3 = JSON.parse(strJson)        // 接近 W3C 标准
  ```


## 类数组对象
<div style="text-indent: 2em">拥有一个length属性和若干索引属性的对象，常见的有 `arguments、document.forms、document.getElementsByClassName`。arguments 是函数中自动创建的一种类数组对象，用来模拟函数重载效果（根据传入参数个数执行不同函数）。</div>

  ```js
  // 转换为数组
  var arrayLike = { '0' : 'a','1' : 'b', length : 3 };
  var arr1 = Array.prototype.slice.call(arrayLike);
  var arr2 = [].slice.call(arrayLike);
  var arr3 = Array.from(arrayLike);

  // arguments 重载
  var checkout = function(){
    　　if(arguments.length==0){
    　　　　console.log("微信");
    　　}else if(arguments.length==1){
    　　　　console.log("现金");
    　　}else{
    　　　　console.log("刷卡");
    　　}
  }
  checkout();      // 微信
  checkout(100);   // 现金
  checkout(1, 2);  // 刷卡
  ```


## 面向对象编程
<div style="text-indent: 2em">`字面量方式 和 new Object()` 是两种最常用的对象创建方式，但是在使用同一接口创建多个对象时会产生大量重复代码，为了简化代码量并提高可维护性，项目开发时一般使用面向对象写法。</div>


### 三大特征
  * 封装：函数
  * 继承：重用代码
  * 多态：增加属性方法


### 编程思想
  * 对象：由方法和属性组成的一个整体，可看作是一个黑盒子，使用时只关注它的对外接口而不需要了解内部结构
  * 面向对象：为空对象添加自定义的方法和属性
    

### 编程模式
  * __工厂模式__：解决了重复实例化多个对象的问题，但是不能识别各自的实例化对象。
  * __构造函数模式__：把函数当作一个类（首字母大写以区分普通函数），实例化的对象可以通过 instanceof 判断它的实例。基本原理是实例化时会隐式创建一个空对象并最后返回。
  * __原型模式__：好处是可以让 `所有对象实例` 共享 `原型对象` 所包含的属性和方法。
  * __混合模式__：将可变属性写入构造函数，将方法和固定属性写入原型对象，这样可以节省内存。


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
  ```


## 原型对象

### 原型对象
  1. 函数对象：通过 new Function() 创建都是函数对象，其他的都是普通对象。每个对象都有 `__proto__` 属性指向原型对象，但只有函数对象才有 `prototype` 属性。
  2. 原型对象：一个普通对象。所有的原型对象都会自动获得一个指向其构造函数的指针 `constructor`，构造函数通过 `prototype` 属性指向它的原型对象，可以说 原型对象 `Person.prototype` 就是构造函数 `Person` 的一个实例。


### 原型链继承
  1. __基本思路__：利用原型让一个引用类型继承另一个引用类型的属性和方法，即让原型对象等于另一个类型的实例，这样就可以通过 `__proto__` 属性构成实例与原型的链条, 从而实现继承。
  2. __实现过程__：调用实例对象中的属性或方法时，如果自身有则调用，没有则通过 `__proto__` 属性沿着原型链向上查找直到 null 结束，如果没有则返回undefined。查找顺序为：`实例对象 obj --> 原型对象 fn.prototype --> Object.prototype -->  null`。
  3. __继承代码__
 
  ```js
  function Person(name){
      this.name = name;   
  }
  Person.prototype.say = function(){
      console.log(1);
  }
  function Son(name){
      Person.call(this, name);
  }
  var s1 = new Son("son1");
  var s2 = new Son("son2");
  console.log(s1.__proto__ == s2.__proto__)
  ```



# 三、数组
> 一组数据的有序集合，表现形式是内存中的一段连续的内存地址

## 创建
  ```js
  // 直接创建 
  var arr = []            // 空数组
  var arr2 = [1, 2, 3]    // 三个元素

  // 通过构造函数
  var arr1 = new Array()    // 空数组
  var arr2 = new Array(10)    // 长度为10 
  var arr3 =  new Array(1, 2)   // 两个元素
  ```

## 拷贝
  * 浅拷贝
    * `arr.slice(0)、arr.concat()`
    * 如果数组元素都是基本类型，原始数组和目标数组的修改互不影响
    * 如果数组元素是对象或数组，这样会只拷贝引用而导致修改时相互影响
  * 深拷贝
    * `JSON.parse(JSON.stringify(arr))`
    * 不论数组元素是什么类型，原始数组和目标数组的修改都会互不影响


  ```js
  // 浅拷贝
  var shallowCopy = function (obj) { 
    if (typeof obj !== 'object') return; 
    var newObj = obj instanceof Array ? [] : {}; 
    for (var key in obj) {
      if (obj.hasOwnProperty(key)) {
        newObj[key] = obj[key];
      }
    }
    return newObj;
  }

  // 深拷贝
  var deepCopy = function(obj) {
    if (typeof obj !== 'object') return;
    var newObj = obj instanceof Array ? [] : {};
    for (var key in obj) {
      if (obj.hasOwnProperty(key)) {
        newObj[key] = typeof obj[key] === 'object' ? deepCopy(obj[key]) : obj[key];
      }
    }
    return newObj;
  }
  ```
  


## 特殊数组
  * 多维数组：元素是数组
  * 稀疏数组：索引不连续，length值大于实际元素个数


  ```js
  // 二维数组
  var arr = [ [1, 2], [1, ‘good’] ]
  console.log(arr[0][0])

  // 稀疏数组
  var arr = [];
  arr[20] = "hello";
  ```


## 操作方法
  1. 基础
    * 读写：`索引/属性`
    * 添加：`arr[index] = new`
    * 删除：`delete arr[index]`
    * 遍历：`for`
  2. ES3
    * 添加：`push、unshift、concat`
    * 删除：`pop、shift`
    * 裁剪：`slice、splice`
    * 排序：`sort、reverse`
    * 转换：`join、toString`
  3. ES5
    * 位置：`indexOf、lastIndexOf`
    * 迭代：`every、some、filter、forEach、map`
    * 累计：`reduce`


  ```js
  // 元素读写
  var arr = [2, 1, 3]
  consloe.log(arr.length)
  consloe.log(arr[0])

  arr[1] = 1
  arr[-1.23] = true  // 超出范围时就不是索引，而是属性


  // 判断是否数组
  Array.isArray(arr)   
  arr instanceof Array
  arr.constructor === Array
  arr.prototype.toString.apply([Array])

  // 判断元素         
  arr.every(function(item){
      return item > 5
  }) 

  // 排序
	arr.sort(function(a, b){ 
      return a-b
  })

  // 遍历
  arr.forEach(function(value, index, arr){ })

  // 映射返回新数组，原数组不改变
  arr.map(function(value, index, arr){ 
      return value * 2    
  })
  arr.map(item => item + 10)

  // 过滤不符合条件的元素
  arr.filter(function(value, index){ 
      return value % 2 !== 0
  })          
  
  // 累计
  arr.reduce(function(x, y){ 
      return x + y
  })
  ```


## 扩展方法

### 求最值
  ```js
  // 最大值
  Math.max(...arr)
  Math.max.apply(null, arr)

  // 最小值
  Math.min(...arr)
  Math.min.apply(null, arr)
  ```

### 数组去重
  ```js
  // ES6 Set 有元素唯一的特性
  [...new Set(arr)]  
  Array.from(new Set(arr))

  // 添加原型方法
  Array.prototype.distinct = function (){
      var arr = this,
          result = [],
          len = arr.length;

      arr.forEach(function(v, i ,arr){   
          var bool = arr.indexOf(v, i+1);   
          if(bool === -1){
              result.push(v);
          }
      })
      return result;
  };
  arr.distinct()

  // 普通封装
  function arry_unique(array){
      var res = [];
      var json = {};
      for(var i = 0; i &lt; array.length; i++){
          if(!json[array[i]]){
              res.push(array[i]);
              json[array[i]] = 1;
          }
      }
      return res;
  }
  ```


### 排序
  1. sort 排序 
    * 升序排列：`return a-b`
    * 倒序排列：`return b-a`
  2. 冒泡排序
    * 思想：相邻元素之间逐对两两比较，若不符合预期则先交换位置再继续比较，这样则每次比较都能把最大或最小的元素放在预期位置直到完成排序
    * 特点：简单实用易理解，缺点是比较次数多、效率低
  3. 快速排序：对冒泡排序的一种改进
    * 思想：首先找到一个基准点（一般是数组中部），然后数组被分为两部分，依次与基准点数据比较，若比它小则放左边，反之放右边。
    * 特点：快速且常用，缺点是需另外声明两个数组、浪费内存


  ```js
  // sort 排序  
  export const orderArr = arr => {
      arr.sort((a, b) => {

          // 数组元素排序，
          return a-b    

          // 数组对象排序 
          return a[property] - b[property]; 
      })
  }

  // 冒泡排序
  function bubbleSort(arr){
      for(i=0; i &lt; arr.length-1; i++){
          for(j=0; j &lt; arr.length-1-i; j++){
              if(arr[j] &gt; arr[j+1]){
                  var temp = arr[j];
                  arr[j] = arr[j+1];
                  arr[j+1] = temp;
              }
          }
      }
      return arr;
  }
   
  // 快速排序
  function  quickSort(arr){
      if(arr.length &lt;= 1){
          return arr 
      }
      var mIndex = Math.floor(arr.length/2)
      var mVal = arr.splice(mIndex, 1)[0]
      var left = [], right=[ ];

      for(var i=0; i &lt; arr.length; i++){
          if(arr[i] &lt; midIndexval){
              left.push(arr[i])
          }else{
              right.push(arr[i])
          }
      }
      return quickSort(left).concat([mVal], quickSort(right))
  }
  ```

 

