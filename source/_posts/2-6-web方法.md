---
title: Web 公共方法
tags:
  - 项目开发
categories: 项目开发
top: false
keywords:
  - 插件
date: 2019-03-03 16:20:16
description: 法类、页面功能类、数据处理类
---


# 一、算法类
  * 排序算法
    * __冒泡排序__：比较任何两个相邻元素，如果第一个比第二个大则交换位置。元素向上移动到正确顺序，类似气泡上升至表面而得名。
    * __选择排序__：每次从元素中选出最小或最大值，存放在序列的起始位置，以此循环至排序完毕。
    * __插入排序__：将一个数据插入到已经排好序的有序数据中，从而得到一个新的、个数加一的有序数据，适用于少量数据的排序。
    * __归并排序__：将原始序列切分成较小的序列直到无法再切分，然后将小序列排序后归并成大序列，直到最后只有一个排序完毕的大序列。
    * __快速排序__：通过一趟排序将要排序的数据分割成独立的两部分，其中一部分的所有数据都比另外一部分的所有数据都要小，然后再按此方法对这两部分数据分别进行上述递归排序，以此达到整个数据变成有序序列。
  * 搜索算法
    * __顺序搜索__：让目标元素与列表中的每一个元素逐个比较，直到找出与给定元素相同的元素为止，缺点是效率低下。
    * __二分搜索__：在一个有序列表，以中间值为基准拆分为两个子列表，拿目标元素与中间值作比较从而再在目标的子列表中递归此方法，直至找到目标元素。
  * 其他
    * __贪心算法__：在对问题求解时，不考虑全局，总是做出局部最优解的方法。
    * __动态规划__：在对问题求解时，由以求出的局部最优解来推导全局最优解。
    * __复杂度__：一个方法在执行的整个生命周期所需要占用的时间、空间等资源。


## 冒泡排序
  ```js
  Array.prototype.bubbleSort = function() {
      for (let i = 0; i < this.length; i++) {
          for (let j = 0; j < this.length - 1 - i; j++) {
              if (this[j] > this[j + 1]) {
                  let temp = this[j]
                  this[j] = this[j + 1]
                  this[j + 1] = temp
              }
          }
      }
  }
  ```


## 选择排序
  ```js
  Array.prototype.selectionSort = function() {
      let indexMin
      for (let i = 0; i < this.length - 1; i++){
          indexMin = i
          for (var j = i; j < this.length; j++){ 
              if(this[indexMin] > this[j]) {
                  indexMin = j
              }
          } 
          if (i !== indexMin){
              let aux = this[i]
              this[i] = this[indexMin]
              this[indexMin] = aux
          }
      }
      return this
  }
  ```


## 插入排序
  ```js
  Array.prototype.insertionSort = function() {
      let j
      let temp
      for (let i = 1; i < this.length; i++) {
          j = i
          temp = this[i]
          while (j > 0 && this[j - 1] > temp) {
              this[j] = this[j - 1]
              j--
          } 
          this[j] = temp
          console.log(this.join(', '))
      }
      return this
  }
  ```


## 归并排序
  ```js
  Array.prototype.mergeSort = function() {
      const merge = (left, right) => {
          const result = []
          let il = 0
          let ir = 0
          while(il < left.length && ir < right.length) {
              if(left[il] < right[ir]) {
                  result.push(left[il++])
              } else {
                  result.push(right[ir++])
              }
          }
          while (il < left.length) {
              result.push(left[il++])
          }
          while (ir < right.length) {
              result.push(right[ir++])
          }
          return result
      }
      const mergeSortRec = array => {
          if (array.length === 1) {
              return array
          }
          const mid = Math.floor(array.length / 2)
          const left = array.slice(0, mid)
          const right = array.slice(mid, array.length)
          return merge(mergeSortRec(left), mergeSortRec(right))
      }
      return mergeSortRec(this)
  }
  ```


## 快速排序
  ```js
  Array.prototype.quickSort = function() {
      const partition = (array, left, right) => {
          var pivot = array[Math.floor((right + left) / 2)]
          let i = left
          let j = right
          while (i <= j) {
              while (array[i] < pivot) {
                  i++
              }
              while (array[j] > pivot) {
                  j--
              }
              if (i <= j) {
                  let aux = array[i]
                  array[i] = array[j]
                  array[j] = aux
                  i++
                  j--
              }
          }
          return i
      }
      const quick = (array, left, right) => {
          let index
          if (array.length > 1) {
              index = partition(array, left, right)
              if (left < index - 1) {
                  quick(array, left, index - 1)
              }
              if (index < right) {
                  quick(array, index, right)
              }
          }
      }
      quick(this, 0, this.length - 1)
      return this
  }
  ```


## 顺序搜索
  ```js
  Array.prototype.sequentialSearch = function(item) {
      for (let i = 0; i < this.length; i++) {
          if (item === this[i]) return i
      }
      return -1
  }
  ```


## 二分搜索
  ```js
  Array.prototype.binarySearch = function(item) {
      this.quickSort()
      let low = 0
      let mid = null
      let element = null
      let high = this.length - 1
      while (low <= high){
          mid = Math.floor((low + high) / 2)
          element = this[mid]
          if (element < item) {
              low = mid + 1
          } else if (element > item) {
              high = mid - 1
          } else {
              return mid
          }
      }
      return -1
  }
  ```


## 动态规划
> 美国硬币：d1=1, d2=5, d3=10, d4=25，如果要找36美分的零钱，我们可以用 1个25美分、1个10美分和1个便士（ 1美分)
最少硬币找零的解决方案是找到 n 所需的最小硬币数

  ```js
  class MinCoinChange {

    constructor(coins) {
      this.coins = coins
      this.cache = {}
    }

    makeChange(amount) {
      if (!amount) return []
      if (this.cache[amount]) return this.cache[amount]
      let min = [], newMin, newAmount
      this.coins.forEach(coin => {
          newAmount = amount - coin
          if (newAmount >= 0) {
              newMin = this.makeChange(newAmount)
          }
          if (newAmount >= 0 && 
                (newMin.length < min.length - 1 || !min.length) && 
                (newMin.length || !newAmount)) {
              min = [coin].concat(newMin)
          }
      })
      return (this.cache[amount] = min)
    }
  }
  const rninCoinChange = new MinCoinChange([1, 5, 10, 25])
  console.log(minCoinChange.makeChange(36)) // [1, 10, 25]

  const minCoinChange2 = new MinCoinChange([1, 3, 4])
  console.log(minCoinChange2.makeChange(6))  // [3, 3]
  ```


## 贪心算法
> 通过贪心算法得出上述解决方案

  ```js
  class MinCoinChange {

    constructor(coins) {
      this.coins = coins
    }

    makeChange(amount) {
      const change = []
      let total = 0
      this.coins.sort((a, b) => a < b).forEach(coin => {
          while ((total + coin) <= amount) {
            change.push(coin)
            total += coin
          }
      })
      return change
    }
  }
  const rninCoinChange = new MinCoinChange ( [ 1, 5, 10, 2 5])
  console. log (rninCoinChange. rnakeChange (36))
  ```



# 二、页面功能类


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


# 三、数据处理类

## URL 参数
  ```js
  function getUrlKey(name) {
    return decodeURIComponent((new RegExp('[?|&]' + name + '=' + '([^&;]+?)(&|#|;|$)').exec(location.href) || [, ""])[1].replace(/\+/g, '%20')) || null
  }
  ```

## 类型判断

  ```js
  // 字符串的首字母大写
  function firstLetterUpperCase (args) {
    const [first, ...rest] = args
    return first.toUpperCase() + rest.join('')
  }

  /**
    * @function is 判断数据是否某类型
    * @param it {any} 要判断的数据
    * @param type {string} 某类型
    *
    * @function isAtom 判断数据是否原子类型 (除了对象和数组之外)
    * @function isArray 判断数据是否为数组
    * @function isEquals 判断两个对象是否相同
    */
  function is (it, type) {
    return Object.prototype.toString.call(it) === `[object ${firstLetterUpperCase(type)}]`
  }
  function isAtom (it) {
    return it === null || typeof it !== 'object' || it instanceof Date || it instanceof RegExp
  }
  function isArray(it){
    return Array.isArray(it) || is(it, 'array')
  }
  function isEquals (a, b) {
    // null, undefined, number, boolean, symbol, string, function
    if (typeof a !== 'object' || a === null) {
      return Object.is(a, b)
    } else if (is(a, "regExp")) {
      return is(b, "regExp") && a.source === b.source && a.flags === b.flags
    } else if (is(a, "date")) {
      return is(b, "date") && a.getTime() === b.getTime()
    } else if (isArray(a)) {
      return isArray(b) && a.length === b.length && a.every((datum, index) => datum === b[index])
    } else {
      if (!is(b, "object") || Object.keys(a).length !== Object.keys(b).length) return false
      for (const i in a) {
        if (!equals(a[i], b[i])) return false
      }
      return true
    }
  }


  /**
    * 在某个日期上增减 天数/月份/年份，负数时为减少
    * @param date {string|Date} 字符串表示的日期
    * @param Offset {number} 增加或减少的 天数/月份/年份
    * @returns {Date} 日期对象
    */
  function addDay (date, dayOffset) {
    const toDate = is(data, "data") ? date : new Date(date)
    const unit = 1000 * 60 * 60 * 24
    return new Date(toDate.getTime() + unit * dayOffset)
  }
  function addMonth (date, monthOffset) {
    const anchor = new Date(date)
    const month = anchor.getUTCMonth()
    // 修改新日期对象而不应修改原日期，所以前面 new Date
    anchor.setUTCMonth(month + monthOffset)
    return anchor
  }
  function addYear (date, yearOffset) {
    return addMonth(date, yearOffset * 12)
  }

  /**
  * 得到两个日期的差距值
  * @param a {string|Date} 日期a
  * @param b {string|Date} 日期b
  * @returns {number} 两个日期的差距值
  */
  function compareTime (a, b) {
    const aToDate = is(a, "data") ? a : new Date(a)
    const bToDate = is(b, "data") ? b : new Date(b)
    return aToDate.getTime() - bToDate.getTime()
  }

  /**
    * 日期格式转换
    * @param date {string|Date} 日期
    * @param format {string} 日期格式
    * @returns {Date} 新格式的日期
    */
  function dateFormat (date, format = 'yyyy-MM-dd') {
    if(!date) return
    date = is(date, "date") ? date : new Date(date)
    const info = {
      'y+': date.getFullYear(), //  年
      'Y+': date.getFullYear(), //  年
      'M+': date.getMonth() + 1, // 月
      'D+': date.getDate(), // 日
      'd+': date.getDate(), // 日
      'h+': date.getHours(), // 时
      'm+': date.getMinutes(), // 分
      's+': date.getSeconds(), // 秒
      'q+': Math.floor((date.getMonth() + 3) / 3), // 季度
      a: ['一', '二', '三', '四', '五', '六', '日'][date.getDay() - 1], // 星期
      S: date.getMilliseconds() // 毫秒
    }

    const weekFormatMap = {
      2: week => `周${week}`,
      3: week => `星期${week}`
    }

    // 星期使用一个函数单独格式化
    const formatWeek = (value, format) => {
      const len = Math.min(3, format.length)
      const formatter = weekFormatMap[len]
      return formatter ? formatter(value) : value
    }

    for (var key in info){
      const reg = new RegExp(key)
      if (reg.test(format)) {
        format = format.replace(reg, $0 => {
          const formatted = (Number.isNaN(info[key]) || info[key] === undefined) ? '??' : info[key]
          return key === 'a' ? formatWeek(info[key], key) : formatted.toString().padStart($0.length, '0')
        })
      }
    }
    return format
  }

  // 获取某年某月的天数
  function getDays ({ year, month }) {
    const isLeapYear = year % 4 === 0 && year % 100 !== 0
    const map = {
      1: 31,
      2: isLeapYear ? 29 : 28,
      3: 31,
      4: 30,
      5: 31,
      6: 30,
      7: 31,
      8: 31,
      9: 30,
      10: 31,
      11: 30,
      12: 31
    }
    return map[month]
  }


  // 获取当前时间时间段
  function getTimeSlot () {
    const timeSlot = new Date().getHours()
    if (timeSlot < 6) {
      return '凌晨'
    } else if (timeSlot < 9) {
      return '早上'
    } else if (timeSlot < 12) {
      return '上午'
    } else if (timeSlot < 14) {
      return '中午'
    } else if (timeSlot < 17) {
      return '下午'
    } else if (timeSlot < 19) {
      return '傍晚'
    } else if (timeSlot < 22) {
      return '晚上'
    } else {
      return '夜里'
    }
  }

  // 获取明天的日期
  function getTomorrow (anchor) {
    const date = new Date()
    const now = {
      year: date.getFullYear(),
      month: date.getMonth() + 1,
      day: date.getDate()
    }
    let { year, month, day } = anchor || now
    day += 1
    if (day > getDays({ year, month })) {
      month += 1
      day = 1
    }
    if (month > 12) {
      year += 1
      month = 1
    }
    return `${year}-${month.toString().padStart(2, '0')}-${day.toString().padStart(2, '0')}`
  }

  /**
    * 一个深克隆函数
    * 期望的应用场景是数据克隆，而非对象克隆
    * [边际问题]：处理 Date、Function、Symbol、RegExp、prototype ?
    * 现在的克隆策略：
    * 1. Date，RegExp会重新初始化一个对象
    * 2. 对于Symbol, Function会继续保持相同的引用
    * 3. 对于原型会忽略
    * 除了对象和数组外，都归类为"原子类型" (isAtom(it) => true)
    * @param source {*} 要克隆的源数据
    * @returns {*} 克隆后的数据
    */
  function clone (source) {
    if (source === null || typeof source !== 'object') {
      return source
    } else if (source instanceof Date) {
      return new Date(source)
    } else if (source instanceof RegExp) {
      return new RegExp(source)
    }
    const target = isArray(source) ? [] : {}
    for (const key in source) {
      if (!source.hasOwnProperty(key)) { //  忽略原型
        break
      }
      target[key] = clone(source[key])
    }
    return target
  }

  ```



## 身份证信息
  ```js
  // 根据身份证号获取信息：性别、出生日期、年龄
  function getIdentity(id){
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
      var birthDate = new Date(Number(_birthDate.year), Number(_birthDate.month) - 1, Number(_birthDate.date));
      result['birthDay'] = dateFormat(birthDate)

      toAge()
          
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

  // 根据生日计算周岁
  function toAge (birthday) {
    const now = new Date()
    const anchor = new Date(birthday)
    let age = now.getUTCFullYear() - anchor.getUTCFullYear()
    // 现在的月份，比生日月份更大，虚岁+1
    if (now.getMonth() > anchor.getMonth()) {
      age += 1
    } else if (now.getUTCMonth() === anchor.getUTCMonth()) {
      // 月份相同时，当前天数比生日的天数更大，或者当前天数等于生日的天数时，虚岁+1
      if (now.getUTCDate() >= anchor.getUTCDate()) {
        age += 1
      }
    }
    return age - 1 // 周岁为虚岁-1
  }
  ```


# 性能优化类


### JS 节流和防抖
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

   



   