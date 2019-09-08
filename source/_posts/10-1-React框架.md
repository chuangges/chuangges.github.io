---
title: React 框架的基础使用
tags:
  - React
categories: React
top: false
keywords:
  - React
date: 2019-08-17 22:05:46
description: 入门简介、项目开发、JSX 表达式、组件化编程
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

    // 命令式编程实现带有标记的地图
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
  ```
  

## 路由功能

### react-router
> `react-router-v4` 称为 “第四代 react-router“，主要有三个包：`react-router(core)、react-router-dom(for web)、react-router-native(for #native)`。react-router 实现了路由的核心功能，react-router-dom/native 都是基于 react-router 并添加了对应运行环境的特定功能，它们通过 npm 安装时都会将 react-router 作为依赖安装。



### 代码实现
> https://www.jianshu.com/p/dcdb3884d73c

  ```js
  // 安装工具
  cnpm i react-router-dom -S

  // 修改 src/index.js
  import './style/style.scss';
  import App from './router/App';


  // 新建 src/router.js：存放路由
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

  // 新建页面：pages/index.jsx
  import React, { Component } from 'react'

  export default class Index extends Component {
    constructor (props) {
      super(props)
      this.state = {}
    }

    render () {
      return (
        <div className="index">indexPage</div>
      )
    }
  }

  // 新建 style/index.css：存放全局样式
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



# 组件化开发

## 创建组件
> 组件名称的首字母都必须大写，因为通过 babel 转换 JSX 语法时调用了 `React.createElement()`，它需要接收三个参数 `type、config、children`。第一个参数声明了元素类型，当组件名的首字母小写时 babel 会在转义时将它当成了一个字符串传入，当首字母大写时则会传入一个变量。如果传入一个字符串，在创建虚拟 DOM 对象时 React 会认为这是一个原生的 HTML 标签而导致报错，传入变量则会被看作组件。


  * __创建方式__
    * __函数式定义方式__ (无状态组件)
      * 组件不会被实例化，整体渲染性能较好，应尽量使用
      * 组件不能操作 state，不能访问生命周期函数和 this
    * __ES5 原生方式__ (正在逐渐废弃)
      * 每一个成员函数的 this 都会自动由 React 绑定，导致不必要的性能开销
      * 创建组件时可以添加 mixins 属性，并将可供混合的类的集合以数组形式赋给 mixins
    * __ES6 类的方式__（有状态组件)
      * 成员函数不会自动绑定 this，需要开发者手动绑定，否则 this 不能获取当前组件实例对象
      * state 是在 constructor 中实现初始化，props 属性类型和组件默认属性作为组件类的属性而不是组件实例的属性，所以使用类的静态属性配置。
  * __构造函数__
    * __使用场景__：constructor 构造函数是子类继承父类时的构造方法，默认返回实例对象 this。主要用于初始化数据、绑定属性和方法，注意必须调用 super() 继承父类的属性和方法。
    * __为什么必须调用 super__：子类必须在 constructor 方法中调用 super 方法，否则新建实例时会报错。这是因为子类自己的 this 对象必须先通过父类的构造函数完成塑造，得到父类同样的实例属性和方法，然后再对其进行加工，加上子类自己的属性和方法。如果不调用 super 方法，子类就得不到 this 对象。
    * __为什么可以省略__：由于 ES6 的继承规则，不管子类写不写 constructor，new 实例的过程都会自动给补上 constructor。


  ```js
  // 函数组件：写法简洁，但是功能单一
  function MyCom(props){
  　　return (<h1>mycomponent</h1>)
  }

  // ES5 原生
  const MyCom = React.createClass({
    getInitialState() {
      return { message: 'Hi' };
    },
    logMessage() {
      console.log(this.state.message);
    },
    render() {
      return (
        <input type="button" value="Log" onClick={this.logMessage} />
      )
    }
  })

  // 类组件：官方推荐，可以维护状态变量和实现复杂功能
  class MyCom extends React.Component{
      // 继承：extends + super
      constructor(props){
          super(props);    
          this.state = { name: "" }
      }
      componentDidMount() {
        this.setState(() => {
          return { name: "William" }
        })
      }
      render(){
          return (<h1>this.state.name</h1>)
      }
  }
  ```


## 组件类型
> 根据不同划分依据可以将组件分为不同类型

  * __组件分类__
    * 组件的职责：`展示组件、容器组件`
    * 组件的定义方式：`函数组件、类组件`
    * 组件内部是否维护 state：`无状态组件、有状态组件`
  * __区别与联系__
    * 函数组件、无状态组件和展示型组件主要关注 UI 展现，类组件、有状态组件和容器型组件主要关注数据逻辑。
    * 函数组件一定是无状态组件，展示型组件一般是无状态组件。类组件可以是有状态组件、无状态组件，容器型组件一般是有状态组件。



## 组件模式
> 即使用 React 的最佳实践，最初是为了将数据逻辑和 UI 表现层进行分离而引入。通过在组件之间划分职责，可以创建更多可重用的、内聚的组件，这些组件可用于组合复杂的 UI，这在构建可扩展的应用程序时尤其重要。


  * __展示组件__：使用纯函数来简化表述的无状态组件
  * __容器组件__：获取数据并渲染子组件的有状态组件
  * __高阶组件__
    * 本质：一个接收一个组件作为参数并进行修改，然后返回一个新组件的函数
    * 应用：使用 react-router-v4 之后就可以使用 `withRouter()` 来继承以 props 形式传递给组件的各种方法。使用了 redux 之后就可以使用 `connect({})()` 方法来将展示组件和 store 中的数据进行连接。
    ```js
    import {withRouter} from 'react-router-dom';

    class App extends React.Component {
      constructor() {
        super();
        this.state = {path: ''}
      }
      
      componentDidMount() {
        let pathName = this.props.location.pathname;
        this.setState(() => {
          return {
            path: pathName
          }
        })
      }
      
      render() {
        return (<h1>rendering at: {this.state.path}</h1>)
      }
    }

    // 使用了 withRouter 则可以直接访问 this.props.locationlocation
    // 而不需要将 location 作为 props 直接传入，非常方便。
    export default withRouter(App)
    ```
  * 渲染回调：主要用于共享或重用组件逻辑。虽然许多开发人员倾向于使用高阶组件的可重用逻辑，但是渲染回调减少了命名空间冲突并更好地说明了逻辑来源。
    ```js
    // 本质是暴露了 children 这个外部属性
    class Counter extends React.Component {
      constructor(props) {
        super(props);
        this.state = {
          count: 0
        }
      }

      inct = () => {
        this.setState(prevState => {
          return {
            count: prevState.count + 1
          }
        })
      }

      render() {
        return (<div onClick={this.inct}>{this.props.children(this.state)}</div>)
      }
    }

    class App extends React.Component {
      render() {
        return (
          <Counter>
            {state => (
              <div>
                <h1>The count is: {state.count}</h1>
              </div>
            )}
          </Counter>
        )
      }
    }
    ```


## 组件 API
> npm install react 时得到组件及其 API。组件接收 props 输入并返回描述(声明)用户界面 (UI) 的 React 元素。这就是 React 被称为声明性 API 的原因，因为 React 通过你输入的内容告诉它所希望的 UI 外观，而 React 负责其余的工作。

  

### render



### state
### props
### context
### lifecycle events



## 绑定 this
> bind 返回一个新的函数对象

  1. __constructor + bind__：只需要在构造函数中预先绑定一次
  2. __render + bind__：每次渲染时都需要重新绑定，存在性能问题
  3. __箭头函数__
    * ES6 写法：只能在 render 添加，存在性能问题且不能移除监听事件
    * ES7 写法：可以在 Class 中直接赋值，是 ES6 写法的问题优化方案
      * 安装：`npm install --save-dev babel-preset-stage-2`
      * 配置：`.babelrc："presets":["react","env","stage-2"]`


  ```js
  class App extends React.Component {
      constructor(props) {
          super(props)
          this.state = { msg: "hello" }
          // 1
          this.logMsg = this.logMsg.bind(this)
      }
      logMsg() {
          console.log(this.state.msg)
      }
      _logMsg = () => {
        console.log(this.state.message)
      }
      render() {
          return (
            // 1
            <div onClick={this.logMsg}>点击</div>

            // 2
            <input type="button" value="Log" onClick={this.logMsg.bind(this)} />

            // 3、ES6 写法
            <input type="button" value="Log" onClick={() => this.logMsg()} />
            // 3、ES7 写法
            <input type="button" value="Log" onClick={this._logMsg} />
          )
      }
  }
  ```











