---
title: Flex 布局和动画
tags:
  - 布局和动画
categories: 移动端
top: false
keywords:
  - flex
date: 2019-05-11 23:16:17
description: flex 布局、常见动画
---

&lt;div align="center">如果想让一个盒子变成弹性盒子可以用 `display: flex`，弹性盒子中有主轴(横轴row) 和副轴(纵轴column)，可以根据需要设置盒子的容器(父元素)和项目(子元素)
&lt;/div&gt; 


# 一、Flex 布局

## 基础属性
  ```scss
  // 容器属性
  .parent{
    display: flex;

    // 元素排列方向(左右和上下)，切换主副轴
    flex-direction: row | row-reverse | column | column-reverse;

    // 是否换行：reverse 往上一行换行
    flex-wrap: nowrap | wrap | wrap-reverse;

    // 复合属性，无顺序关系
    flex-flow: flex-direction flex-wrap; 

    // 副轴上(即子项垂直) 的对齐方式
    align-items: stretch | flex-start | flex-end | center | baseline;

    // 主轴上(即子项水平) 的对齐方式
    justify-content: flex-start | flex-end | center | space-between | space-around;

    // 多行垂直(副轴) 对齐方式, 默认 stretch
    align-content: flex-start | flex-end | center | space-between | space-around;
  }

  // 项目属性
  .child{
    // 定义排列顺序, 数字越大越靠后(可为负值)
    order: number;

    // 定义放大比例 (单行有多余空间时)
    flex-grow: number;

    // 定义缩小比例 (单行空间不足时)    
    flex-shrink: number;

    // 设置主轴上项目宽度(建议代替宽度)   
    flex-basis: %/px/rem;

    // 前三者复合属性，默认0 1 auto; 
    flex: grow [shrink] [basis];

    // 定义项目自己的对齐方式，可覆盖 align-items
    align-self: stretch | flex-start | flex-end | center | baseline;
  }
  ```


## 布局实例

  ```scss
  // div.box、span.item
  .box-1 {
    display: flex;
    justify-content: center;
    align-items: center;
  }

  .box-2 {
    display: flex;
    justify-content: space-between;
  }

  .box-3 {
    display: flex;
    .item:nth-child(2) {
      align-self: center;
    }
    .item:nth-child(3) {
      align-self: flex-end;
    }
  }

  .box-4 {
    display: flex;
    flex-flow: row-reverse wrap;
    justify-content: space-between;
    align-content: space-between;
  }

  // div.box、div.column 分别两个 span.item
  .box-5{
    display: flex;
    flex-wrap: wrap;
    align-content: space-between;

    .column{
      flex-basis: 100%;
      display: flex;
      justify-content: space-between;
    }
  }

  .box-6 {
    display: flex;
    flex-wrap: wrap;
    justify-content: space-between;
    align-content: space-between;
  }
  .box-7 {
    display: flex;
    flex-flow: column wrap;
    justify-content: space-between;
    align-content: space-between;
  }

  // div.column 分别包含 2、1、3 个子元素
  .box-8{
    display: flex;
    flex-wrap: wrap;
    align-content: space-between;

    .column{
      flex-basis: 100%;
      display: flex;
      justify-content: space-between;
    }
    .column:nth-child(2){
      justify-content: center;
    }
  }
  ```

  &lt;div align="center"> 
    ![flex 布局](/images/mobile/flex.png)
  &lt;/div&gt; 


## 网格布局
> 平均分布，即在容器里面平均分配空间

  ```scss
  // 几个子元素就平分几份
  .box{
    display: flex;
    .item{
      flex: 1;
    }
  }

  // 某个网格的宽度为固定的百分比，其余网格平均分配剩余的空间
  .box{
    display: flex;
    .item{
      // flex: 1 1 0%;
      flex: 1;

      &.fixed{
        flex: 0 0 20%;
      }
    }
  }
  ```


## 圣杯布局

  ```scss
  body{
    display: flex;
    min-height: 100vh;
    flex-direction: column;

    header, footer {
      flex: 1;
    }

    .content{
      flex: 1;
      display: flex;
      
      .main{
        flex: 1;
      }
      .left, .right{
        // 两个边栏的宽度设为 12em
        flex: 0 0 12em;
      }
      .left{
        order: -1;
      }
    }
  }

  // 小屏幕时内容区三栏自动变为垂直叠加
  @media (max-width: 768px) {
    .content {
      flex-direction: column;
      flex: 1;

      .left, .main, .right {
        flex: auto;
      }
    }
  }
  ```

  &lt;div align="center"> 
    ![圣杯布局](/images/mobile/grail.png)
  &lt;/div&gt; 



## 流式布局
> 每行的项目数固定，会自动分行

  ```scss
  .parent {
    width: 200px;
    height: 150px;
    background-color: black;
    display: flex;
    flex-flow: row wrap;
    align-content: flex-start;
  }

  .child {
    box-sizing: border-box;
    background-color: white;
    flex: 0 0 25%;
    height: 50px;
    border: 1px solid red;
  }
  ```



## 样式布局

  ```scss
  // 输入框的前方添加提示，后方添加按钮
  .input-item{
    display: flex;

    input{
      flex: 1;
    }
  }

  // 主栏的左侧或右侧添加一个图片栏 (类似头像)
  .item{
    display: flex;
    align-items: flex-start;

    img{
      margin-right: 1em;
    }
    p{
      flex: 1;
    }
  }

  // 固定底栏：页面内容太少时底栏会抬高到页面的中间
  body{
    display: flex;
    min-height: 100vh;
    flex-direction: column;

    // header、footer、div.content
    .content{
      flex: 1;
    }
  }
  ```


# 二、动画

## 实现方式

### JS
  1. 通过定时器 `setTimeout、setIterval` 间隔来改变元素样式
    * 经常导致页面频繁性重排重绘而消耗性能
    * 性能优化时一般使用 16ms 进行节流处理连续触发的浏览器事件 touchmove、scroll等
  2. 通过浏览器用于定时循环操作的接口 `requestAnimationFrame` 实现最佳动画效果
    * 它可理解为一个性能优化版、专为动画量身打造的 setTimeout
    * 它并不指定回调函数运行时间而是跟着浏览器内建的刷新频率来执行回调
  3. 复杂动画交互建议使用RAF及setInterval或setTimeout降级处理


### CSS3
> 最大优势是摆脱了 js 的控制, 并能利用硬件加速以及实现复杂动画效果。比如在移动端开发中使用 transition 会让页面变慢甚至卡顿，通过添加 `transform: translate3D(0,0,0) / translateZ(0)` 来开启移动端动画的 GPU 加速，让动画过程更加流畅。

  * 过渡：`transition`
  * 动画：`animation`


### HTML5

  * `svg`：使用 XML 格式定义基于矢量的图形并嵌入到 HTML 使用
    * 原生绘制各种图形和动画并可以被 js 调用，CSS3 出现以后应用较少
    * 它是对图形的渲染 (html是对dom的渲染)，但渲染元素较多且复杂的动画时比较慢 
  * `canvas`：可用于实现复杂动画，可通过 js 渲染控制动画的执行
  * `WebGL`：一种 3D 绘图标准，在 js 中可以直接使用其 API 绘制 3D 图形
    * 可看作是浏览器提供的接口，但是门槛相对较高(需要几何等数学知识)
    * 使用时可以通过封装了 WebGL 接口的 Three.js 等框架


## 常用库

  * Animate.css：CSS3 动画库，运动元素添加类名
  * Tween.js：包含各种经典算法的补间动画，以平滑效果变化
  * Three.js：实现 3D 程序的 js 类库，封装了 webgl 接口
  * swiper.animate.js：移动端触摸滑块类库，初始化回调中使用并在运动元素添加类名


## 常用动画

### CSS3
> loading 加载动画效果素材网站：https://www.eyuyun.com/127.html

  ```html
  // 加载
  &lt;div class="loading-box"&gt;
    &lt;img src="data:image/svg+xml;base64,PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHdpZHRoPSIxMjAiIGhlaWdodD0iMTIwIiB2aWV3Qm94PSIwIDAgMTAwIDEwMCI+PHBhdGggZmlsbD0ibm9uZSIgZD0iTTAgMGgxMDB2MTAwSDB6Ii8+PHJlY3Qgd2lkdGg9IjciIGhlaWdodD0iMjAiIHg9IjQ2LjUiIHk9IjQwIiBmaWxsPSIjRTlFOUU5IiByeD0iNSIgcnk9IjUiIHRyYW5zZm9ybT0idHJhbnNsYXRlKDAgLTMwKSIvPjxyZWN0IHdpZHRoPSI3IiBoZWlnaHQ9IjIwIiB4PSI0Ni41IiB5PSI0MCIgZmlsbD0iIzk4OTY5NyIgcng9IjUiIHJ5PSI1IiB0cmFuc2Zvcm09InJvdGF0ZSgzMCAxMDUuOTggNjUpIi8+PHJlY3Qgd2lkdGg9IjciIGhlaWdodD0iMjAiIHg9IjQ2LjUiIHk9IjQwIiBmaWxsPSIjOUI5OTlBIiByeD0iNSIgcnk9IjUiIHRyYW5zZm9ybT0icm90YXRlKDYwIDc1Ljk4IDY1KSIvPjxyZWN0IHdpZHRoPSI3IiBoZWlnaHQ9IjIwIiB4PSI0Ni41IiB5PSI0MCIgZmlsbD0iI0EzQTFBMiIgcng9IjUiIHJ5PSI1IiB0cmFuc2Zvcm09InJvdGF0ZSg5MCA2NSA2NSkiLz48cmVjdCB3aWR0aD0iNyIgaGVpZ2h0PSIyMCIgeD0iNDYuNSIgeT0iNDAiIGZpbGw9IiNBQkE5QUEiIHJ4PSI1IiByeT0iNSIgdHJhbnNmb3JtPSJyb3RhdGUoMTIwIDU4LjY2IDY1KSIvPjxyZWN0IHdpZHRoPSI3IiBoZWlnaHQ9IjIwIiB4PSI0Ni41IiB5PSI0MCIgZmlsbD0iI0IyQjJCMiIgcng9IjUiIHJ5PSI1IiB0cmFuc2Zvcm09InJvdGF0ZSgxNTAgNTQuMDIgNjUpIi8+PHJlY3Qgd2lkdGg9IjciIGhlaWdodD0iMjAiIHg9IjQ2LjUiIHk9IjQwIiBmaWxsPSIjQkFCOEI5IiByeD0iNSIgcnk9IjUiIHRyYW5zZm9ybT0icm90YXRlKDE4MCA1MCA2NSkiLz48cmVjdCB3aWR0aD0iNyIgaGVpZ2h0PSIyMCIgeD0iNDYuNSIgeT0iNDAiIGZpbGw9IiNDMkMwQzEiIHJ4PSI1IiByeT0iNSIgdHJhbnNmb3JtPSJyb3RhdGUoLTE1MCA0NS45OCA2NSkiLz48cmVjdCB3aWR0aD0iNyIgaGVpZ2h0PSIyMCIgeD0iNDYuNSIgeT0iNDAiIGZpbGw9IiNDQkNCQ0IiIHJ4PSI1IiByeT0iNSIgdHJhbnNmb3JtPSJyb3RhdGUoLTEyMCA0MS4zNCA2NSkiLz48cmVjdCB3aWR0aD0iNyIgaGVpZ2h0PSIyMCIgeD0iNDYuNSIgeT0iNDAiIGZpbGw9IiNEMkQyRDIiIHJ4PSI1IiByeT0iNSIgdHJhbnNmb3JtPSJyb3RhdGUoLTkwIDM1IDY1KSIvPjxyZWN0IHdpZHRoPSI3IiBoZWlnaHQ9IjIwIiB4PSI0Ni41IiB5PSI0MCIgZmlsbD0iI0RBREFEQSIgcng9IjUiIHJ5PSI1IiB0cmFuc2Zvcm09InJvdGF0ZSgtNjAgMjQuMDIgNjUpIi8+PHJlY3Qgd2lkdGg9IjciIGhlaWdodD0iMjAiIHg9IjQ2LjUiIHk9IjQwIiBmaWxsPSIjRTJFMkUyIiByeD0iNSIgcnk9IjUiIHRyYW5zZm9ybT0icm90YXRlKC0zMCAtNS45OCA2NSkiLz48L3N2Zz4=" alt=""&gt;
    &lt;p&gt;loading...&lt;/p&gt;
  &lt;/div&gt;

  // 首页
  &lt;div class="app_loading" id="app_loading"&gt;
      &lt;div class="text_box"&gt;
          &lt;span&gt;loading...&lt;/span&gt;
          &lt;div&gt;&lt;/div&gt;
          &lt;div&gt;&lt;/div&gt;
          &lt;div&gt;&lt;/div&gt;
          &lt;div&gt;&lt;/div&gt;
      &lt;/div&gt;
      &lt;div class="starline"&gt;
          &lt;div&gt;&lt;/div&gt;
          &lt;div&gt;&lt;/div&gt;
          &lt;div&gt;&lt;/div&gt;
      &lt;/div&gt;
  &lt;/div&gt;
  ```

  ```css
  /* 旋转 */
  .loading-box{
      position: fixed;
      top: 0;
      bottom: 0;
      left: 0;
      right: 0;
      z-index: 9999;
      background-color: rgba(255, 255, 255, .6);
      text-align: center;
  }
  .loading-box img{
      width: 120px;
      margin-top: 40vh;
      margin-bottom: 20px;
      animation: rotating 1s steps(12, end) infinite;
  }
  @keyframes rotating {
    0% {
        transform: rotate(0deg);
    }
    100% {
        transform: rotate(1turn);
    }
  }

  /* 圆环 */
  .app_loading {
    position: fixed;
    height: 100vh;
    width: 100vw;
    top: 0;
    left: 0;
    background: #0c0d22;
    opacity: 1;
    transition: opacity 1s;
    z-index: 9999;
    pointer-events: none;
  }
  .text_box span {
    color: #eff1f2;
    display: block;
    position: absolute;
    left: 50vw;
    top: 50vh;
    transform: translate(-50%, -50%);  
    /* 居中布局 */
  }
  .text_box div {
    position: absolute;
    left: 50vw;
    top: 50vh;
    transform: translate(-50%, -50%);
    border-radius: 50%;
    border: 1px solid #fff;
    opacity: 0;
  }
  .text_box div:nth-of-type(1) {
    width: 25vh;
    height: 25vh;
    animation: box_fade 3s 0s ease-in infinite;
  }
  .text_box div:nth-of-type(2) {
    width: 45vh;
    height: 45vh;
    animation: box_fade 3s .4s ease-in infinite;
  }
  .text_box div:nth-of-type(3) {
    width: 65vh;
    height: 65vh;
    animation: box_fade 3s .8s ease-in infinite;
  }
  .text_box div:nth-of-type(4) {
    width: 85vh;
    height: 85vh;
    animation: box_fade 3s 1.2s ease-in infinite;
  }
  @keyframes box_fade {
    0% {
      opacity: 0;
    }
    30% {
      opacity: .5;
    }
    60% {
      opacity: 0;
    }
    100% {
      opacity: 0;
    }
  }
  .starline {
    position: absolute;
    left: 0;
    right: 0;
    bottom: 0;
    top: 0;
    /* background: pink; */
  }
  .starline div {
    position: absolute;
    width: 100px;
    height: 2px;
    border-radius: 1px;
    background-image: -webkit-linear-gradient(right, rgba(225, 225, 225, 0), rgba(225, 225, 225, 1));
  }
  .starline div:nth-child(1) {
    top: -10px;
    right: -30px;
    animation: xmove 12s ease-out infinite;
    transform: rotateZ(-45deg) translate3d(-0, 0, 0);
      }
  @keyframes xmove {
    to {
      transform: rotateZ(-45deg) translate3d(-800px, 0, 0)
    }
  }
  .starline div:nth-child(2) {
    top: 200px;
    right: -120px;
    animation: ymove 8s ease-out infinite;
    transform: rotateZ(-45deg) translate3d(-0, 0, 0);
  }
  @keyframes ymove {
    to {
      transform: rotateZ(-45deg) translate3d(-800px, 0, 0)
    }
  }
  .starline div:nth-child(3) {
    top: 100px;
    right: -100px;
    animation: zmove 16s ease-out infinite;
    transform: rotateZ(-45deg) translate3d(-0, 0, 0);
  }
  @keyframes zmove {
    to {
      transform: rotateZ(-45deg) translate3d(-800px, 0, 0)
    }
  }
  ```


### transition
  ```css
  .box {
    background-color: #ffff00;
    color: #000000;
    width: 200px;
    transition: background-color 1s linear, color 1s ease-in-out, width 1s ease-in;
  }
  .box:hover {
    background-color: #003366;
    color: #ffffff;
    width: 500px;
  }
  ```


## animation
  ```css
  .box {
    height: 100px;
    margin: 20px 0;
    color: grey;
    background-color: pink;
  }
  /* 定义keyframs动画 */
  @keyframes mycolor {   
    0% {
        color: #ffff00;
        background-color: red;
    }
    40% {
        color: #003366;
        background-color: yellow;
    }
    70% {
        color: bisque;
        background-color: darkblue;
    }
    100% {
        color: #fff;
        background-color: blue;
    }
  }
  .box:hover {
    /* animation: 动画名 时间 动画曲线 延迟时间 播放次数 是否逆向播放 [非动画时间状态]; */
    animation: mycolor 5s linear;
  }









