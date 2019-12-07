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
>  Facebook 开源的一个用于动态构建用户 UI 界面的 JS 库，它不是一个 MVC 框架而只针对 View，不能解决实际项目的所有问题，需要结合 Redux、React-router 等其它库。

## 主要特点
  
  1. __单向数据__：数据只能从父组件流向子组件，减少了重复代码。
  2. __高效渲染__
    * __virtual DOM__：描述 DOM 元素结构结构和样式的 js 对象。组件状态改变时操作内存数据而不需要遍历元素的所有属性，极大提高了渲染性能。
    * __DOM Diff__：对比改变前后两个对象差异的算法，用于计算出更新真实 DOM 的最小步骤并最终只把变化的部分重新渲染，极大减少了与 DOM 的交互。
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


## 技术栈

  * __Babel__：编译工具
  * __Redux__：状态管理工具
  * __React-router__：路由工具
  * __create-react-app__：脚手架工具


# 二、项目开发
https://www.ituring.com.cn/article/507688

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
> 两种方法：1、脚手架创建的目录默认隐藏配置目录 config，暴露则需要执行 npm run eject (不可逆)，2、安装 react-app-rewired 并配置如下：

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

  // 根目录新建 config-overrides.js：项目启动时会优先读取并整合到配置项
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
      return <div className="index">indexPage</div>
    }
  }

  // 新建 style/index.css：存放全局样式
  ```


# 二、JSX 表达式
> 基于 ECMAScript 的一种语法拓展而并非一种新语言，用于创建虚拟 DOM。


## 主要优点
  * 编写组件时比较简单快速。
  * 渲染时输出虚拟 dom，执行更快。
  * 类型安全，在编译过程中就能发现错误。
  * 防注入攻击：React DOM 在渲染之前默认会过滤所有传入的值并将所有内容转换为字符串，这样可以有效地防止 XSS（跨站脚本）攻击。


## 使用规则

  * 表格标签必须添加 tbody。
  * 最外层有且只有一个标签，但可以是空标签。
  * 对大小写敏感，区分是组件还是 html 标签。
  * 所有的标签必须闭合，单标签必须有末尾反斜杠。
  * 标签内放`<`会报错，因为他会按照 html 来解析。
  * 在标签内部的注释需要写入 `{}`，在标签外的的注释则不需要。
  * `{}` 不可插入： 函数声明、对象、for 循环、if 语句、while 语句。
  * `{}` 可插入：变量、简单运算、JS 内置函数、函数执行、三元运算符、自定义组件。
  * 属性名：小驼峰命名，不能使用关键字：`class - classNmae、for - htmlFor`。
  * 属性值：字符串加引号，变量加 `{}`。如果包含换行符 `\n`，则需要设置 css 样式才能生效 `whiteSpace: 'pre-wrap'`。


  ```js
  // 空标签：<React.Fragment/> 的语法糖
  class Columns extends React.Component {
    render() {
      return (
        <>
          <td>Hello</td>
          <td>World</td>
        </>
      )
    }
  }

  // 属性值
  <div tabIndex="0"></div>
  <img src = {`images/${star}.png`} />
  <span className={`tab ${index===this.state.cIndex?"active":null}`}>标签</span>
  const props = {
    name: "Jack",
    age: 20,
    children: []
  }
  <span {...props}></span>

  // 行内样式：双大括号，标准 JSON，省略 px，名字性驼峰
  <p style={{"width" : 200, "height" : 200, "backgroundColor" : "red"}}></p>
  <p style={{display: (index===this.state.cIndex) ? "block" : "none"}}>标签</p>

  // 条件渲染
  const isBtn = this.state.isBtn
  <div>{ isBtn && <Button onClick={this.handleClick} />}</div>
  <div>{ isBtn ? <Button /> : <span>内容</span> }</div>
  if (isBtn) { return <div><Button /></div> }

  function Render ({ if: cond, children }) {
    return cond ? children : null
  }
  <Render if={status === 'loading'} >加载</Render>

  // 表达式
  function formatName(user) {
    return user.firstName + ' ' + user.lastName;
  }
  render () {
    return (
      <div className="wrap">
        <h1>{parseInt(Math.random() * 100)}年</h1>
        <h2>{formatName(this.state.user)}</h2>
      </div>
    )
  }

  // 数组
  <ul>
    {arr.map((item,index)=> <li key={index}> {item} </li>)}
  </ul>

  // 事件
  import cn from 'classnames';
  export default function CustomIcon(props) {
    const { type, className, active=false, onClick } = props;
    const cls = cn('cc-custom-icon', className, `cc-custom-icon__${type}`, {
      'cc-custom-icon--active': active
    });
    return <i className={cls} onClick={onClick}></i>
  }

  import { CustomIcon } from '@C/_common'
  export default function DeletableBlock(props) {
    const { title, children, index, onDelete } = props;

    return (
      <div className='cc-delete-block'>
        <CustomIcon type="delete" onClick={() => handleDelete(index)} />
        {/* 注释：子组件 */}
        {children}
      </div>
    )

    function handleDelete(index) {
      onDelete(index)
    }
  }
  ```


## 实现原理

  * __本质__：`React.createElement` 的语法糖。
  * __解析规则__：`< 开头的标签使用 HTML 规则解析、{ 开头的使用 JS 规则解析`。
  * JSX 为什么不会直接渲染为 DOM
    * react 需要根据需求通过不同库将元素渲染到对应场景：`react-dom -> 页面、react-canvas -> canvas、ReactNative -> 原生 App`。
    * 当数据变化而需要更新组件时，可以通过快速算法操作 Js 对象而不用操作 DOM，这样可以尽量减少浏览器重排而极大地优化性能。

  <div align="center"> 
    ![JSX 实现原理](/images/react/jsx.png)
  </div>


  ```js
  // 编译前：JSX 表达式
  <div class="input-wrap">
    <input 
      type="text" 
      autocomplete="off" 
      value="" 
      id="mq" 
      class="input" 
      title="请输入搜索文字" />
    <button>搜索</button>
  </div>

  // 编译过程：执行 React.createElement
  React.createElement("div", {className: 'input-wrap'},
    React.createElement(
      "input",
      { type:'text',
        autocomplete:"off",
        value:"",
        id:"mq",
        class:"input",
        title:"请输入搜索文字" 
      }
    ),
    React.createElement('button', null, "搜索")
  )

  // 编译结果：虚拟 DOM
  {
    tagName: 'div',
    attribute: { className: 'input-wrap'},
    children: [
    {
      tagName: 'input',
      attribute: {
        type: "text",
        autocomplete:"off",
        value:"",
        id:"mq",
        class:"input",
        title:"请输入搜索文字"
        }
    },
    {
      tagName: "button",
      attribute: null,
      children: '搜索'
      }
    ]
  }
  ```


# 四、组件化开发

## 组件创建
> 组件名的首字母必须大写，因为 JSX 转换时会调用 `React.createElement(type, config, children)`。type 声明了元素类型：首字母大写时会被 babel 看作一个组件而传入变量，小写时则看作一个 html 标签而传入字符串。


  * __函数式__：无状态组件
    * 组件不会被实例化，整体渲染性能较好，应尽量使用。
    * 组件不能操作 state，不能访问生命周期函数和 this。
  * __ES5 原生方式__：正在逐渐废弃
    * 每个成员函数的 this 都会由 React 自动绑定，导致不必要的性能开销。
    * 创建组件时可以添加 mixins 属性，并将可供混合的所有类的数组形式赋给它。
  * __ES6 类的方式__：有状态组件
    * 成员函数需要开发者手动绑定，否则 this 不能获取当前组件的实例对象。
    * state 是在 constructor 中实现初始化，props 属性类型和组件默认属性作为组件类的属性而不是组件实例的属性，所以使用类的静态属性配置。


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

  * __组件分类__
    * 根据组件的职责：`展示组件、容器组件`。
    * 根据组件的定义方式：`函数组件、类组件`。
    * 根据组件内部是否维护 state：`无状态组件、有状态组件`。
  * __区别与联系__
    * 函数组件、无状态组件和展示型组件主要关注 UI 展现，类组件、有状态组件和容器型组件主要关注数据逻辑。
    * 函数组件一定是无状态组件，展示型组件一般是无状态组件。类组件可以是有状态组件、无状态组件，容器型组件一般是有状态组件。


## 组件模式
> React 组件使用的最佳方式，最初是为了将数据逻辑和 UI 表现层进行分离而引入。通过在组件之间划分职责而创建可重用、高内聚的组件，这些组件可用于组合复杂的 UI，这在构建可扩展的应用程序时尤其重要。


  * __展示组件__：使用纯函数来简化表述的无状态组件。
  * __容器组件__：获取数据并渲染子组件的有状态组件。
  * __高阶组件__
    * 本质：一个接收一个组件作为参数并进行修改，然后返回一个新组件的函数。
    * 应用：使用 react-router-v4 之后就可以使用 `withRouter()` 来继承以 props 形式传递给组件的各种方法。使用了 redux 之后就可以使用 `connect({})()` 方法来将展示组件和 store 中的数据进行连接。
  * __渲染回调__：主要用于共享或重用组件逻辑。虽然许多开发人员倾向于使用高阶组件的可重用逻辑，但是渲染回调减少了命名空间冲突并更好地说明了逻辑来源。

    ```js
    // 高阶组件
    import {withRouter} from 'react-router-dom';
    class App extends React.Component {
      constructor() {
        super();
        this.state = {path: ''}
      }
      componentDidMount() {
        let path = this.props.location.pathname;
        this.setState(() => {
          return { path }
        })
      }
      render() {
        return (<h1>rendering at: {this.state.path}</h1>)
      }
    }
    // 使用了 withRouter 则可以直接访问 this.props.locationlocation
    // 而不需要将 location 作为 props 直接传入，非常方便。
    export default withRouter(App)

    // 渲染回调：本质是暴露了外部属性 children
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
        return <p onClick={this.inct}>{this.props.children(this.state)}</p>
      }
    }
    class App extends React.Component {
      render() {
        return  <Counter>{state => (<h1>The count is: {state.count}</h1> )}</Counter>
      }
    }
    ```


## 绑定 this

  1. __constructor + bind__：只需要在构造函数中预先绑定一次
  2. __render + bind__：每次渲染时都需要重新绑定，存在性能问题
  3. __箭头函数__
    * ES6 写法：只能在 render 添加，存在性能问题且不能移除监听事件。
    * ES7 写法：ES6 的优化方案，可以在 Class 中直接赋值。需要安装 babel-preset-stage-2 并进行配置  `.babelrc："presets":["react","env","stage-2"]`。

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


## 组件 API
> npm install react 时得到组件及其 API。组件接收 props 输入并返回描述(声明)用户界面的 React 元素。这就是 React 被称为声明性 API 的原因，因为 React 通过传入参数即可渲染出对应的 UI 外观。

### render
> class 创建组件时用于实例化 React 组件(转换 Jsx 并插入到指定节点)。当组件 props、state 被改变时会重新执行，shouldComponentUpdate 返回 false 时则不会执行。

  ```js
  // 三个参数：渲染元素、插入节点、回调函数
  ReactDOM.render(
    <App />,
    document.getElementById('root')
  )
  ReactDOM.render(
    React.createElement(App),  
    document.getElementById('root')
  )
  ```


### state
> 管理组件自身数据的对内接口，更新数据时通过异步方法 `this.setState()`。

  ```js
  export default class MyCom extends React.Component {
    constructor(){
      super();
      this.state={
        name: "张三",
        age: 20
      }
    }

    handleDelete = () => {
      this.setState({
        name: "",
        age: 0
      })
    }

    // 属性为变量时加 []
    handleClick (option, value){
      this.setState({
        [option]: value
      })
    }

    render() {
      return (
        <div>
          <button onDelete={this.handleDelete}></button>
          <button onClick={ () => this.handleClick("name")}>点击</button>
        </div>
      )
    }
  }
  ```


### props
> 实现父子组件通信的对外接口，数据是只读的但可以在子组件限制其类型和默认值。

  ```js
  // 传递数据和事件
  export default function Block (){
    const name = "Mary"
    const handleDelete = () => { }
    return (
      <>
        <Name name={name} />
        <DeletableBlock onDelete={handleDelete} />
      </>
    )
  }

  export default function Name ({name}){
    return <span>{name}</span>
  }
  export default function DeletableBlock(props) {
    const { children, onDelete } = props;
    return <button type="delete" onClick={() => onDelete()}>清空</button>
  }

  // 限制类型和默认值：ES5、ES6
  import PropTypes from 'prop-types';
  Name.propTypes = {
    name: PropTypes.string
  }
  Name.defaultProps = {
    name: 'Mary'
  }

  export default class List extends React.Component {
    static defaultProps = {
      list: [123, 123, 123]
    }
    static propTypes = {
      list: PropTypes.array,
    }

    constructor(props){
      super(props);         
    }
    render() {
      return (
        this.props.list.map((item,index)=><li key={index}>item.name</li>)
      )
    }
  }
  ```


### Context

* __功能__：通过上下文对象实现跨层级组件通信，避免了在每个层级都需要手动传递 props。
* __实现原理__：基于生产者消费者模式。首先通过 `React.createContext` 创建一个包含两个组件的上下文对象：Provider (生产者：定义数据的一个父组件)、Consumer (消费者：使用数据的一个或多个子组件)。
* __应用场景__：不建议在 App 中使用，但如果开发组件时可以确保组件不会破坏组件树的依赖关系而且影响范围小，可以考虑使用 Context 解决一些问题。

  ```js
  // index.js：父组件
  import React from 'react';
  import Son from './son';
  export const {Provider,Consumer} = React.createContext("value默认值");
  export default class App extends React.Component {
    render() {
      let name ="context 通信"
      return (<Provider value={name}> <Son /> </Provider>)
    }
  }

  // son.js：子组件、孙组件
  import React from 'react';
  import { Consumer } from "./index";
  export default function Son(props) {
    return (<Consumer>{ name => <div>子组件获取的值:{name}</div> }</Consumer>)
  }
  ```


### 生命周期

  <div align="center"> 
    ![生命周期执行顺序](/images/react/life_cycle.png)
  </div>


  ```js
  class A extends React.Component {
    // 用于初始化 state、绑定事件
    constructor() {}
    
    // 在初始化和更新时触发并返回新对象 state，不更新则可返回 null
    static getDerivedStateFromProps(nextProps, prevState) {}

    // 判断是否需要更新组件，常用于组件性能优化
    shouldComponentUpdate(nextProps, nextState) {}

    // 组件挂载后调用，常用于 axios 请求、setState 数据、操作 dom
    componentDidMount() {}

    // 最新渲染输出（提交到 DOM 节点）之前前被调用，用于获取最新数据
    getSnapshotBeforeUpdate() {}

    // 组件即将销毁，可用于移除事件监听、定时器等
    componentWillUnmount() {}

    // 组件销毁后调用
    componentDidUnMount() {}

    // 组件更新后调用
    componentDidUpdate() {}

    // 渲染组件函数
    render() {}
  }
  ```






