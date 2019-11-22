---
title: React 框架的基础使用
tags:
  - React
categories: React
top: false
keywords:
  - React
date: 2019-08-17 22:05:46
description: 基础介绍、项目开发、JSX 表达式、组件化开发
---

# 一、基础介绍
>  Facebook 开源的一个用于动态构建用户界面的 UI 库，它不是一个 MVC 框架而只针对 View，不能解决实际项目的所有问题，需要结合 Redux、React-router 等其它库。


## 对比 Vue

### 相同点
  * 数据驱动视图
  * 都支持服务器端渲染
  * 都有支持 native 的方案：React 的 React native、Vue 的 weex。
  * 都有 Virtual DOM、webComponent 规范(组件化)，通过 props 实现父子通信。


### 不同点
  * __框架模式__：Vue 是 `MVVM` 模式，React 则只针对 `MVC view 层`。
  * __数据绑定__：vue 实现了数据的双向绑定，react 的数据流动则是单向的。
  * __state 对象__：vue 不是必须的，react 应用状态则是不可变的并需要使用 setState 更新。
  * __virtual DOM__：vue 会跟踪每个组件的依赖关系而`不需要重新渲染整个组件树`，React 则是每当应用状态被改变时`重新渲染全部组件`，需要 shouldComponentUpdate 控制。
  * __组件写法__：Vue 推荐使用 `webpack + vue-loader` 的单文件组件，即把 html、css、js 写在同一文件。React 则推荐 `JSX + inline style`，即把 html、css 全部写进 Js。


## 主要特点
  
  1. __单向数据__：单向的从数据到视图的渲染，减少了重复代码。
  2. __高效渲染__
    * __virtual DOM__：浏览器端创建的一个描述 dom 结构和样式的 js 对象，组件状态改变时操作内存数据而避免了直接遍历元素的所有属性，极大提高了渲染的效率和性能。
    * __DOM Diff__：对比改变前后两个对象差异的算法，用于计算出更新真实 DOM 的最小步骤，最终只把变化的部分重新渲染。
  3. __组件化开发__
    * DOM 树上的节点被称为元素，Virtual DOM 上的节点则称为组件。
    * 组件就是封装起来的具有独立功能的 UI 模块，具有高内聚、低耦合的特点，React 组件开发推荐使用 JSX 而不能用模板。
  4. __声明式编程__：简洁易懂而有利于代码维护、无须使用变量而避免了创建和修改状态。
  5. __支持客户端与服务器渲染__：服务端渲染 (Node)、APP (ReactNative)，但这只是拓展功能。

  ```js
  // 2、虚拟 DOM：标签不加引号
  const p = React.createElement('p', {
    id: 'box', 
    className: 'a'
  }, '茉莉花', tDom);
  ReactDOM.render(p, document.getElementById('app')); 

  const list = ['1', '2', '3', '4'];
  ReactDOM.render(
    `<ul>{list.map((item,index) => <li key={index}>{item}</li>)}</ul>`, 
    document.getElementById('app')
  )

  // 4、命令式编程和声明式编程
  const map = new Map.map(document.getElementById('map'), {
    zoom: 4,
    center: {lat,lng}
  })
  const marker = new Map.marker({
    position: {lat, lng},
    title: 'Hello Marker'
  })
  marker.setMap(map)

  <Map zoom={4} center={lat, lng}>
    <Marker position={lat, lng} title={'Hello Marker'}/>
  </Map>
  ```


## 技术栈

  * __Babel__：编译工具
  * __Redux__：状态管理工具
  * __React-router__：路由工具
  * __create-react-app__：脚手架工具


# 二、项目开发

## 初始化目录
> 相关文件：index.html (入口模板)、manifest.json (应用配置)、index.js (应用入口)、serviceWorker.js (生产环境缓存资源)

  ```js
  // localhost:3000
  npm install -g create-react-app
  create-react-app my-app
  cd my-app
  npm start
  npm run build

  // 自定义目录
  cd src
  rm App.* index.css logo.svg
  mkdir coms pages style tool
  ```


## 项目配置
> 脚手架创建的目录默认隐藏配置文件，修改配置项有两种方法：1、暴露配置文件后直接修改运行命令 npm run eject 生成的 config 目录(不可逆)，2、安装 react-app-rewired 并进行配置如下：

  ```js
  // 安装
  npm install react-app-rewired --save

  // 修改 package.json
  "scripts": {
    "start": "react-app-rewired start",
    "build": "react-app-rewired build",
    "test": "react-app-rewired test",
    "eject": "react-app-rewired eject"
  }

  // 根目录新建 config-overrides.js：项目启动时候会优先读取并整合到配置项
  const { injectBabelPlugin } = require('react-app-rewired');
  module.exports = function override(config, env) {
      config = injectBabelPlugin([
          'import', { libraryName: 'antd', libraryDirectory: 'es', style: 'css' }
      ], config)
      config = injectBabelPlugin([
          "@babel/plugin-proposal-decorators", { "legacy": true }
      ], config)
      return config;
  }
  ```


https://segmentfault.com/a/1190000016342792#articleHeader10

  

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
> Facebook 专门为 react 发明的一种新的类似于 XML 格式的语言，它是 JS 的语法拓展。它使用 XML 标记的方式去直接声明界面，然后利用编译器转换成 JS 语言。

## 优点
  * 编写模板更加快速。
  * 渲染时输出虚拟 dom，执行更快。
  * 类型安全，在编译过程中就能发现错误。


## 实现原理
> JSX 不会直接渲染为 DOM：1. 元素可能需要渲染到 页面（react-dom）、canvas（react-canvas）、原生 App（ReactNative），2. 当数据变化而需要更新组件时，可以通过快速算法操作 Js 对象而不用操作 DOM，这样可以尽量减少浏览器重排而极大地优化性能。

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

https://www.cnblogs.com/ly2019/p/11210407.html



# 四、组件化开发

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
        return (<p onClick={this.inct}>{this.props.children(this.state)}</p>)
      }
    }

    class App extends React.Component {
      render() {
        return (
          <Counter>
            {state => (
              <h1>The count is: {state.count}</h1>
            )}
          </Counter>
        )
      }
    }
    ```


## 组件 API
> npm install react 时得到组件及其 API。组件接收 props 输入并返回描述(声明)用户界面 (UI) 的 React 元素。这就是 React 被称为声明性 API 的原因，因为 React 通过你输入的内容告诉它所希望的 UI 外观，而 React 负责其余的工作。

### render
> 它接受三个参数 (渲染元素、插入节点、回调函数)，用于将模板转为 HTML 语言并插入指定的 DOM 节点，是使用 class 创建组件时必须实现的方法 (否则会直接抛出错误)。


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











