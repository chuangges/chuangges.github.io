---
title: CSS 样式
tags:
  - HTML + CSS
categories: HTML + CSS
top: false
keywords:
  - html
  - css
date: 2019-05-08 22:32:08
description: CSS3 要点、开发常用样式、动画

---

# 一、浏览器兼容

## 常见问题
  * img 之间出现间距：浮动。
  * 上下 margin 重合问题：上下选一个。
  * opacity、placeholder、outline、伪元素、z-index：`css hack`。
  * li 元素使用 display: inline-block 时会出现缝隙：`浮动、font-size: 0`。
  * 子元素的 margin-top 会出现在父元素的上方：父元素 `overflow: hidden / border: 1px solid transparent / padding-top`。


## css hack
> 针对不同的浏览器写不同的 CSS 来解决浏览器兼容。

  * 选择器前缀法：@media screen
  * CSS 属性前缀法：-webkit-、-moz-、-ms-、-o-
  * IE 条件注释语句：解决 IE 兼容问题


# 二、CSS3

## css 单位
  * __rem__: 相对于根元素字体尺寸
  * __em__: 相对于父级元素的字体尺寸
  * __%__: 百分比，相对于父元素的尺寸
  * __px__: 像素，相对于用户设备分辨率的最基本单元
  * pt：Word等办公软件中使用的字体大小单位
  * dp：安卓开发时使用的长度单位
  * sp：安卓开发时使用的字体大小单位
  * vw：视窗宽度，1vw等于视窗宽度的 1%
  * vh：视窗高度，1vh等于视窗高度的 1%
  * vmin / vmax：vw和vh中较 小/大 的那个


## 引用字体
  ```css
  @font-face {                       
    font-family: ArtifaktBlod;     
    src: url('fonts/ArtifaktElementOfc-Bold.ttf');    
  }  
  div {
    font-family: ArtifaktBlod;          
  }
  ```


## transform
  1. __转换属性__
    * transform-origin：指定元素变换的中心点
    * transform-style：指定场景为 2D 或 3D
    * perspective：指定 3D 的视距
    * perspective-origin：设置视距的基点
    * backface-visibility：是否可以看见 3D 场景背面
  2. __2D变换__
    * translate：位移，translate / translateX / translateY
    * scale：缩放，scale / scaleX / scaleY
    * rotate：旋转，rotate
    * skew：变形，skew / skewX / skewY
    * matrix：矩阵，是所有 2D 变换的本质
  3. __3D变换__
    * translate3d：位移，translate3d / translateZ
    * scale3d：缩放，scale3d / scaleZ
    * rotate3d：旋转，rotate3d / rotateX / rotateY / rotateZ
    * matrix3d：矩阵，是所有 3D 变换的本质


  ```scss
  .img {
    transform: rotate(30deg);
    transform-origin: 200px 300px;

    // 等价于 
    transform: translate(200px, 300px) 
              rotate(30deg)
              translate(-200px, -300px); 
    transform-origin: 0 0;
  }

  .box {
    transform-style: preserve-3d;

    /* perspective() 配合 transform 其他函数一起使用，
    仅表示当前变形元素的视距 */
    transform: perspective(200px) rotateY(45deg);

    // perspective 指定子元素共享的视距 
    perspective: 100px;

    // center表示眼睛在场景正中心
    perspective-origin: center;


    img {
      // 父元素不指定 3D 则失效 
      transform: translateZ(16px);

      // 让背面不可见，旋转时常用
      backface-visibility: hidden;
    }
  }
  ```


## transition 过渡

  1. __过渡效果__：默认样式中定义元素的初始样式和过渡函数，然后定义过渡后的元素样式
  2. __缩写语法__：`transition：过渡属性 过渡时间 [过渡曲线] [时间延迟]`

  ```css
  .box {
    width: 100px;
    height: 20px;
    background-color: orange;
    /* transition: width 1s ease-in, backgroundColor 1s ease-in; */
    transition: all 1s ease-in .2s;
  }
  .box:hover {
    width: 300px;
    background-color: red;
  }
  ```


## animation 动画

  1. __动画效果__：通过 animation 绑定 @keyframes 定义的动画，自动实现一种或多种形态同时变化的效果
  2. __动画定义__：`@keyframes 动画名{ 百分比 或 from、to }`
  3. __动画绑定__：`animation: 动画名 时间 动画曲线 延迟时间 播放次数 是否逆向播放 [非动画时间的状态]`
  4. __动画暂停__：`animation-play-state: paused;`

  ```css
  .box { 
    color: #f00; 
    font-size: 36px; 
    font-weight: bold;
  }
  @keyframes change {
    0%   { text-shadow: 0 0 4px #f00 }
    50%  { text-shadow: 0 0 40px #f00 }
    100% { text-shadow: 0 0 4px #f00 }
  }
  .box:hover {
    animation: change 1s ease-in infinite;
  }
  .box.stop {
    animation-play-state: paused;
  }
  ```


# 三、组件样式
> 只在当前组件生效，不影响全局样式。

## Scoped
> 缺点：可能影响其它相同类名的组件样式而不能完全避免冲突、增加了每个样式的权重而导致必须全局修改、增加了标签渲染时间。

  ```html
  <!-- 在元素中添加了一个唯一属性用来区分 -->
  <style scoped>
  h1 {
    color: #f00;
  }

  h1[data-v-4c3b6c1c] {
    color: #f00;
  }
  </style>
  ```

## Modules
> CSS 模块化的一种方式而并非官方标准和浏览器的特性，它重新生成类名而有效避开了 css 权重和类名重复的问题。注意它需要安装 css-loader 插件并进行配置 (Vue 等框架已经集成)，所有类名都要通过 :class 进行绑定。

  ```html
  <template>
    <div :class="css.wrap">
      <input :class="css.input" type="text">
    </div>
  </template>
  <style module="css" lang="scss">
    .wrap {
      color: #999;
      .input { }
    }
  </style>
  ```
  ```js
  // css-loader 默认配置
  {
    modules: true,
    importLoaders: 1,
    localIdentName: '[hash:base64]'
  }

  // 通过 vue-loader 自定义配置
  module: {
    rules: [
      {
        test: '\.vue$',
        loader: 'vue-loader',
        options: {
          cssModules: {
            // 格式化类名：当前文件名、当前类名、hash 字符串
            localIdentName: '[name]__[local]-[hash:base64:5]',
            // only 只支持驼峰绑定类名，true 支持驼峰和中括号
            camelCase: true
          }
        }
      }
    ]
  }
  ```


# 四、PC 端样式
## 初始化
  ```css
  /* reset.css  */
  body, h1, h2, h3, h4, h5, h6, hr, p, blockquote, dl, dt, dd, ul, ol, li, pre, form, fieldset, legend, button, input, textarea, th, td { margin:0; padding:0; } 
  body, button, input, select, textarea { font:12px/1.5tahoma, arial, \5b8b\4f53; } 
  h1, h2, h3, h4, h5, h6{ font-size:100%; } 
  address, cite, dfn, em, var { font-style:normal; } 
  code, kbd, pre, samp { font-family:couriernew, courier, monospace; } 
  small{ font-size:12px; } 
  ul, ol { list-style:none; } 
  a { text-decoration:none; color: #3D3F40; } 
  a:hover { color: #62B060; } 
  sup { vertical-align:text-top; } 
  sub{ vertical-align:text-bottom; } 
  fieldset, img { border:0; } 
  button, input, select, textarea { font-size:100%; } 
  table { border-collapse:collapse; border-spacing:0; }
  body {
    width: 100%;
    height: 100%;
    overflow: hidden;
    -moz-osx-font-smoothing: grayscale;
    -webkit-font-smoothing: antialiased;  /* 设置字体平滑 */
    font-family: "Microsoft YaHei", 'Muli', "Helvetica", Arial, sans-serif;
  }
  ```


## 滚动条 
  ```css
  /*  定义滚动条样式   */   
  ::-webkit-scrollbar {   
  /* ::-webkit-scrollbar:horizontal  */
      width: 10px;   
      height: 10px;  /*  高宽分别对应横竖滚动条的尺寸  */
      background-color: #F5F5F5;   
  }  
  /*定义滚动条轨道 内阴影+圆角*/   
  ::-webkit-scrollbar-track {   
      box-shadow: inset 0 0 6px rgba(0,0,0,0.3);     
      border-radius: 10px;   
      background-color: #F5F5F5;   
  }   
  /*定义滑块 内阴影+圆角*/   
  ::-webkit-scrollbar-thumb {   
      border-radius: 10px;   
      box-shadow: inset 0 0 6px rgba(0,0,0,.3);   
      background-color: #C0C0C0;   
  }  
  ```


## 元素居中 
  ```css
  /* 块中块元素 */
  .div-child {
    margin: 0 auto;  
  }
  .div-parent {
    display: table-cell;   
    vertical-align: middle; 
  }

  /* 块中内联元素 */
  .div-parent {
    text-align: center;   
    height: 100px;
    line-height: 100px;  
    
    /* 其它方法垂直居中 */
    display: table-cell;    
    vertical-align: middle;
  }

  /* 不定宽高的水平、垂直居中 */
  /* 1.定位 */
  .parent {
    position: relative;
  }
  .child { 
    position: absolute;
    left: 50%;
    top: 50%;
    margin-left: -100px;   /* 自身宽度一半 */
    margin-top: -50px;    /* 自身高度一半 */
  }
  /* 2.CSS3 */
  .wrapper {
    position: absolute;
    top: 50%;
    left: 50%;
    z-index: 3;
    transform: translate(-50%,-50%);
  }
  /* 3.flexbox方法 (设置父元素，让子元素水平居中) */
  .parent {       
    display: flex;
    justify-content: center;  
    align-items: center;      
  }  
  ```


## 布局方式 
  ```css
  /* 左边固定右边自适应的两列布局（定位和flex方式略） */
  .parent::after {
    content: "";            
    display: block;
    height: 0;               
    visibility: hidden;     
    clear: both;
  }
  .parent {  
    *zoom : 1      
  }
  .left {
    float: left;
    width: 100px;
  }
  .right {
    margin-left: 120px;  
  }

  /* 两边固定中间自适应的三列布局 (浮动和定位略) */
  .parent {
    display: flex;
  }
  .left,.right {
    width: 100px;
    height: 100px;
  }
  .middle {
    flex: 1;
    margin: 0 200px; 
  }

  /* 移动端窗口居中布局 */
  .text_box span {
    color: #eff1f2;
    display: block;
    position: absolute;
    left: 50vw;
    top: 50vh;
    transform: translate(-50%, -50%);  
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
  ```

## 瀑布流布局
<div style="text-indent: 2em">原理是页面容器内的多个高度不固定的 div 之间按照一定的间隔参差不齐的无序浮动，鼠标滚动时不断在容器内的尾部加载数据并自动加载到空缺位置，不断循环。</div>
<div style="text-indent: 2em">核心是基于一个网格的布局，而且每行包含的项目列表高度是随机的（随自身内容动态改变高度），同时每个项目列表呈堆栈形式排列，最关键的是，堆栈之间彼此之间没有多余的间距差存大。</div>

  ```css
  /* 大层 */
  .container {
    width: 100%;
    margin: 0 auto;
  }

  /* 瀑布流层 */
  .waterfall {
    column-count: 4;
    column-gap: 1em;
  }

  /* 一个内容层 */
  .item { 
    padding: 20px;
    -webkit-column-break-inside: avoid; /* Chrome, Safari, Opera */
    page-break-inside: avoid;  /* Firefox */
    break-inside: avoid;       /* IE 10+ */
    border: 1px solid #000;
    height: 100%;
    overflow: auto;
  }
  .item img {
    width: 100%;
    margin-bottom:10px;
  }
  ```


## 表单元素
  ```css
  /* 清除默认框 */
  input{
    border: none / 0 ;  
  }
  /* 清除下拉框默认选择样式 */
  select{
    appearance: none;
  }
  /* 隐藏下拉箭头 */
  select::-ms-expand { 
    display: none; 
  }

  /* 应用于有焦点的元素(输入框, 超链接)具有焦点的时间内*/
  a:focus, a:hover{ 
    outline: 1px  dotted red; 
    opacity: 0.6;
  }
  input:focus{ 
    background: yellow; 
  }

  /* placeholder 颜色 */
  ::-webkit-input-placeholder{  
    color: #d3d3d3; 
  } 
  :-moz-placeholder{  
    color: #d3d3d3; 
  } 
  ::-moz-placeholder{ 
    color: #d3d3d3;
  } 
  :-ms-input-placeholder{  
    color: #d3d3d3; 
  } 
  ```


## 文本对齐
> 一行居中、多行左对齐

  ```css
  ul li {
    width: 100px;
    text-align: center;
  }
  ul li p {
    display: inline-block;
    text-align: left;
  }
  ```
  

## 文本超出省略
  ```css
  /* 单行文本 */
  div{
    width: 100px;
    overflow: hidden;
    text-overflow: ellipsis;  
    white-space: nowrap;      
  }

  /* 多行文本 */
  div{
    width: 100px;
    overflow: hidden;
    text-overflow: ellipsis;
    /* 弹性伸缩盒子 */
    display: -webkit-box;
    /* 文本最多显示行数 */
    -webkit-line-clamp: 2;
    /* 不设则不显示省略号 */
    -webkit-box-orient: vertical;
  }

  /* 跨浏览器兼容方法 */
  p {
    position: relative;
    line-height: 14px;
    height: 42px;
    overflow: hidden;
  }
  p::after {
    content: "...";
    font-weight: bold;
    position: absolute;
    bottom: 0;
    right: 0;
    padding: 0 20px 1px 45px;
    background: url(ellipsis_bg.png) repeat-y;
  }
  ```



## 常用特殊图形

  ```scss
  // 三角形
  .triangle{
    width: 0;
    height: 0;
    border-top: 50px solid black;
    border-right: 50px solid transparent;
    border-left: 50px solid transparent;
  }

  // 箭头
  .border-triangle-top(@brcolor: #fbfdff, @bgcolor: #fff, @brwidth: 8px, @width: 100%, @height: 40px) {
    width: @width;
    height: @height;
    position: relative;
    &:after,
    &:before {
      content: "";
      position: absolute;
      width: 0;
      height: 0;
      border: @brwidth solid transparent;
      border-bottom-color: @brcolor;
      left: 50%;
      margin-left: -@brwidth;
      top: -@brwidth * 2;
    }
    &:after {
      border-bottom-color: @bgcolor;
      top: -@brwidth * 2 + 1px;
    }
  }
  .border-triangle-bottom {
    font-size: 12px;
    padding: 10px 15px;
    box-sizing: border-box;
    box-shadow: 2px 2px 5px #d9d9d9;
    background: #fff;
    position: relative;
    border-radius: 4px;
    line-height: 18px;
    
    span{
      display: inline-block;
      text-align: left;
      font-size: 12px;
      &.t-left{
        width: 100%;
      }
    }
    &:after,
    &:before {
      content: "";
      position: absolute;
      width: 0;
      height: 0;
      border: 4px solid transparent;
      border-top-color: #d9d9d9;
      left: 50%;
      margin-left: -4px;
      bottom: -8px;
    }
    &:after {
      border-top-color: #fff;
      bottom: -7px;
    }
  }

  // <div class="title" data-title="hello world">title悬浮框</div>
  .title {
    margin-top: 100px;
    margin-left: 100px;
    position: relative;
  }
  .title:hover::after {
    content: attr(data-title);
    display: inline-block;
    padding: 10px 14px;
    border: 1px solid #ddd;
    border-radius: 5px;
    position: absolute;
    top: -50px;
    left: -30px;
  }

  // 半圆
  .semi-circle {
    width: 100px;
    height: 50px;
    background: red;
    border-radius: 50px 50px 0 0;
  }
  // 半椭圆
  .semiellipse {
    margin-top: 30px;
    width: 100px;
    height: 50px;
    background: red;
    border-radius: 100% 0 0 100% /50%;
  }
  // 椭圆
  .ellipse {
    width: 100px;
    height: 50px;
    background: red;
    border-radius: 50px/25px;
  }

  // 梯形
  .echelon {
    width: 600px;
    border: 100px solid;
    border-color: transparent transparent #c00;
  }

  // 三栏
  .three-cols {
    width: 150px;
    height: 30px;
    padding: 15px 0;
    border-top: 30px solid;
    border-bottom: 30px solid;
    background-color: currentColor;
    background-clip: content-box;
  }

  // 多层阴影
  .shadows {
    width: 100px;
    height: 100px;
    background-color: #fff;
    border: 5px solid black;
    box-shadow: 0 0 0 5px green,0 0 0 10px red;
  }

  // 眼睛
  .eye {
    width: 150px;
    height: 150px;
    padding: 10px;
    border: 10px solid;
    border-radius: 50%;
    background-color: currentColor;
    background-clip: content-box;
  }

  // 放大镜
  .magnifier {
    width: 50px;
    height: 50px;
    border-radius: 50%;
    border: 5px solid #333;
    position: relative;
  }
  .magnifier::after {
    content: '';
    display: block;    
    width: 8px;    
    height: 60px;    
    border-radius: 5px;    
    background: #333;    
    position: absolute;    
    right: -22px;    
    top: 38px;    
    transform: rotate(-45deg);
  }
  ```


# 五、移动端样式

## reset.css
  ```css
  html{
    -webkit-text-size-adjust: none;
    /* 禁用长按选中、复制文本功能 */
    -webkit-user-select: none;
    -webkit-touch-callout: none;
    /* 取消可点击元素点击时的背景色 */
    -webkit-tap-highlight-color: transparent;
  }

  body{
    font-size: 14px;
    background: #fbfbfb;
    -webkit-overflow-scrolling: touch;  /* 滚动条上下拉动时卡顿 */
    font-family: -apple-system, BlinkMacSystemFont, "PingFang SC",
    "Helvetica Neue",STHeiti,"Microsoft Yahei",Tahoma,Simsun,sans-serif;
  }

  body,h1,h2,h3,h4,h5,h6,p,dl,dd,ul,ol,pre,form,input,textarea,th,td,select{
    margin: 0; 
    padding: 0; 
    text-indent: 0;
    font-weight: normal;
  }
  a{
    text-decoration: none;
  }
  /* 取消聚焦高亮效果 */
  a:focus, button:focus, input:focus, textarea:focus{
    outline: 0;
  }
  ol,ul,li{
    margin: 0;
    padding: 0;
    list-style: none;
  }
  img, input, button{
    border: none;
  }
  textarea{ 
    resize: none; 
    overflow: auto;
  }
  button:disabled {
    opacity: .65;    
  }

  input::-webkit-input-placeholder,textarea::-webkit-input-placeholder{
    color:#999;
  }
  input:-moz-placeholder,textarea:-moz-placeholder{
    color:#999;
  }
  input::-moz-placeholder,textarea::-moz-placeholder{
    color:#999;
  }
  input:-ms-input-placeholder,textarea:-ms-input-placeholder{
    color:#999;
  }

  button,input,textarea{
    color: inherit;
    font: inherit;
  }
  input:disabled, textarea:disabled {
    -webkit-text-fill-color: #666;
    -webkit-opacity: 1;
    color: #000;
  }

  .shadow{
    box-shadow: 0px 6px 6px #c8c8c8;
  }

  /* 隐藏滚动条 */
  ::-webkit-scrollbar {
    width: 0;
    background: transparent;
  }

  /* 开启硬件加速，解决页面闪白，保证动画流畅 */
  .animation { 
    transform: translate3d(0, 0, 0);
  }
  ```


## 媒体查询
  ```css
  /* rem 配置 */
  html{
    font-size: 10px
  }
  @media screen and (min-width:321px) and (max-width:375px){
    html{ font-size: 11px }
  }
  @media screen and (min-width:376px) and (max-width:414px){
    html{ font-size: 12px }
  }
  @media screen and (min-width:415px) and (max-width:639px){
    html{ font-size: 15px }
  }
  @media screen and (min-width:640px) and (max-width:719px){
    html{ font-size: 20px }
  }
  @media screen and (min-width:720px) and (max-width:749px){
    html{ font-size: 22.5px }
  }
  @media screen and (min-width:750px) and (max-width:799px){
    html{ font-size: 23.5px }
  }
  @media screen and (min-width:800px){
    html{ font-size: 25px } 
  }

  /* 竖屏时的样式 */
  @media all and (orientation:portrait){   }

  /* 横屏时的样式 */
  @media all and (orientation:landscape){   }
  ```



## 表单标签
  ```css
  /* input placeholder 出现文本位置偏上 */
  input{
    line-height: normal;
  }

  /* 输入框 placeholder 的颜色值改变 */
  ::-webkit-input-placeholder {  
    color: #999; 
  }
  :-moz-placeholder {  
    color: #999; 
  }
  ::-moz-placeholder {  
    color: #999; 
  }
  :-ms-input-placeholder {  
    color: #999; 
  }
  input:focus::-webkit-input-placeholder{ 
    color: #999; 
  }

  /* IOS Input Disabled 默认样式问题 */
  input:disabled, textarea:disabled {
    -webkit-text-fill-color: #000;
    -webkit-opacity: 1;
    color: #000;
  }

  /* 改变选中内容的背景颜色：字体颜色换成 color */
  ::selection { 
    background: #FFF; 
    color: #333; 
  } 
  ::-moz-selection { 
    background: #FFF; 
    color: #333; 
  } 
  ::-webkit-selection { 
    background: #FFF; 
    color: #333; 
  } 

  /* 禁用 select 默认下拉箭头 */
  select::-ms-expand {
    display: none;
  }

  /* 禁用 radio 和 checkbox 默认样式 */
  input[type=radio]::-ms-check, 
  input[type=checkbox]::-ms-check
  {
    display: none;
  }

  /* 禁用表单输入框默认清除按钮 */
  input[type=text]::-ms-clear,
  input[type=tel]::-ms-clear,
  input[type=number]::-ms-clear
  {
    display: none;
  }

  /* 重置默认外观 */
  input, select { 
    /* 清除默认内阴影 */
    -webkit-appearance:none; 
    appearance: none; 
  }

  /* 禁止winphone默认触摸事件 (e.preventDefault无效) */
  html{
    -ms-touch-action: none;
  }
  ```

## 1px border
  ```scss
  .hair-line {
    position: relative;
  }
  .hair-line::after {
    content: '';
    position: absolute;
    width: 200%;
    height: 200%;
    left: 0;
    top: 0;
    border-color: #dcdcdc;
    border-style: solid;
    border-width: 0;
    transform: scale(0.5);
    transform-origin: 0 0;
  }
  .hair-line--b::after {
    border-bottom-width: 1px;
  }
  .hair-line--t::after {
    border-top-width: 1px;
  }
  .hair-line--r::after {
    border-right-width: 1px;
  }
  .hair-line--l::after {
    border-left-width: 1px;
  }
  .hair-line--a::after {
    border-width: 1px;
  }

  @mixin all-border-1px($color: #999, $radius: 10px) {
    content: '';
    position: absolute;
    top: 0;
    left: 0;
    border: 2px solid $color;
    border-radius: $radius * 2;
    box-sizing: border-box;
    width: 200%;
    height: 200%;
    transform: scale(0.5);
    transform-origin: left top;
  }
  .border-1px::after{
    @include all-border-1px();
  }
  ```


# 六、动画效果

## 实现方式

### JS
  1. 通过定时器 `setTimeout、setIterval` 间隔改变元素样式，但经常导致页面频繁性重排重绘而消耗性能。
  2. 通过浏览器用于定时循环操作的接口 `requestAnimationFrame` 实现最佳动画效果：它可看作一个性能优化版的 setTimeout，但并不指定回调函数运行时间而是跟着浏览器内建的刷新频率来执行回调。
  3. 复杂动画交互建议使用 RAF、setInterval/setTimeout 降级处理。


### CSS3
> `transition、animation` 摆脱了 js 的控制，并且可以利用硬件加速以及实现复杂动画效果。比如在移动端开发时使用 transition 会让页面变慢甚至卡顿，通过添加 `transform: translate3D(0,0,0) / translateZ(0)` 来开启移动端动画的 GPU 加速可以让动画过程更加流畅。

### HTML5

  * `svg`：使用 XML 格式定义基于矢量的图形并嵌入到 HTML 使用。
  * `canvas`：可用于实现复杂动画，可通过 js 渲染控制动画的执行。
  * `WebGL`：可看作是浏览器提供的接口，门槛较高而一般使用封装后的 Three.js。


## 常用的库

  * `Animate.css`：CSS3 动画库，运动元素添加类名。
  * `Tween.js`：包含各种经典算法的补间动画，以平滑效果变化。
  * `Three.js`：实现 3D 程序的 js 类库，封装了 webgl 接口。
  * `swiper.animate.js`：移动端触摸滑块类库，初始化回调中使用并在运动元素添加类名。


## 加载动画
> 素材网站：https://www.eyuyun.com/127.html

  ```html
  <!-- 图标 -->
  <style>
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
  </style>
  <div class="loading-box">
      <img src="images/mobile/loading.png" alt="">
      <p>loading...</p>
  </div>

  <!-- 圆环 -->
  <style>
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
        opacity: .6;
      }
      60% {
        opacity: .2;
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
      transform: rotateZ(-45deg) translate3d(-0, 0, 0)
    }
    @keyframes zmove {
      to {
        transform: rotateZ(-45deg) translate3d(-800px, 0, 0)
      }
    }
  </style>
  <div class="app_loading">
    <div class="text_box">
      <span>loading...</span>
      <div></div>
      <div></div>
      <div></div>
      <div></div>
    </div>
    <div class="starline">
      <div></div>
      <div></div>
      <div></div>
    </div>
  </div>
  ```


