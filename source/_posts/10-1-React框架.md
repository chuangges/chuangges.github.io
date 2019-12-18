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
  
  
  1. __声明式设计__：采用简洁易懂的声明范式，可以轻松描述应用。
  2. __单向数据流__：推崇一种单向的数据流动模式，减少了重复代码。
  3. __高效渲染__：通过组件构建虚拟 DOM ，最大限度地减少了与 DOM 的交互。
    * __virtual DOM__：描述 dom 元素结构结构和样式的 js 对象。组件状态改变时操作内存数据而不需要遍历元素的所有属性。
    * __DOM Diff__：对比改变前后两个 js 对象差异的算法，用于计算出更新的最小步骤并最终只把变化的部分重新渲染到真实 dom。
  4. __组件化开发__
    * DOM 树上的节点被称为元素，Virtual DOM 上的节点则称为组件。
    * 组件是封装起来的具有独立功能的 UI 模块，具有高内聚、低耦合的特点，React 组件开发推荐使用 JSX 而不能用模板。
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


## Flux 架构
> 由于 React 只涉及 UI 层的处理，所以构建大型应用时需要搭配一种架构模式才能使后期维护成本相对较小。Flux 正是 Facebook 官方提出的一套前端应用架构模式，它只是一种架构思想而非具体的框架。

  * __Flux 模式__：代码结构比较简单，它将一个应用分成以下四个部分，核心思想是单向数据流：`Action、Dispatcher、Store`，核心流程是 Dispatcher。
    <div align="center"> 
      ![Flux 模式](/images/react/flux.png)
    </div>
  * __MVC 模式__：处理多数据和复杂业务时可能会变得非常混乱，因为很多 view 都具备修改多个 model 的能力。单个修改行为可以称之为一个 Action，一个 Action 的产生可能是用户行为或一个 Ajax 请求。解决方法是可以将多个 model 抽象成一个 model，但会造成代码冗余和多余的性能损耗。
    <div align="center"> 
      ![MVC 模式](/images/react/mvc.png)
    </div>


# 二、构建工具

## create-react-app
> 简陋的官方脚手架，使用门槛相对较高。

  ```js
  // localhost:3000
  npx create-react-app my-app
  cd my-app
  
  yarn、npm start
  yarn、npm run build
  ```

### 项目配置
> 两种方法：1、npm run eject 暴露默认隐藏的配置目录 (不可逆)，2、安装 react-app-rewired 覆盖默认配置。
  
  ```js
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

### less

  ```js
  // 法一：webpack.config.dev.js、webpack.config.prod.js 
  yarn add less less-loader

  {
    test: /\.(css|less)$/,   // 划重点
    use: [
      require.resolve( 'style-loader' ),
      {
        loader: require.resolve( 'css-loader' ),
        options: {
        importLoaders: 1,
      },
      ...
      {
        loader: require.resolve( 'less-loader' ),  // 划重点
      }
    ],              
  },

  // 法二：config-overrides.js
  yarn add less-loader less --save-dev

  const { injectBabelPlugin } = require('react-app-rewired');
  + const rewireLess = require('react-app-rewire-less');
  module.exports = function override(config, env) {
    config = injectBabelPlugin(['import', { 
      libraryName: 'antd', 
      libraryDirectory: 'es', 
      style: 'css' 
    }], config);

    + config = rewireLess.withLoaderOptions({
      modifyVars: {
        "@primary-color": "#1DA57A"
      }
    })(config, env);

    return config;
  }
  ```


## umi
> dva 开发者云谦编写的一个企业级 react 应用框架，也是一个开箱即用的脚手架工具。它可以看作是一个专注性能的类似 next.js 的前端框架，通过约定、自动生成和解析代码等方式来辅助开发，减少了开发者的代码量。

  * __react 开发问题__
    * 项目做大的时候，开发调试的启动和热更新时间会变得很长。
    * 大应用下，网站打开很慢，有没有办法基于路由做到按需加载。
    * dva model 每次都要手写载入，能否一开始就同项目初始化好。
  * __umi 优势__
    * 开箱即用，内置 less、react、react-router 等。
    * 类 next.js 且功能完备的路由约定，同时支持配置的路由方式。
    * 完善的插件体系，覆盖从源码到构建产物的每个生命周期。
    * 高性能，通过插件支持 PWA、以路由为单元的 code splitting 等。
    * 支持静态页面导出，适配各种环境，比如无线业务、支付宝钱包等。
    * 支持一键开启 dll、hard-source-webpack-plugin 等。
    * 一键兼容到 IE9，基于 umi-plugin-polyfills。
    * 完善的 TypeScript 支持，包括 d.ts 定义和 umi test。
    * 深入融合 dva 数据流，可用来处理数据流、响应一些复杂交互。
  * __基础使用__
    ```js
    // 安装 umi、脚手架
    cnpm install -g umi create-umi
    umi -v

    // 初始化目录：选择 app、 antd / dva 
    create-umi

    // 新建页面
    umi g page user  

    // 运行和打包
    npm start、umi dev
    npm run build、umi build
    ```


# 三、JSX 表达式
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


# 三、路由功能

## 相关库
> react-router-4.0 (以下简称 RR4) 采用单代码仓库模型架构，包含了若干相互独立的包。注意：react-router-dom/native 已经包含了 react-router 的依赖，使用时不需要安装和引用 react-router。

  * `react-router`：核心公用组件和方法。具体有：MemoryRouter、Prompt、Redirect、Route、RouterStaticRouter、 Switch、matchPath、withRouter。
  * `react-router-dom`：用于 web 环境开发，提供了 dom 操作控制路由的主要方法有 BrowserRouter、 HashRouter、 Link 、NavLink。
  * `react-router-native`：用于给 native 相关应用提供路由支持，主要有 NativeRouter、 Link、 DeepLinkingAndroidBackButton。
  * `react-router-redux`：React Router 和 Redux 的集成。
  * `react-router-config`：集中配置静态路由，主要有 matchRoutes、renderRoutes。
  

## 路由组件
> 所有与路由有关的组件（Link、NavLink、Route、Switch）必须包裹在容器组件中，容器组件有且只能有一个子元素。

  * 容器组件
    * `<BrowserRouter>`：浏览器自带的 H5 API，restful 风格。需要添加服务器配置 (node/nginx)，让前端发送的请求映射到对应的 html 文件。
    * `<HashRouter>`：使用 hash 方式在前端完成路由切换。`#` 后面的内容不会发送到服务器端，对于后端来说路由地址始终不变。
    * `<MemoryRouter>`：在内存中管理 history，地址栏不会变化。用于 reactNative。
    * `<NativeRouter>`
    * `<StaticRouter>`
  * 相关组件
    * `<Link>`：react-router 提供，用于点击时切换路由。
    * `<NavLink>`：Link 的加强版，可以自定义选中状态和钩子函数。
    * `<Switch>`：用于嵌套 Route 组件，多个路径相同时只匹配第一个。
    * `<Route>`：用于实现 RR4 动态路由的嵌套。
    * `<Redirect>`：用于路由重定向。
    

  ```js
  <Link 
    to="/about" 
    replace={true}         // 覆盖当前路径
  >关于</Link> 

  <Link to={{
    pathname: '/courses',
    search: '?sort=name',  // 下个页面取值：props.location.state
    hash: '#the-hash',
    state: { fromDashboard: true }
  }}/>

  // 当 event id 为奇数的时候，激活链接
  const activeEvent = (match, location) => {
    if (!match) {
      return false
    }
    const eventID = parseInt(match.params.eventID)
    return !isNaN(eventID) && eventID % 2 === 1
  }
  <NavLink
    to="/home?ask" 
    exact  // 是否严格匹配
    activeClassName="selected" 
    activeStyle={{ color: 'green', fontWeight: 'bold' }}
    isActive={activeEvent}  // 判断链接是否激活的额外逻辑
  >问答</NavLink>

  <Route 
    exact                // 是否完全匹配
    strict               // path 有结尾斜线只能匹配有斜线的路径
    path="/peoples/"     // 路由匹配路径 (不能匹配 /peoples)
    component={Peoples}  // URL、Route 匹配时渲染的组件
  ><Route/>

  <Switch>
    {/* 进入客户管理页面，默认展示 List 区域内容，或者使用重定向 */}
    <Route path='/custom' exact component={List}></Route>
    <Route path='/custom/list' component={List}></Route>
    <Route path='/custom/create' component={Create}></Route>
    <Route path='/custom/Detail/:id' component={Detail}></Route>
    {/* <Redirect from='/custom' to='/custom/list'></Redirect> */}
  </Switch>

  <Redirect
    to={{
      pathname: "/login",
      search: "?name=Mike",
      state: { id: 1 }
    }}
  />
  ```


## 路由对象
> 被 Route 绑定的渲染组件，总是被传入三个属性（对象）：history、location、match。在渲染组件中也会有很多其他组件，这些组件内部如果想要获取三个对象，需要 withRouter（通过装饰器或函数调用的形式都可以）。

  ```js
  // history：用于编程式导航
  let { history } = this.props
  history.push()       // 追加一条记录   
  history.replace()    // 不会追加记录   
  history.goback()     // 回退
  history.goforward()  // 前进
  history.go(n)

  // loaction：指当前位置
  let { location: { pathname, search, state} } = this.props;
  <Link to={location} />
  <NaviveLink to={location} />
  <Redirect to={location />
  history.push(location)
  history.replace(location)

  // match：包含了路由匹配的信息
  let { match: { params } } = this.props;
  console.log(params.id)
  ```


## 路由传参
> 不推荐：localstroage、redux（页面刷新后会丢失数据）。

  ```js
  // 1、params
  <Route path='/user/:id' component={User} />  // 路由设置

  <Link to='/user/2' />                 // 传值一
  this.props.history.push("/user/2");   // 传值2二

  this.props.match.params.id     // 获取值


  // 2、URL参数
  <Route path='/user' component={User} />
  <Link to='/user?id=2' />
  this.props.location.query.id


  // 3、location 对象（哈希路由不支持）
  <Route path='/user' component={User}></Route>
  <Link to={{ 
    pathname:' /user',
    state: {id: 123},
    search:'?sort=name',
    hash:'#the-hash'
  }} />
  this.props.location.state.id
  ```


## 基础使用

  * __静态路由__：在应用渲染前的初始化阶段配置好路由信息，应用于 RR4 之前的版本。
  * __动态路由__：随着应用渲染而起作用，无需事先配置路由。核心设计理念是一切都是组件，这更符合 React 组件化的思想。
  * __嵌套路由__：Route 渲染的组件内部定义新的 Route，实现页面的局部变换，比如说标题栏不变，内容根据路由引入不同模块。
  * __响应式路由__：手机端访问 /admin，竖屏模式下只展示导航栏，横屏时展示导航栏和内容。PC 端根据屏幕大小展示不同内容。

  ```js
  cnpm i react-router-dom -S

  import React, { Component } from 'react';
  import { Route, Redirect } from 'react-router'
  import { BrowserRouter as Router, Route } from 'react-router-dom'

  // 动态路由
  <div id="app">
    <Router>
      <Route path="/" exact component={Home}/>
      <Route path="/detail/:id" exact component={Detail}/>
    </Router>
  </div>


  // 嵌套路由
  const About = ({ match }) => (
    <div>
      <Route path={match.url + '/other'} component={Other} />
    </div>
  )
  const App = () => (
    <Router>
      // 路由重定向
      <Route path="/" render={()=> ( <Redirect to="/home" />) }></Route>
      <Route path="/home" component={Home} />
      <Route path="/about" component={About} /> 
    </Router>
  )

  // 响应式路由
  const App = () => (
    <AppLayout>
      <Route path="/admin" component={Admin} />
    </AppLayout>
  )
  const Admin = () => (
    <Layout>
      <Nav />
      <Media query={PRETTY_SMALL}>
        {screenIsSmall => screenIsSmall
          ? <Switch>
              <Route exact path="/admin/dashboard" component={Dashboard}/>
              <Route path="/admin/other" component={Other} />
            </Switch>
          : <Switch>
              <Route exact path="/admin/dashboard" component={Dashboard}/>
              <Route path="/admin/other" component={Other} />
              <Redirect from="/admin" to="/admin/dashboard" />
            </Switch>
        }
      </Media>
    </Layout>
  )
  ```


# 四、状态管理
MobX 架构
MobX 的核心是观察者模式。

Store 是被观察者（observable）
组件是观察者（observer）
一旦Store有变化，会立刻被组件观察到，从而引发重新渲染。


  * __MobX__：响应式管理，state 是可变对象，适合中小型项目。
  * __Redux__：函数式管理，state 是不可变对象，适合大型项目。



# 五、组件化开发

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

## 组件渲染

  ```js
  // 1、component：属性值是一个组件，URL、Route 匹配时就会被渲染
  <Route path='/foo' component={FOO} />

  // 2、render：属性值是一个返回 jsx 元素的函数
  <Route path='/foo' render={props=>(
    <Foo {...props} data={extraProps} />
  )} />

  // 3、children：属性值同上，但返回的组件一定会被渲染，但不匹配时 match: null
  <Route path='/foo' children={props=>(
    <div className={props.match ? 'active' : ''}>
        <Foo />
    </div>
  )} />
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
  import ReactDOM from "react-dom"
  let app = document.getElementById('root')

  ReactDOM.render(<App />, app)
  ReactDOM.unmountComponentAtNode(app)
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






