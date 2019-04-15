---
title: PC 端开发常用标签和样式
tags:
  - HTML
  - CSS
categories: HTML + CSS
top: false
keywords:
  - html
  - css
date: 2019-03-06 22:32:08
description: HTML、CSS

---

# 一、HTML 元素
> __相互转换：display__ 

  * 块级元素：独占一行、可设宽高、不设宽度时默认是其父元素的100%
    * 常用5个标签：h、p、ul、div、table
    * 6大属性：width、height、background、border、padding、margin 
  * 行内元素：一行可放多个、宽高不可设置、宽度包裹内容
    * 常用标签：a、span、label、strong
  * 行内块元素：一行多个、可设宽高  
    * 常用标签：img、input等
  


# 二、浮动
## float
  1. 设计初衷：文字环绕效果
  2. 主要特性：包裹性 和 破坏性

## 清除浮动
  1. 本质：清除浮动元素对后面元素的影响
  2. 方法
    * 给父元素指定高度：不太灵活
    * 在浮动元素后面盒子添加 clear: both
    * 给浮动元素父元素添加 overflow: hidden/auto
    * 底部添加伪元素 after 
  3. 特例：父元素有浮动/绝对定位时不需清除
  4. 经典方法
    ```css
    div::after {
        content: "";       /* 添加内容为空 */   
        display: block;
        height: 0;           /* 不占空间 */
        visibility: hidden;  /* 不可见  */   
        clear: both
    }
    div {  
        *zoom : 1;   /* 兼容低版本浏览器 */   
    }
    ```

# 三、布局模型
> 网页布局：整体布局用浮动，局部布局用定位

  1. 流动模型：浏览器默认的文档流。块级元素自下而上、内联元素自左而右按顺序排列
  2. 浮动模型：浮动流。元素脱离文档流而释放空间，但浮动元素不能遮挡后面文档流中内容的正常显示
  3. 层模型：定位流。父元素相对定位，子元素绝对定位/固定定位，z-index规定定位元素的堆叠顺序


# 四、常用知识点
  * 文件引入优先级：就近原则，距离相应代码近的引入方式优先级高
  * 浏览器读取规则：从右向左，因为相比从左到右匹配更快、性能更优
  * 元素隐藏方法：display、Opacity、visibility、hide、遮罩等
  * 元素间距处理：父子元素的间距给父元素加 padding，同级元素的间距则给一个元素加 margin
  * 伪类和伪元素：`:hover、:link 等伪类`用于向已有元素添加特殊效果，`::before、::after 等伪元素`用于新建抽象元素并添加样式从而实现特殊效果


# 五、css 单位
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


# 六、浏览器兼容

## 常见兼容问题
  * __img 之间出现间距__：浮动
  * __上下 margin 重合问题__：上下选一个
  * __opacity、placeholder、outline、伪元素、z-index__  
  * __子元素的 margin-top,会出现在父元素的上方__
    * 给父元素加 overflow: hidden
    * 能用 padding-top 替代时就替代
    * 给父元素设置 border: 1px solid transparent
  * __li 元素使用 display: inline-block 时会出现缝隙__
    * 使用浮动
    * 给li重新设置字号 font-size: 0 


## css hack
> 针对不同的浏览器写不同的 CSS 来解决浏览器兼容

  * 选择器前缀法：@media screen
  * CSS 属性前缀法：-webkit-、-moz-、-ms-、-o-
  * IE 条件注释语句：解决 IE 兼容问题


# 七、CSS3

## 引用字体文件
  ```css
  @font-face {                       
      font-family: ArtifaktBlod;     
      src: url('fonts/ArtifaktElementOfc-Bold.ttf');  /* 引入字体资源 */    
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


## transition

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


## animation

  1. __动画效果__：通过 animation 绑定 @keyframes 定义的动画，自动实现一种或多种形态同时变化的效果
  2. __动画定义__：`@keyframes 动画名{ //百分比 或 from、to }`
  3. __动画绑定__：`animation: 动画名 时间 动画曲线 延迟时间 播放次数 是否逆向播放 [非动画时间的状态]`
  4. __动画暂停__：`animation-play-state: paused;`

  ```css
  .box { 
      color: #f00; 
      font-size: 36px; 
      font-weight: bold;
      animation: change 1s ease-in infinite; 
      /* animation-play-state: paused; */
  }
  @keyframes change {
      0%   { text-shadow: 0 0 4px #f00 }
      50%  { text-shadow: 0 0 40px #f00 }
      100% { text-shadow: 0 0 4px #f00 }
  }
  ```
  


# 八、PC 端开发常用样式
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
      display:-webkit-flex;
      justify-content:center;  
      align-items:center;      
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


## 文本溢出省略
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
      display: -webkit-box; 
      overflow: hidden; 
      text-overflow: ellipsis;
      -webkit-line-clamp: 2;
      -webkit-box-orient: vertical;
  }
  ```

## 特殊图形
```css
/* 半圆 */
.semi-circle {
    width: 100px;
    height: 50px;
    background: red;
    border-radius: 50px 50px 0 0;
}
/* 半椭圆 */
.semiellipse {
    margin-top: 30px;
    width: 100px;
    height: 50px;
    background: red;
    border-radius: 100% 0 0 100% /50%;
}
/* 椭圆 */
.ellipse {
    width: 100px;
    height: 50px;
    background: red;
    border-radius: 50px/25px;
}

/* 梯形 */
.echelon {
    width: 600px;
    border: 100px solid;
    border-color: transparent transparent #c00;
}

/* 三栏 */
.three-cols {
    width: 150px;
    height: 30px;
    padding: 15px 0;
    border-top: 30px solid;
    border-bottom: 30px solid;
    background-color: currentColor;
    background-clip: content-box;
}

/* 多层阴影 */
.shadows {
    width: 100px;
    height: 100px;
    background-color: #fff;
    border: 5px solid black;
    box-shadow: 0 0 0 5px green,0 0 0 10px red;
}

/* <div class="demo" data-title="hello world">title悬浮框</div> */
.title {
    margin-top: 100px;
    margin-left: 100px;
    position: relative;
}
.title:hover::after {
    content: attr(data-title);/* 取到data-title属性的值 */
    display: inline-block;
    padding: 10px 14px;
    border: 1px solid #ddd;
    border-radius: 5px;
    position: absolute;
    top: -50px;
    left: -30px;
}

/* 眼睛 */
.eye {
    width: 150px;
    height: 150px;
    padding: 10px;
    border: 10px solid;
    border-radius: 50%;
    background-color: currentColor;
    background-clip: content-box;
}

/* 放大镜 */
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


