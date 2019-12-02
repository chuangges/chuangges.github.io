---
title: Web 公共方法
tags:
  - 项目开发
categories: 项目开发
top: false
keywords:
  - 公共方法
date: 2019-03-06 16:20:16
description: 页面功能类、数据处理类、性能优化类
---

# 一、页面功能类

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


# 二、数据处理类

## 类型判断

  ```js
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
  // 字符串的首字母大写
  function firstLetterUpperCase (args) {
    const [first, ...rest] = args
    return first.toUpperCase() + rest.join('')
  }
  ```

## Ajax 交互
  ```js
  // 获取 URL 参数值
  function getUrlKey(name) {
    return decodeURIComponent((new RegExp('[?|&]' + name + '=' + '([^&;]+?)(&|#|;|$)').exec(location.href) 
          || [, ""])[1].replace(/\+/g, '%20')) || null
  }
  ```

## 日期数据
  ```js
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
  * 获取两个日期的差距值
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
    * 转换日期格式
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


# 三、性能优化类

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

   



   