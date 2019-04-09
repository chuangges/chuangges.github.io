---
title: jQuery 基础使用
tags:
  - jQuery
categories: Javascript
top: false
keywords:
  - js
  - jquery
date: 2019-03-16 22:04:14 
description: 基础概念、选择器、操作、事件机制、插件机制
---

# 一、基础概念
> 一个 javascript 库，它把常用方法封装到一个js文件中，需要时直接调用即可

##  入口函数
  1. js
    * 主流：`window.onload = function(){ }`
  2. jquery
    * 主流：`$("document").ready(function(){ })`   
    * 简化：`$(function(){ })`
  3. 区别
    * 书写个数不同：前者只能出现一次并且会被覆盖，后者可出现任意多次且不存在事件覆盖
    * 执行时机不同：前者在文档树加载完成之后还必须 等待图片等所有资源加载完成后才会执行，而后者在文档树加载完成时执行


## $ 符号
<div style="text-indent: 2em"> jQuery 的别名，本质是一个函数，调用时通过传入不同参数而实现不同功能，可传参数如下：</div>
  
  * 字符串：`$("#id")`
  * DOM 对象：`$(domObj)` 
  * function：`$(function(){ })`


## 对象转换   
  * DOM 转为 jq：`var $btn = $(btn)`
  * jq 转为 DOM：`var btn = $btn[0] / $btn.get(0)`


# 二、常用选择器
  * 基础：`$(".class")、$("#id")、$("tagName")、$('li.active')、$('li, .active')、$("li span")、$("li>span")`
  * 过滤：`$('p:first')、$(':empty')、$('p:hidden')、$("div[id^='code']")、$("li:odd")、$("li:eq(0)")、$("ul:nth-child(0)"、$("ul:first-child")`
  *  筛选：`children、find、next、siblings、parent、closest、filter、not、has、eq`


# 三、操作样式
## css操作
  * 获取单个样式：`css("width")`
  * 设置单个样式：`css("width", "20")`
  * 设置多个样式：`css({"color": "red", "w": "10px"})`

## class操作
  * 添加：`addClass()`   
  * 移除：`removeClass()`，不传参数则移除所有的样式
  * 切换：`toggleClass()`，没有则添加
  * 判断：`hasClass()、is()`


# 四、动画
  * 显示隐藏：`show/hide(speed, callback)`
  * 滑入滑出：`slideDown/Up/Toggle(speed, callback)`
  * 淡出淡入：`fadeIn/Out/Toggle(speed, callback)`
  * 自定义动画：`animate(prop, speed, easing, callback)` 


# 五、操作 DOM

  * 创建：`var $h3 = $('h3标签')`
  * 插入
    * `$('父').append/prepend('子')`
    * `$('子').appendTo('父')`
    * `$('同胞').before/after('插入同胞')` 
    * `$('插入同胞').insertBefore/After('同胞')`
  * 删除：`$('#box').remove()、$('ul').remove('l1')`
  * 克隆：`$('h3').clone()`，传入 true 时克隆结构加事件，否则只克隆结构 
  * 清空子元素：`empty()、html("")`，后者不会清理子元素事件而造成内存泄漏，不推荐使用
  


# 六、操作属性
  * 读取属性：`attr(name)`
  * 移除属性：`removeAttr(name)` 
  * 设置单个属性：`$("img").attr(‘src’, ‘test.jpg’)`
  * 设置多个属性：`$('div').attr({'class': 'a', 'style': 'color: red;'})`
  * 方法区别
    * 操作元素的固有属性：`prop`
    * 处理元素的自定义属性：`attr`

   
# 七、操作内容
  * 表单元素：`val(value)`
  * 普通元素：`html(str)、text(str)` 


# 八、事件机制

## 发展历程
  1. 简单绑定：一次只能绑定一个事件，`click(handler)、scroll(handler)`
  2. bind 绑定：不支持动态创建的元素绑定事件，`$("p").bind("click mouseenter", handler)`
  3. on 绑定
    * 自身绑定：`on(事件名称, 事件处理函数)`
    * 事件委托`on(事件名称, 可选后代元素, 传递数据, 事件处理函数)`
    * 事件解绑
      * 解绑匹配元素的所有事件：`off()`
      * 解绑匹配元素的所有 click 事件：`off(“click”)`
      * 解绑元素所有代理的 click 事件但不解绑自身事件：`off( “click”, “**” )`
    * 先解绑再绑定
      * 功能：防止事件累加，导致点击一次而事件执行多次
      * 实现：`$("#btn").off("click").on("click", callback)`


## 事件触发
  * 直接调用：`$("div").click()`
  * trigger：会触发浏览器默认操作，`$("input").trigger("focus")`
  * triggerHandler：不会触发浏览器的默认行为，`$("input").triggerHandler("focus")`


## 事件委托
  * 把事件绑定在父元素上
  * 点击子元素则触发事件冒泡
  * 父元素的事件响应这个事件冒泡
  * 响应事件冒泡时获取触发该事件的元素
  * 如果该元素和 on 绑定的元素相匹配则触发事件处理函数
  


# 九、编程思想

## js 与 jquery
  * 可共存：`$('div').html()、div.innerHTML`
  * 不可混用：`div.html()、$('div').innerHTML`
  * 对应关系：`onclick 和 click()、innerHTML 和 html()`
  * this 绑定：`fn(obj){ var $this = $(obj) }`

## 链式编程
  * 原理：`return this`
  * 示例：`$('li').parent().css("width", "10px").end().next().hide()`
    

## 隐式迭代
  * 理解：方法内部为所有匹配元素进行循环遍历
  * 注意：设置的是全部元素，获取的是第一个元素的值
  * 示例：`$('div').css('color', 'red')、$('li').css('width')`
 

## each 遍历
  * 数组：`$.each(arr, function(i, v){ })`     
  * 对象：`$.each(obj, function(key, val){ })`   
  * 元素：`$('div').each(function(i, obj){ })`


## 多库共存
  1. 问题：如果引用的其他库也用到 $，则前面引入库中的 $ 会被覆盖
  2. 解决
    * 若想让其他库使用 $ 则可以把 jQuery 的 $ 给释放掉
    * 同时存在多个版本的 jQuery 则可以使用 noConflict 来区分



# 十、插件机制
> 为 jQuery 原型添加功能函数来扩展功能


## 插件开发
  1. 类级别  
    ```js
    // 调用：$.hello()
    (function($){
        $.extend({
            hello: function(){
              console.log("hello world")
            }
        })
    })(jQuery)

    // 调用：$.myExtend.hello() 
    (function($){
        $.myExtend({
            hello: function(){
              console.log("hello world")
            } 
        })
    })(jQuery)
    ```
  2. 对象级别
    ```js
    // 调用：$("#box").myAnimate()
    (function($){
        // 写法一
        $.fn.extend({
            myAnimate: function(){ } 
        })
        // 写法二
        $.fn.myAnimate = function(){ }
    })(jQuery) 
    ```


## 插件模板
  1. 分号：开头添加是为了避免别人的代码不规范引起错误，结尾添加是为了 避免压缩出错
  2. 传参
    * 没有第四个参数
    * undefined：保证原生，防止他人把其定义为别的
    * window、document：快速查找，而且也便于代码压缩，比如 window 可能被压缩为 w  
    ```js
    ;(function(win, doc, $, undefined){ 
        // 写法一
        $.fn.extend({
            "pluginName": function(opt){}
        })

        // 写法二
        var defaults = { }
        $.fn.pluginName = function(opt){
          // 合并默认配置和用户配置
          var set = $.extend({}, defaults, opt)

          // 支持链式调用
          return this; 
        } 
    }(window, document, jQuery))
    ```

 
## 方法
  * `$.extend( obj )`：扩展 jquery 类本身，只有通过 jq/$ 类来调用
  * `$.fn.extend( obj )`：扩展 jquery 原型对象，每个实例化的 jq 对象都可以调用
  * `$.extend( target, [objN])`：使用一个或多个其他对象来扩展第一个对象
  * 关系：`$.fn.pluginName = $.fn.extend(pluginName) = $.prototype.extend(pluginName)`



