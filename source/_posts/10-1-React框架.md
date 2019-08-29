---
title: React 框架的基础使用
tags:
  - React
categories: React
top: false
keywords:
  - React
date: 2019-08-17 22:05:46
description: 入门简介、JSX 表达式、组件化开发
---

# 一、入门简介
>  Facebook 开源的一个可用于动态构建用户界面的 JavaScript 库(框架)

  * 单向的从数据到视图的渲染，非双向数据绑定。
  * 一个用于构建页面组件的 UI 库而不是一个 MVC 框架，可认为是 MVC 中的 View。
  * 对虚拟 DOM 进行编程而不是直接操作 DOM 对象，并通过 diff 算法以最小步骤作用到真正的浏览器 DOM。
  * 可以使用 JSX 但不能用模板，JSX 最终会被编译为合法的 JS 语句调用。
  * 服务器端渲染只是一个锦上添花的功能而并非核心出发点，官方站点几乎没有提及其在服务器端的应用。
  * 在实际项目中，它并不能解决所有问题，需要结合 Redux、React-router 等其它库来协助提供完整的解决方法。



## 对比 Vue

### 相同点
  * 数据驱动视图
  * 都支持服务器端渲染
  * 都有支持 native 的方案：React 的 React native、Vue 的 weex
  * 都有 Virtual DOM、组件化开发，通过 props 参数进行父子组件数据的传递，都实现 webComponent 规范


### 不同点
  * __框架模式__
    * Vue 则是 MVVM 模式
    * React 严格上只针对 MVC 的 view 层
  * __virtual DOM__
    * vue 会跟踪每一个组件的依赖关系而不需要重新渲染整个组件树
    * React 则是每当应用的状态被改变时全部组件都会重新渲染，需要 shouldComponentUpdate 进行控制
  * __组件写法__
    * React 推荐的做法是 JSX + inline style，即把 HTML、CSS 全都写进 Js
    * Vue 推荐的做法是 webpack + vue-loader 的单文件组件格式，即 html、css、js 写在同一个文件
  * __数据绑定__
    * vue 实现了数据的双向绑定
    * react 数据流动是单向的
  * __state 对象__
    * react 应用中是不可变的，需要使用 setState 更新状态
    * vue 中并不是必须的，数据由 data 属性在 vue 对象中管理



## 主要特点

  * __声明式编程__
    * 编程范式：命令式编程、声明式编程、函数式编程、面向对象编程
    * 优点
      * 更加简洁、易懂，利于大型项目代码的维护
      * 无须使用变量，避免了创建和修改状态
    ```js
    // 命令式编程描述代码如何工作，告诉计算机一步步地执行、先做什么后做什么
    const toLowerCase = arr => {
        const res = [];
        for (let i = 0, len = arr.length; i < len; i++) {
            res.push(arr[i].toLowerCase());
        }
        return res;
    }

    // 声明式编程表明想要实现什么目的，应该做什么，但是不指定具体怎么做
    const toLowerCase = arr => arr.map(
        value => value.toLowerCase();
    )

    // JavaScript 命令式编程实现带有标记的地图
    const map = new Map.map(document.getElementById('map'), {
        zoom: 4,
        center: {lat,lng}
    })
    const marker = new Map.marker({
        position: {lat, lng},
        title: 'Hello Marker'
    })
    marker.setMap(map)

    // React 组件中显示地图
    <Map zoom={4} center={lat, lng}>
        <Marker position={lat, lng} title={'Hello Marker'}/>
    </Map>
    ```
  * __单向数据流__：减少了重复代码，比较简单
  * __高效__
    * virtual DOM：最大限度的减少直接操作 DOM
    * DOM Diff 算法：最小化页面重绘，减少重排重绘的次数
  * __组件化开发__：高内聚、低耦合，注意使用 Js 编写而非模板
  * __支持客户端与服务器渲染__：可以实现 服务端渲染 (Node)、APP (ReactNative)



## 实现原理
  
### 虚拟 DOM 

  <div style="text-indent: 2em;">复杂或频繁的 DOM 操作通常是性能瓶颈产生的原因，所以 React 为了尽可能减少对DOM的操作，提供了 Virtual DOM 机制代替直接的 DOM 操作：`在浏览器端通过 Js 实现了一套 DOM API 来创建一个描述 dom 结构和样式的对象 (即虚拟 DOM)，并用来管理真实 DOM 的状态更新`。为什么通过这多一层的 Virtual DOM 操作就能更快呢？这是因为 `React 有个 diff 算法`。</div>
  <div style="text-indent: 2em;">每当数据变化时，React 都会重新构建整个 DOM 树，然后 React 通过 diff 算法将当前整个 DOM 树和上一次的 DOM 树进行对比，计算出最小的步骤更新真实的浏览器 DOM。而且 React 能够批处理虚拟 DOM 的刷新，在一个事件循环内的两次数据变化会被合并。尽管每一次都需要构造完整的虚拟 DOM 树，但是因为虚拟 DOM 是内存数据，性能极高，而对实际 DOM 进行操作的仅仅是 Diff 部分，因而能达到提高性能的目的。</div>

  ```js
  // 方法一
  // 创建虚拟 DOM 对象并渲染到页面
  const vDom1 = React.createElement('p',{id: 'myId1', className: 'myClass1'}, '珂朵莉',vDom3);
  ReactDOM.render(vDom1, document.getElementById('app')); 

  //方法二
  const id = 'myId2';
  const content = '我永远喜欢珂朵莉';
  const vDom3 = React.createElement('span', {}, 'daisiki');
  const vDom2 = <div><h2 id={id } className="myClass2">{content}</h2>{vDom3}</div>
  // 将虚拟DOM对象插入到页面的指定容器中
  ReactDOM.render(vDom2, document.getElementById('test2'));
  const list = ['1', '2', '3', '4'];
  ReactDOM.render(
    <ul>
        { list.map(( item,index) => <li key={index}>{item}</li> ) }
    </ul>
    ,document.getElementById('app')
  )
  ```


### 组件化

  <div style="text-indent: 2em;">虚拟 DOM 不仅带来了简单的UI开发逻辑，同时也带来了组件化开发的思想，组件就是封装起来的具有独立功能的 UI 部件。React 推荐以组件的方式去重新思考 UI 构成，将 UI 上每一个功能相对独立的模块定义成组件，然后将小的组件通过组合或者嵌套的方式构成大的组件，最终完成整体 UI 的构建。`DOM 树上的节点被称为元素，Virtual DOM 上则称为 commponent，Virtual DOM 的节点就是一个完整抽象的组件`，components 的存在让计算 DOM diff 更高。</div>


  * 页面功能
  ```xml
  <div class='wrapper'>
    <button class='like-btn'>
      <span class='like-text'>点赞</span>
      <span>👍</span>
    </button>
  </div>

  <script>
    const button = document.querySelector('.like-btn')
    const buttonText = button.querySelector('.like-text')
    let isLiked = false
    button.addEventListener('click', () => {
      isLiked = !isLiked
      buttonText.innerHTML = isLiked ? '取消' : '点赞'
    }, false)
  </script>
  ```
  * 封装组件
  ```js
  // 公共父类
  class Component {

    constructor (props = {}) {
      // 自定义配置数据
      this.props = props
    }

    setState (state) {
      const oldEl = this.el
      this.state = state
      this._renderDOM()
      if (this.onStateChange) this.onStateChange(oldEl, this.el)
    }

    _renderDOM () {
      this.el = createDOMFromString(this.render())
      if (this.onClick) {
        this.el.addEventListener('click', this.onClick.bind(this), false)
      }
      return this.el
    }
  }

  // mount 方法：将组件的 DOM 元素插入页面，并在 setState 时更新页面
  const mount = (component, wrapper) => {
    wrapper.appendChild(component._renderDOM())
    component.onStateChange = (oldEl, newEl) => {
      wrapper.insertBefore(newEl, oldEl)
      wrapper.removeChild(oldEl)
    }
  }


  class LikeButton extends Component {
    constructor (props) {
      super(props)
      this.state = { isLiked: false }
    }

    onClick () {
      this.setState({
        isLiked: !this.state.isLiked
      })
    }

    render () {
      return `
        <button class='like-btn' style="background-color: ${this.props.bgColor}">
          <span class='like-text'>
            ${this.state.isLiked ? '取消' : '点赞'}
          </span>
          <span>👍</span>
        </button>
      `
    }
  }

  mount(new LikeButton({ bgColor: 'red' }), wrapper)
  ```



## 基础使用

  * React 的核心库：`react.development.js`
  * 提供操作 DOM 的 react 扩展库：`react-dom.development.js`
  * 解析 JSX 语法代码转为纯 JS 语法代码的库：`babel.min.js`


  ```xml
  <div id="root"></div>
  <script src="js/react.development.js"></script>
  <script src="js/react-dom.development.js"></script>
  <script src="js/babel.min.js"></script>

  <!-- script标签的类型必须为 text/babel：声明需要 babel 处理 -->
  <script type="text/babel">
    ReactDOM.render(<h1>hello world</h1>, document.getElementById('root'))
  </script>
  ```


# 二、项目开发

## 技术栈
  * __Babel__：编译工具
  * __Redux__：状态管理工具
  * __React-router__：路由工具
  * __create-react-app__：脚手架工具


## 初始化目录
> 相关文件：index.html (入口模板)、manifest.json (应用配置)、index.js (应用入口)、serviceWorker.js (生产环境缓存资源)

  ```js
  npm install -g create-react-app
  create-react-app my-app
  cd my-app
  npm start

  // 自定义目录
  cd src
  rm App.* index.css logo.svg
  mkdir coms pages style tool

  // 安装工具
  cnpm i react-router-dom -S

  // 修改 index.js
  import './style/style.scss';
  import App from './router/App';


  // 新建 router.js：存放路由
  import React, { Component } from 'react'
  import { BrowserRouter as Router, Switch, Route } from 'react-router-dom'
  import SiteIndex from './pages/index'

  export default class App extends Component {
    render () {
      return (
        <Router basename="/">
          <Switch>
            <Route exact path='/' component={SiteIndex} />
          </Switch>
        </Router>
      )
    }
  }

  // 新建页面：page/site/index.jsx
  import React, { Component } from 'react'

  export default class Index extends Component {
    constructor (props) {
      super(props)
      this.state = {}
    }

    componentDidMount () { }

    render () {
      return (
        <div className="home">indexPage</div>
      )
    }
  }

  // 新建 style/index.css：存放全局样式
  ```

 



## 简单练习

  ```js
  // test/src/index.js
  import React, { Component } from 'react'  // 组件父类 Component
  import ReactDOM from 'react-dom'      // 将 React 组件渲染到页面

  class Head extends Component {
    render () {
      return (
        <h1>React 小书</h1>
      )
    }
  }

  ReactDOM.render(
    <Head />,
    document.getElementById('root')
  )
  ```




# 二、JSX 语法
> 全称 `JavaScript XML`，是 react 定义的一种类似于 XML 的 JS 扩展语法：`XML + JS`。用于创建 react 虚拟 DOM 对象，即 JSX 最终会被解析编译为合法的 JS 对象，基本规则为：< 开头的代码以标签的语法解析、{ 开头的代码以 JS 语法解析。


## 实现原理

### 具体实现

  <div align="center"> 
    ![JSX 实现原理](/images/react/jsx.png)
  </div>


  ```js
  // 编译前
  class Header extends Component {
    render () {
      return (
        <div>
          <h1 className='title'>React 小书</h1>
        </div>
      )
    }
  }
  ReactDOM.render(
    <Header />,
    document.getElementById('root')
  )


  // 编译后
  class Header extends Component {
    render () {
      return (
        // 构建一个 Js 对象描述 HTML 的结构和信息
        React.createElement(
            "div",
            null,
            React.createElement(
              "h1",
              { className: 'title' },
              "React 小书"
            )
          )
        )
    }
  }
  ReactDOM.render(
    // 渲染组件并且构造 DOM 树，然后插入到页面的特定元素
    React.createElement(Header, null), 
    document.getElementById('root')
  )
  ```


### 原理解析
> 为什么不直接从 JSX 直接渲染构造 DOM 结构呢

  * 元素可能需要渲染到 页面（react-dom）、canvas（react-canvas）、原生 App（ReactNative）
  * 当数据变化时需要更新组件时，可以通过快速算法操作 Js 对象而不用直接操作页面 DOM，这样可以尽量少的减少浏览器重排，极大地优化性能。



## 


















