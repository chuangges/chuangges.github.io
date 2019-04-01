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
description: 字符串、对象、数组、base64、二进制对象 
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
  // 点操作后只能是属性名，有横杠时须去掉并把后面的首字母大写
  var name = obj.name   
   // 中括号中可放属性名或变量名  
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

  // 数组中是否存在下标为 1 的元素
  1 in arr 
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

 

# 四、Base64
> 基于 64 个可打印字符 `A-Z、a-z、0-9、+、/` 来表示二进制数据的一种方法

## 产生
  * 需求
    * 网络传输中文时容易出现乱码情况
    * 网络传输图片等文件的字符并不全是可打印的字符
    * 电子邮件早期只能传输英文，中文字符不能被服务器处理
  * 适用场景
    * 当访问外部资源很麻烦或受限时
    * 当图片的体积太小，占用一个 HTTP 会话不是很值得时
    * 当图片是在服务器端用程序动态生成，各个访问用户显示不同时
  * 图片应用
    * 主流浏览器都支持基于 Base64 编码的 dataURL 字符串 作为图片地址，[图片在线转码](http://imgbase64.duoshitong.com/) 
    * 图片上传服务器时需要纯字符串形式，截取 data:image/png;base64, 后面的字符串即可上传


## Base64 编码
> 一种将二进制序列的数据转换为字符串的编码算法，只是无法直接看到明文但并非加密

  * 理解
    * 字节 byte 是存储二进制数据的基本单位
    * 中文有 utf-8、gbk 等多种编码，不同编码对应的结果都不一样
    * 对于图片、文本和音视频等数据，只有转换为 二进制序列 之后才能进行 Base64 编码
    * 编码后的数据体积一般会变大，而且 base64Url 形式的图片不会被浏览器缓存，每次访问这样页面时都被下载一次，所以不适合被大量使用
  * 规则
    1. 将每三个字节作为一组，一共是 24 个二进制位
    2. 将 24 个二进制位分为四组，每个组有 6 个二进制位
    3. 在每组前面加两个00，扩展为 32 个二进制位，即 4 个字节
    4. 根据 base64 编码对照表转换为扩展后的每个字节的对应字符


## API

### 浏览器原生实现
  ```js 
  // 编码
  window.btoa('xiaomabuhei')  
  // 解码
  window.atob('eGlhb21hYnVoZWk=')          


  // 中文编码时需要先进行 URI 组件编码
  window.btoa(window.encodeURIComponent('小马不黑'))
  window.decodeURIComponent(window.atob(baseStr))
  ```

### js 实现
  ```js
  var Base64 = {
    _keyStr: "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/=",

    encode: function(input) {
      var output = "";  
      var chr1, chr2, chr3, enc1, enc2, enc3, enc4;  
      var i = 0;  
      input = Base64._utf8_encode(input);  
      while (i &lt; input.length) {  
          chr1 = input.charCodeAt(i++);  
          chr2 = input.charCodeAt(i++);  
          chr3 = input.charCodeAt(i++);  
          enc1 = chr1 &gt;&gt; 2;  
          enc2 = ((chr1 & 3) &lt;&lt; 4) | (chr2 &gt;&gt; 4);  
          enc3 = ((chr2 & 15) &lt;&lt; 2) | (chr3 &gt;&gt; 6);  
          enc4 = chr3 & 63;  
          if (isNaN(chr2)) {  
              enc3 = enc4 = 64;  
          } else if (isNaN(chr3)) {  
              enc4 = 64;  
          }  
          output = output +  
          _keyStr.charAt(enc1) + _keyStr.charAt(enc2) +  
          _keyStr.charAt(enc3) + _keyStr.charAt(enc4);  
      }  
      return output; 
    },

    decode: function(input) {
      var output = "";  
      var chr1, chr2, chr3;  
      var enc1, enc2, enc3, enc4;  
      var i = 0;  
      input = input.replace(/[^A-Za-z0-9\+\/\=]/g, "");  
      while (i &lt; input.length) {  
          enc1 = _keyStr.indexOf(input.charAt(i++));  
          enc2 = _keyStr.indexOf(input.charAt(i++));  
          enc3 = _keyStr.indexOf(input.charAt(i++));  
          enc4 = _keyStr.indexOf(input.charAt(i++));  
          chr1 = (enc1 &lt;&lt; 2) | (enc2 &gt;&gt; 4);  
          chr2 = ((enc2 & 15) &lt;&lt; 4) | (enc3 &gt;&gt; 2);  
          chr3 = ((enc3 & 3) &lt;&lt; 6) | enc4;  
          output = output + String.fromCharCode(chr1);  
          if (enc3 != 64) {  
              output = output + String.fromCharCode(chr2);  
          }  
          if (enc4 != 64) {  
              output = output + String.fromCharCode(chr3);  
          }  
      }  
      output = Base64._utf8_decode(output);  
      return output;  
    },

    _utf8_encode: function(string) {
      string = string.replace(/\r\n/g, "\n");  
      var utftext = "";  
      for (var n = 0; n &lt; string.length; n++) {  
          var c = string.charCodeAt(n);  
          if (c &lt; 128) {  
              utftext += String.fromCharCode(c);  
          } else if((c &gt; 127) && (c &lt; 2048)) {  
              utftext += String.fromCharCode((c &gt;&gt; 6) | 192);  
              utftext += String.fromCharCode((c & 63) | 128);  
          } else {  
              utftext += String.fromCharCode((c &gt;&gt; 12) | 224);  
              utftext += String.fromCharCode(((c &gt;&gt; 6) & 63) | 128);  
              utftext += String.fromCharCode((c & 63) | 128);  
          }  
  
      }  
      return utftext; 
    },

    _utf8_decode: function(e) {
      var string = "";  
      var i = 0;  
      var c = c1 = c2 = 0;  
      while ( i &lt; utftext.length ) {  
          c = utftext.charCodeAt(i);  
          if (c &lt; 128) {  
              string += String.fromCharCode(c);  
              i++;  
          } else if((c &gt; 191) && (c &lt; 224)) {  
              c2 = utftext.charCodeAt(i+1);  
              string += String.fromCharCode(((c & 31) &lt;&lt; 6) | (c2 & 63));  
              i += 2;  
          } else {  
              c2 = utftext.charCodeAt(i+1);  
              c3 = utftext.charCodeAt(i+2);  
              string += String.fromCharCode(((c & 15) &lt;&lt; 12) | ((c2 & 63) &lt;&lt; 6) | (c3 & 63));  
              i += 3;  
          }  
      }  
      return string;
    }
  }
  ```


## 图片转换为 base64 

### 本地上传图片
  ```js
  // html：input type="file" id="uploadImg"

  function getBase64Img(fileObj, callback){  
      var reader = new FileReader();  
      reader.readAsDataURL(fileObj);   // 转为 Base64 格式 
      reader.onload = function(e){  
          // 变成字符串  
          // callback(reader.result);  
          callback(e.target.result);
      }  
   }
   var imgFile = document.getElementById("fileInput").files[0];
   fileInput.addEventListener("change", function (event) {
     var file = fileInput.files[0];
     getBase64Img(file, function(imgBase64){
        if (imgBase64.length > 2100000) {
              // 2 M = 2097152 B
              alert( '请上传不大于 2M 的图片！');
              return;
        }else{
            // 截取 data:image/png;base64 后面的纯字符串
            var upload_file = imgBase64.substring(imgBase64.indexOf(",") + 1);
            // 上传截取后的字符串
            console.log(upload_file);
        }
    })
   }, false)
  ```


### 网络或本地图片
  ```js
  // 图像文件转 Base64
  function getBase64Img(img) {
      var canvas = document.createElement("canvas");
      canvas.width = img.width;
      canvas.height = img.height;
      var ctx = canvas.getContext("2d");
      ctx.drawImage(img, 0, 0, img.width, img.height);
      var ext = img.src.substring(img.src.lastIndexOf(".") + 1).toLowerCase();
      var dataURL = canvas.toDataURL("image/" + ext);
      return dataURL;
  }

  // Base64 字符串转二进制
  function base64ToBlob(dataurl) {

      // 去掉 url 的头并转化为byte
      var arr = dataurl.split(',');

      // 处理异常：将 ASCII 码小于0的转换为大于0
      var mime = arr[0].match(/:(.*?);/)[1];

      // 解码
      var bytes = window.atob(arr[1]);
      var n = bytes.length;

      // 生成视图（直接针对内存）：8位无符号整数，长度1个字节
      var u8arr = new Uint8Array(n);

      while (n--) {
          u8arr[n] = bytes.charCodeAt(n);
      }
      return new Blob([u8arr], { type: mime });
  }
  
  var image = new Image();
  image.src = "img_url";
  image.onload = function() {
      var base64 = getBase64Img(image);
      var file = base64ToBlob(base64);
  }
  ```


# 五、二进制对象
<div style="text-indent: 2em">js 最初无法处理文件、网络流等二进制 (编码格式的) 数据，只能先使用 charCodeAt() 逐字节读取 或者 转换为 Base64 格式，但是处理速度慢而且容易出错。js 后来通过引入 API 逐渐可以处理二进制数据，但是标准库里没有相关内容，所以 NodeJs 就自己搞了一套，现在 Web 自己也搞起来了，这就导致了 Web 环境和 Node 环境里的二进制内容处理方式有些不同。</div> 

## 通用
<div style="text-indent: 2em">`ArrayBuffer 对象` 表示内存中存储了一段二进制数据的原始缓冲区，它不能直接读写，只能通过 `TypedArray/DataView 视图` 转换为类型化数组之后来读写。同一段内存的不同数据有不同的读取方式，对应不同的视图。视图的作用是以指定格式读写二进制数据但本身并不存储，本质是二进制数据的抽象数据结构，两种视图的区别在于字节序，TypedArray 视图的数组成员都是同一个数据类型，DataView 视图的数组成员可以是不同的数据类型。。</div> 

### ArrayBuffer
> 用于存储二进制数据，本质是一串数字组成的乱码但没有格式，不能直接被访问

  ```js
  // 创建一个 8 字节的缓冲区对象
  let buffer = new ArrayBuffer(8);

  // 字节长度 8
  console.log(buffer.byteLength);   

  // 参数是否为视图实例
  ArrayBuffer.isView(buffer) 

  // 裁剪缓冲区
  let buffer1 = buffer.slice(0, 1)  
  ```


### TypedArray
> 成员为相同的数据类型

  * 本质：一组类的统称，它们的实例都是类数组对象
  * 语法：和普通数组语法相同，但是其元素的数据类型相同而且没有空位
  * API：
    * 有符号整数：Int8Array、Int16Array、Int32Array
    * 无符号整数：Uint8Array、Uint16Array、Uint32Array
    * 浮点数：Float32Array、Float64Array
 
  ```js
  // 直接生成视图，默认生成底层 ArrayBuffer 实例并赋值
  var f = new Float64Array(8)

  // 通过 buffer 生成视图，默认全部内容，参数为 buffer、开始索引、长度
  let buffer = new ArrayBuffer(4);
  let a = new Int8Array(buffer)   
  var b = new Uint32Array(buffer, 0, 1)
  var c = new Uint8Array(buffer, 1, 1)

  // 操作视图元素
  for (var i=0; i &lt; f.byteLength; i++){ }
  f[1] = 10        // [0, 10]

  // 与普通数组互转
  var arr = Array.from(f) 
  arr_2 = Array.apply([], t)
  var u = new Uint8Array([ 1, 2, 3 ])

  // 与字符串互转
  var str = String.fromCharCode.apply(null, buffer)
  function strToBuf (str) {
    // 每个字符占用2个字节
    var buf = new ArrayBuffer(str.length*2)
    var bufView = new Uint16Array(buf)
    for(var i=0; i &lt; str.length; i++){
        bufView[i] = str.charCodeAt(i)
    }
      return bufView;
  }
  ```

### DataView 
> TypedArray 的强化版，成员可以是不同的数据类型，而且读写时可以自行设定 大/小 端字节序
 
  ```js
  let buffer = new ArrayBuffer(4)
  let dataView = new DataView(buffer)

  // 独有方法，写入参数是 开始位置、写入数据、大/小端读取
  dataView.getInt8(0)
  dataView.setInt8(0, 10)
  dataView.setInt32(0, 25, false) 
  ```

## web 环境特有

### Blob 
> 表示一个不可变的文件对象，它提供了一系列操作文件的接口

  1. 创建方式
    * 构造函数：`new Blob(dataArr, options)`
      * dataArr：必选数组，数组成员可以是二进制对象或字符串
      * options：可选对象，用于设置数组中数据的 MIME 类型
    * slice 方法：`blob.slice(start, end, contentType)` 
      * 从已有的 Blob 对象调用 slice 接口切出一个新的 Blob 对象
      * contentType：可选，规定新 Blob 对象的类型
    * canvas 对象：`canvas.toBlob(callback, type, options)`
      * 把当前绘制信息转为一个 Blob 对象
  2. 属性与方法
    * 属性
      * size：对象的字节长度
      * type：对象的数据类型 
      * isClosed: 该对象是否调用过 close()
    * 方法
      * close：释放对象
      * slice：裁剪并返回新对象
  
  ```js
  // String 转为 Blob
  var blob = new Blob(["Hello World"], {type:"text/plain"});

  // ArrayBuffer 转为 Blob
  var buffer = new ArrayBuffer(8)
  var blob = new Blob([buffer], {type:"text/plain"});

  // TypeArray 转为 Blob
  var uArr = new Uint16Array([97, 32, 72, 100]);
  var blob = new Blob([uArr]);

  let blob = blob_2.slice(1, 5);

  var canvas = document.getElementById("canvas");
  canvas.toBlob(function(blob){
     console.log(blob);
  })
  ```


### Blob 派生对象
> blob 对象是它们的底层对象，它们都是继承 blob 对象并进行了扩展，但其内容一样无法修改

  1. __File 对象__
    * 功能：保存文件的相关信息并允许 Js 访问
    * 分类
      * input 元素上选择文件后返回的 FileList 对象 
      * 普通元素被自由拖拽时返回的 DataTransfer 对象
      * HTMLCanvasElement 上执行 mozGetAsFile() 方法后返回的结果
  2. __FileReader 对象__
    * 功能：提供异步读取文件内容或二进制数据的接口
    * 保存：读取的文件内容会被保存到 result 属性中
    * 异步读取
      * readAsText(file, encoding)：返回 纯文本。encoding 指定编码格式（默认 'UTF-8'）
      * readAsDataURL(file)：返回 基于 Base64 编码的 dataURL 字符串（读取图像时常用）
      * readAsBinaryString(file)：返回 文件的原始二进制字符串（每个字符表示一字节）
      * readAsArrayBuffer(file)：返回 包含文件内容的 ArrayBuffer 对象
    * 中止读取：abort() 
    * 回调函数
      * onload：成功时
      * onerror：出错时
      * onprogress：更新时
      * onloadstart：开始时
      * onloadend：完成时
      * onabort：中止时
  3. __URL 对象__
    * 功能：通过内存文件创建出一个临时指向 File/Blob 对象的 url
    * 静态方法
      * createObjectURL：生成
      * revokeObjectURL：释放
    * 注意事项
      * 网页一旦刷新或关闭，已生成的 url 就会失效
      * 同样的 blob 在不同的事件调用中会得到不同 url


  ```js
  // 选择文件后获取文件信息
  var fileInput = document.querySelector("#fileInput");

  fileInput.addEventListener("change", function (event) {
      var fileList = fileInput.files
      var file = fileList[0];
      console.log(file.name, file.size)
  }, false)

  // 拖动结束时获取被拖动的数据
  var box = document.getElementById("#box");
  box.ondrop = function(event){
      // 拖动的默认处理方式是在新窗口打开
      event.preventDefault()    

      console.log(event.dataTransfer.files)
  }

  // 获取文件二进制数据
  var reader = new FileReader();
  reader.readAsArrayBuffer(file);

  reder.onprogress = function(e){ 
    console.log(reader.result)  
    console.log(e.target.result)
  }
  reader.onload = function (e) { 
    console.log(reader.result)  
    console.log(e.target.result)
  }

  // 在网页插入图片
  var img = document.createElement("img");
  img.src = window.URL.createObjectURL(file);
  img.width = 60;
  img.onload = function(e) {
      window.URL.revokeObjectURL(this.src);
  }
  document.body.appendChild(img);
  ```

 
## Node 环境特有

### Buffer 
> 它继承于 Uint8Array 并实现了更多的接口，大小固定，表示一块未加工的内存缓存区。



