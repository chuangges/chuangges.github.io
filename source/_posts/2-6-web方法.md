---
title: Web 公共方法
tags:
  - 项目开发
categories: 项目开发
top: false
keywords:
  - 公共方法
date: 2019-03-06 16:20:16
description: Lodash 工具库、页面功能类、数据处理类、性能优化类
---

# 一、Lodash
> 一个轻量级的 Js 原生工具函数库，不需要引入其他第三方依赖。它内部封装了很多对字符串、数组、对象等常见数据类型的处理函数，可以极大提高开发效率。

  * 辅助函数
    * __Array__：适合于数组，比如填充数据、查找元素、数组分片等。
    * __Collocation__：适用于数组和对象，部分适用于字符串，比如分组、查找、过滤等。
    * __Function__：适用于函数，比如节流、延迟、缓存、设置钩子等。
    * __Lang__：普遍适用于各种类型，常用于执行类型判断和类型转换。
    * __Math__：使用与数值类型，常用于执行数学运算。
    * __Number__：适用于生成随机数，比较数值与数值区间的关系。
    * __Object__：适用于对象类型，常用于对象的创建、扩展、类型转换、检索、集合等。
    * __Seq__：常用于创建链式调用，提高执行性能（惰性计算）。
    * __String__：适用于字符串类型。
  * lodash/fp 模块：提供了更接近函数式编程的开发方式，主要特点如下。
    * __Rearragned Arguments__：重新调整参数位置，便于函数之间的聚合。
    * __Capped Iteratee Argument__：封装 Iteratee 参数。
    * __Fixed Arity__：固化参数个数，便于柯里化。
    * __New Methods__

  ```js
  // 安装引用
  npm i lodash -S
  npm i babel-plugin-lodash -D

  // .babelrc
  {
    "plugins":[ "lodash" ]
  }

  // 常见用法
  import _ from 'lodash';
  import { add } from 'lodash/fp';

  // 1、N 次循环
  _.times(5,function(a){
    console.log(a);
  })

  // 2、深层查找属性值
  var ownerArr = [
    {
      "owner": "Colin",
      "pets": [{"name": "dog1"}, {"name": "dog2"}]
    }, 
    {
      "owner": "John",
      "pets": [{"name": "dog3"}, {"name": "dog4"}]
    }
  ]
  var lodashMap = _.map(ownerArr, 'pets[0].name')

  // 3、深克隆对象
  var objB = _.cloneDeep(objA)

  // 4、在指定范围内获取一个随机值
  var randomNum = _.random(15, 20)

  // 5、扩展对象
  var objA = {"name": "戈德斯文", "car": "宝马"}
  var objB = {"name": "柴硕", "loveEat": true}
  var target = _.assign(objA, objB)

  // 6、从列表中随机的选择列表项
  var smartTeam = ["戈德斯文", "杨海月", "柴硕", "师贝贝"]
  var randomSmarter = _.sample(smartTeam)

  // 7、判断对象中是否含有某元素：查询的对象、元素、开始下标
  var smartPerson = {
    'name': '戈德斯文',
    'gender': 'male'
  }
  var sm = _.includes(smartPerson, '戈德斯文')
  var st = _.includes(smartTeam, '杨海月', 2)

  // 8、遍历循环
  _.forEach([1, 3] , function(value, key) {
    console.log(key,value);
  })

  // 9、遍历循环执行某个方法
  var users = [
    { 'user': 'barney' },
    { 'user': 'fred' }
  ]
  var mapUser = _.map(users, 'user')  // ['barney', 'fred']

  // 10、检验值是否为空
  _.isEmpty(null)         // => true
  _.isEmpty(true)         // => true
  _.isEmpty(1)            // => true
  _.isEmpty([1, 2])       // => false
  _.isEmpty({ 'a': 1 })   // => false

  // 11、查找属性
  var users = [
    {'user': 'barney', 'age': 36, 'active': true},
    {'user': 'fred', 'age': 40, 'active': false},
    {'user': 'pebbles', 'age': 1, 'active': true}
  ]

  // find 返回真值的第一个元素
  _.find(users, function (o) {
    return o.age < 40;
  })
  _.find(users, {'age': 1, 'active': true})
  _.find(users, ['active', false])
  _.find(users, 'active')

  // filter：返回真值的所有元素的数组，reject 是其反向方法
  _.filter(users, {'age': 1, 'active': true})
  _.filter(users, ['active', false])
  _.filter(users, 'active')

  // 12、数组去重
  var arr2 = _.uniq(arr1);
  _.uniqBy([2.1, 1.2, 2.3], Math.floor)  // [2.1, 1.2]
  ```




# 四、性能优化类

## JS 节流和防抖
> 在网页实际运行的某些场景下，有些事件是会被不间断的被触发的，而不是我们认为的滚动一次触发一次。这种情况下，由于过于频繁地 DOM 操作和资源加载，严重影响了网页性能，甚至会造成浏览器崩溃。两者都是某个行为持续地触发，区别在于只需要判断是要优化到减少它的执行次数还是只执行一次。

  * __节流__
    * 基础理解：一个水龙头在滴水，可能一次性会滴很多滴，但是我们只希望它每隔 500ms 滴一滴水并保持这个频率。即我们希望函数以一个可以接受的频率重复调用，通过节流函数减少回调函数的执行次数。
    * 应用场景：拖拽元素时 `drag` 事件、监听滚动时 `scroll` 事件、鼠标移动时 `mousemove` 事件、手指滑动时 `touchmove` 事件
  * __防抖__
    * 基础理解：将一个弹簧按下，继续加压，继续按下，但只会在最后放手的一瞬反弹。即我们希望回调函数即使在设定时间内反复调用，也只会在间隔时间超过设定时间后调用一次，通过防抖函数让回调函数实现延时执行。
    * 应用场景：搜索框输入内容时 `keyup` 事件、浏览器窗口调整大小时 `resize` 事件


  ```js
  // 节流：首次不执行
  function throttle(callback, duration=200){
    let timer = null;
    return function(){
      if(timer) return;
      timer = setTimeout(()=>{
        callback.apply(this,arguments);
        timer = null;
      }, duration);
    }
  }
  let handleScroll = throttle(handleScroll)
  window.addEventListener("touchmove", handleScroll);

  // 防抖：首次不执行
  function debounce(callback, delay=200){
    let timer = null;
    return function(){
      if(timer) clearTimeout(timer);
      timer = setTimeout(()=>{
        callback.apply(this,arguments);
        timer = null;
      }, delay);
    }
  }
  let handleSeach = debounce(seachAjax, 500)
  input.addEventListener("keyup", e=>{ handleSeach(e.target.value)})
  ```

   



   