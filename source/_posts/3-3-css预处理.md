---
title: CSS 预处理工具 
tags:
  - HTML + CSS
categories: HTML + CSS
top: false
keywords:
  - scss
date: 2019-03-08 23:24:50
description: Scss 的基础使用
---

# 一、出现需求
<div style="text-indent: 2em">css 只能用来写样式但不能实现真正的编程，而预处理器为 CSS 增加了编程特性，让它更加简洁的同时不再需要考虑浏览器的兼容问题，适应性和可读性更好，可以进行模块化开发，节省开发时间而且方便后期维护等。目前最主流的三个预处理器有 Less、Sass、Stylus</div>

<div style="text-indent: 2em">最早也是最成熟的是 Sass，它使用 以严格的缩进式语法规则书写而不带 {} 和 ; 的缩排语法，后来为了方便习惯 css 的开发人员而升级为Scss，Scss 兼容 Sass 语法而且书写规则类似css。Less 受 Sass 的影响较大，更容易上手但编程功能不够，Stylus主要用来给 Node 项目进行 css 预处理支持</div>
    

# 二、Scss 基础语法

## 引用 `@import`
  ```scss
  // 直接引用文件
  @import "index.css", "index2.scss";   // 导入多个文件
  @import "http://foo.com/bar";
  @import url(foo);
  @import "_index.scss";  // 避免引入文件被编译
  @import "colors";       // 导入 _colors.scss 且不能同时存在 colors.scss

  // 变量插值
  $device: mobile;
  @import url(styles.#{$device}.css);
  ```

## 注释
  ```scss
  //  单行注释(不会被编译到css文件中);
  /*  多行注释(在非压缩模式下会被编译到css文件中)  */
  /*! 重要注释(各种压缩模式下都会被编译到css文件中)  */
    ```


## 变量
  * __变量名__：以$开头,可用作选择器/属性(值)/字符串;
  * __变量值__：数字、字符串(可不带引号)、布尔值、空值(null)、List(列表，类似数组)、Map(映射，类似对象)
  * __注意__：当使用 #{} 时,带引号的字符串将被编译为 不带引号的字符串(为了便于使用);
  
```scss
$width: 1px;             //全局变量(定义在所有选择器外)
$width2: 2px;    
$width2: 3px !default;   //默认变量(无值时起作用)
$pos: bottom;
.aa {
    $fs: 14px;        //局部变量(定义在选择器内)
    $lh: 1.5;

    width: $width;                  //常规使用($var)
    border-#{$pos}: 1px solid red;  //拼字符串(#{$var})
    font: #{$fs}/#{lh} sans-serif;  //复杂属性值(#{$var})
}
.bb {
    $width: 3px !global;           //覆盖全局变量
    width: $width + $width2;       //用于计算($var + $var)
}
@mixin link-style($sel) {
    li #{$sel} {                  //作为选择器(#{$var})
        color: red;
    }
}
@include link-style("a");
```


## 嵌套 
  ```scss
  // 选择器嵌套
  div {
      color: red;
      p {
          color: green;
      }
      &:hover {     //使用 & 表示在嵌套中对父元素的引用
          color: blue;
      }
      @at-root .d {  //选择器嵌套多层后让某个选择器跳出根元素
          color: green;
      }
  } 
  // 属性嵌套
  .div {
      border: {   //嵌套属性后须写冒号
        style: solid;
        left: {
          width: 4px;
          color: #888;
        }
      }
  }
  ```

## 混合宏 `@mixin`
  * @mixin 定义可重用的代码段，@include 调用  

  ```scss
  // 无参 
  @mixin aa {
      margin: 10px;
  }
  .bb {
      @include aa;
  }

  // 参数为数组 
  $margin: 100px;
  $left: 10px;
  @mixin aa($left, $margin) {
      margin: $margin;
      left: $left;
  }
  .bb {
      @include aa($left, $margin);
  }

  // 参数为对象 
  $map: (left: 10px, width: 100px);
  @mixin aa($left, $width) {
      // 接收参数为key值
      left: $left;
      width: $width;
  }
  div {
      @include aa($map...); // 传递参数为对象名+...
  }

  // 默认参数(不传参数时会用默认参数)
  @mixin aa($left: 10px) {}

  // 不定参数 
  @mixin box-shadow($shadows...) {
      //不定参数,用...
      -moz-box-shadow: $shadows;
      -webkit-box-shadow: $shadows;
      box-shadow: $shadows;
  }
  .shadows {
      @include box-shadow(2px 2px 2px #eee);
  }

  // 浏览器前缀设置 
  @mixin rounded($vert, $horz, $radius: 10px) {
      border-#{$vert}-#{$horz}-radius: $radius;
      -moz-border-radius-#{$vert}#{$horz}: $radius;
      -webkit-border-#{$vert}-#{$horz}-radius: $radius;
  }
  #navbar li {
      @include rounded(top, left);
  }

  #footer {
      @include rounded(top, left, 5px);
  }
  ```


## 继承 `@extend`
  * 不管是否调用，基类的样式都将会出现在编译出来的 CSS 代码中  

  ```scss
  .btn {
    display: inline-block;
    margin: 10px;
    padding: 5px 15px;
  }
  .btn-bor {
    border: 1px solid #ccc;
  }
  .btn-red {
    @extend .btn; //多个扩展
    @extend .btn-bor !optional; 
    // 用optional直接跳过空样式,防止元素不存在而导致报错
    border-color: red;
  }

  .hoverlink {
    @extend a:hover;   //@extend可扩展任何选择器
  }
  a:hover {
    text-decoration: underline;
  }

  // 配合占位选择器(%扩展单一选择器,编译后不在css)使用
  .container div%box {
    -webkit-box-sizing: border-box;
    -moz-box-sizing: border-box;
    box-sizing: border-box;
  }
  .bor-box {
    @extend %box; //此时才会编译产生
  } 
  //编译为 .container div.bor-box { } 
  ```


## 占位符 `%placeholder`
  * 一个独立定义的声明块，不调用时不会编译生成 CSS  

  ```scss
  %mt{
      margin-top: 5px;  
  }
  .block {
      @extend %mt;
      span {
          display:block;
          @extend %mt;
      }
  }
  .header {
      color: orange;
      @extend %mt;
      span {
          display:block;
          @extend %mt;
      }
  }
  ```


# 三、Scss 进阶语法

## 条件判断 
 * 可单独使用或结合 @else (if) 使用
 * 使用 not/or/and 分别表示非/或/与，如 @if not($var)
 * 使用 !=/== 分别表示(不)等于，如 @if $a !=0   

```scss
@mixin blockOrHidden($boolean:true) {
    @if $boolean {
      @debug "$boolean is #{$boolean}";
      display: block; //参数为真，显示
    }
    @else {
      @debug "$boolean is #{$boolean}";
      display: none; //参数为假，不显示
    }
}
.block {
    @include blockOrHidden;
}
.hidden {
    @include blockOrHidden(false);
} 
```


## 循环 list/map 
  ```scss
  // list(用空格/逗号隔开项):
  $name: "a", "b";
  $px: 5px 10px 15px 20px; 
  $sel2: (sel1: "span", sel2: "div") 

  // Map
  $map: (key1: 1, key2: 2, key3: 3);
    
  // for循环
  @for $var from <start> through <end> // 包含end值
  @for $var from <start> to <end>      // 不包含end值
    
  // each循环:
  @each $var in list/map


  // while
  $i: 2;
  @while $i>0 {   	// 条件为 true 就会执行
      .color#{$i} {
        color: #222 * $i;
      }
      $i:$i - 1;     // 不能写成 $i:$i-1，因为会被当成字符串
  }

  // for循环 
  $arr: ( (theme: dark, size: 40px), 
      (theme: light, size: 32px)
  );
  @for $i from 1 through length($arr) {
      $item: nth($arr, $i);           // 获取数组中第i项的值
      .#{map-get($item, theme)} {
        width: map-get($item, size);  // 获取指定键值
        height: map-get($item, size);
      }
  }

  // each循环
  $margins: 5px 10px, 15px 20px;    // 两个项
  div {
      margin: nth($margins, 1) nth($margins, 2);
      padding: nth($margins, 1);
  }
  $sel2: (sel1: "span", sel2: "div");  // Map形式
  @each $s in $sel2 {
      .#{$s} {
        width: 20px;
      }
  }
  $headers: (h1:2em, h2:1.5em, h3:1.2em);
  @each $header, $size in $headers {
      #{$header} {
          font-size: $size;
      }
  }
  ```


## 函数和运算 

  1. 自定义
    * @fuction 定义，@return 返回结果
  2. 三元条件
    * if($condition, $if-true, $if-false)：条件成立时返回值1，否则返回值2
  3. 颜色
    * rgb/rgba()：创建颜色
    * alpha/opacity($color)：获取颜色透明度值
    * rgba($color, $alpha)：改变颜色透明度值
    * mix($color1, $color2, [$weight])：混合颜色
    * lighten/darken($color, $percent)：变浅/加深
  4. 数字
    * random()：随机数
    * min/max($nums...)：最小值/最大值
    * floor/ceil($value)：向下/上整数
    * percentage/abs/round($val)：百分比值/绝对值/取整
  5. 字符串
    * quote/unquote($string)：给字符串添加/删除引号
    * to-upper/lower-case()：字符串大小写字母转换
  6. List
    * length($list)：获取长度
    * nth($list, i)：获取列表项（索引i从1开始）
    * append($px, 11px)：添加新值
    * join($list1, $list2)：列表合并
    * zip($lists...)：合并多个表为多维列表
    * index($list, $value)：返回该值在列表中的索引	
  7. Map
    * map-keys/values($map)：获取所有key/values
    * map-get/remove($map, key)：获取/删除指定项
    * map-has-key($map, key)：判断是否有key
    * map-merge($map1, $map2)：合并map
    * keywords($args)：返回函数参数(可动态设置key和value)
  8. Introspection
    * type-of($value)：返回值的类型 
    * unit($number)：返回值的单位
    * unitless($number)：判断该值是否带有单位 
    * commparable($num, $num)：判断两个值是否可做加减和合并


  ```scss
  @function colors($color) {
      $names: map-keys($social-colors);
      @if not index($names, $color) {
          @warn "Waring: `#{$color} is not a valid color name.`";
      }
      @return map-get($social-colors, $color);
  }
  .btn-weibo {
      color: colors(weibo);
  }

  $social-colors: (
      dribble: #ea4c89,
      facebook: #3b5998,
      github: #171515,
      google: #db4437,
      twitter: #55acee
  );

  @each $name in map-keys($social-colors) {
      .btn-#{$name} {
          color: colors($name);
      }
  }

  @for $i from 1 through length(map-keys($social-colors)) {
      .btn-#{nth(map-keys($social-colors), $i)} {
          color: colors(nth(map-keys($social-colors), $i));
      }
  }
  ```


## 调试
  ```scss
  // 类似于控制台输出信息
  @debug 'This is adebug';
  @warn 'Warn';
  @error 'Error';
  ```


