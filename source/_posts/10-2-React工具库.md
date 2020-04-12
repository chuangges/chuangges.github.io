---
title: React 工具库
tags:
  - React
categories: React
top: false
keywords:
  - React
date: 2019-08-29 14:27:28
description: Hook、Redux、Mobox
---

# 一、Hook
> react16.8 引入的特性，它允许你在不写 class 的情况下操作 state 和其它特性。从具象上来说是为函数组件（纯函数）提供副作用能力的 React API，从抽象意义上来说是确定状态源的解决方案。使用规则如下：

  * 函数式编程，你不需要定义 constructor、render、class。
  * 没有了显性的生命周期，所有渲染后的执行方法都在 useEffect 里面统一管理。
  * 只能在最顶层使用 Hook，不能在循环、条件判断或嵌套函数中调用。只能在 React 函数组件中调用 Hook，不能在普通函数中调用。


## 组件复用方案
> 对于组件之间的数据共享问题，React官方采用单向数据流（Flux）来解决。对于组件的复用，React 团队一直在探索解决方案：早期使用 __CreateClass + Mixins__ (混入：将对象复制到另一个对象)，然后使用 Class Component 取代 CreateClass 之后又设计了 __Render Props__ (通过 props 接受一个返回 react element 的函数来实现动态渲染)、__Higher Order Component__ (高阶组件：接收原组件为参数并返回一个新组件的函数)，直到再后来的 __Function Component + Hooks__(封装组件中状态相关逻辑的函数) 设计。

  * __Mixin__ 缺陷
    * 命名冲突：多个 Mixin 可能定义了相同的 state 字段而导致数据覆盖问题。
    * 相关依赖：组件与 Mixin、多个 Mixin 之间都可能存在依赖关系，维护成本较高。
    * 增加复杂性：一个组件引入过多 mixin 时，代码逻辑将会非常复杂，过多的状态也降低了应用的可预测性。
  * __HOC__ 优势
    * HOC 不会影响组件内部的状态，不存在冲突和互相干扰，这就降低了耦合度。
    * 不同于 Mixin 的打平+合并，HOC 具有层级结构（组件树结构），降低了复杂度。
  * HOC 缺陷
    * 嵌套地狱：每一次 HOC 调用都会产生一个组件实例，过多的嵌套会导致难以溯源，而且可能会存在 props 属性覆盖问题。
    * 静态构建：HOC 只是声明了新组件但不会马上渲染，只有在组件被渲染时才执行。
  * __Render Prop__ 优势
    * 动态构建，组件会重新渲染。
    * 不用担心 props 的命名冲突。
    * 可以溯源，子组件的 props 一定是来自于直接父组件。
  * Render Prop 缺陷
    * 嵌套地狱：虽然摆脱了组件多层嵌套的问题，但是转化为了函数回调的嵌套。
    * 使用繁琐：HOC 可以通过装饰器语法的一行代码实现复用，Render Props 则不行。
    * 没有组件的上下文：没有 this.props 属性，不能像 HOC 那样可以直接获取到子组件实例对象 this.props.children。
  * __React Hooks__ 优势
    * 解决了以上的嵌套问题，而且实现了视图和状态的分离，Hooks 还可以相互组合。
    * Hooks 为函数组件而生，从而解决了类组件的几大问题：this 指向容易错误、声明周期中的逻辑代码难以理解和维护、代码复用成本高等。
  * __React Hooks__ 缺陷
    * 用法限制：只能在最顶层使用 Hook，不能在循环、条件判断或嵌套函数中调用。只能在 React 函数组件中调用 Hook，不能在普通函数中调用。
    * 在闭包场景可能会引用到旧的 state、props 值，React.memo 也不能完全替代 shouldComponentUpdate（因为拿不到 state change，只针对 props change）。


  ```js
  // HOC 高阶组件
  function getComponent(WrapCom) {
    return class extends React.Component {
      render() {
        return <WrapCom {...this.props}/>;
      }
    }
  }

  // Render Prop
  <Router>
    <Route path="/home" render={() => <div>Home</div>} />
  </Router>
  ```


## 基础 API

  ```js
  import React, { useState, useEffect, useContext } from 'react'
  useEffect // 万能的生命周期、每次都触发的生命周期、可以重复写多个的生命周期
  useContent // react自带的redux(mobx)、方便组建间传值
  export default function TodoList(props) {

    // 初始状态、改变状态的方法
    const [count, setCount] = useState(0)

    /**
    * @prame 参数：function，默认每次渲染完成后执行，函数里更新 dom、添加订阅等。
    * @prame 参数：[] 表示在首次渲染时执行，[count] 则表示只在 count 变化时执行。
    **/
    useEffect(() = {
      document.title = `You clicked ${count} times`;
    }, [])
    // 清除 effect：组件卸载时需要清除 effect 创建的订阅、计时器等。
    useEffect(() => {
      const id = setInterval(() => {
        setCount(count => count + 1)
      }, 1000)
      return () => clearInterval(id)
    }, [])

    // useContent
    const value = useContext(MyContext);
    
    return(
      <button onClick={() => {setCount(count + 1)}}>Click</button>
    )
  }
  ```



## 自定义 Hook



# 一、Redux


## 核心实现
> 利用闭包管理 state 等变量，然后在 dispatch 时通过用户定义 reducer 获取新状态并赋值给 state，再把外部通过 subscribe 订阅触发。

  ```js
  function createStore(reducer) {
    let currentState
    let subscribers = []
    function dispatch(action) {
      currentState = reducer(currentState, action);
      subscribers.forEach(s => s())
    }
    function getState() {
      return currentState;
    }
    
    function subscribe(subscriber) {
        subscribers.push(subscriber)
        return function unsubscribe() {
            ...
        }
    }
    dispatch({ type: 'INIT' });
    return {
      dispatch,
      getState,
    };
  }
  ```


## 应用实例

```js
// ================= redux ===================
// src/store/index.js
import { createStore } from 'redux'

const reducer = (state = {count: 0}, action) => {
  switch (action.type){
    case 'INCREASE': return {count: state.count + 1};
    case 'DECREASE': return {count: state.count - 1};
    default: return state;   // 初始化
  }
}

const actions = {
  increase: () => ({type: 'INCREASE'}),
  decrease: () => ({type: 'DECREASE'})
}

const store = createStore(reducer);
store.subscribe(() =>
  console.log(store.getState())
)

export default store

// ReduxTest.js
import React, { Component } from 'react'
import store from '../store'

export default class ReduxTest extends Component {

  render() {
    return (
      <div>
        <span>{store.getState()}</span>
        <button onClick={() => store.dispatch({ type: 'add' })}>+</button>
        <button onClick={() => store.dispatch({ type: 'minus' })}>-</button>
      </div>
    )
  }
}

// ====================== react-redux =======================
// index.js 入口文件
import React from 'react'
import ReactDOM from 'react-dom'
import App from './App'
import store from './store'
import { Provider } from 'react-redux'

ReactDOM.render(
  <Provider store={store}>
    <App title="hello React" />
  </Provider>,
  document.getElementById('root')
)

// store/index.js
import { createStore } from 'redux'

const counterReducer = function(state = 0, action) {
  const num = action.payload || 1
  switch (action.type) {
    case 'add':
      return state + num
    case 'minus':
      return state - num
    default:
      return state
  }
}

const store = createStore(counterReducer)

export default store

// ReduxTest.js
import React, { Component } from 'react'
import { connect } from 'react-redux'

// 参数1：mapStateToProps = (state) => { return { num:state } }
// 参数2：mapDispatchToProps = dispatch => { return { add: () => { dispatch({ type: 'add' }) } } }
@connect(
  state => ({ num: state }),
  {
    add: num => ({ type: 'add', payload: num }),
    minus: () => ({ type: 'minus' })
  }
)
class ReduxTest extends Component {
  render() {
    return (
      <div>
        <span>{this.props.num}</span>
        <button onClick={() => this.props.add(2)}>+</button>
        <button onClick={() => this.props.minus()}>-</button>
      </div>
    )
  }
}

export default ReduxTest
```







【react-redux】

定位：react-redux是为了让redux更好的适用于react而生的一个库，使用这个库，要遵循一些规范；

主要内容

UI组件：就像一个纯函数，没有state，不做数据处理，只关注view，长什么样子完全取决于接收了什么参数（props）
容器组件：关注行为派发和数据梳理，把处理好的数据交给UI组件呈现；React-Redux规定，所有的UI组件都由用户提供，容器组件则是由React-Redux自动生成。也就是说，用户负责视觉层，状态管理则是全部交给它。
connect：这个方法可以从UI组件生成容器组件；但容器组件的定位是处理数据、响应行为，因此，需要对UI组件添加额外的东西，即mapStateToProps和mapDispatchToProps，也就是在组件外加了一层state；
mapStateToProps：一个函数， 建立一个从（外部的）state对象到（UI组件的）props对象的映射关系。 它返回了一个拥有键值对的对象；
mapDispatchToProps：用来建立UI组件的参数到store.dispatch方法的映射。 它定义了哪些用户的操作应该当作动作，它可以是一个函数，也可以是一个对象。
       以上，redux的出现已经可以使react建立起一个大型应用，而且能够很好的管理状态、组织代码，但是有个棘手的问题没有很好地解决，那就是异步；  



　npm i -S redux 　　// 这里时我们需要下载的 redux 组件通信的插件

　　　　npm i -S prop-types 　　// 我们的较验规则

　　　　npm i -S react-redux　　// 我们的 react 版的 redux 为了就是更方便的使用 redux

　　　　npm i -S redux-thunk　　// 异步加载我们的代码

　　　　npm i -D redux-devtools-extension　　// 我们可以在谷歌中下载 redux 的插件，然后在项目中下载 redex-devtools-extension 的插件，我们就能在谷歌浏览器中实时的掌握 redux 的数据

　　　　npm i -S react-router　　// 我们的路由插件




【redux-saga】：

定位：react中间件；旨在于更好、更易地解决异步操作（action）；redux-saga相当于在Redux原有数据流中多了一层，对Action进行监听，捕获到监听的Action后可以派生一个新的任务对state进行维护；

特点：通过 Generator 函数来创建，可以用同步的方式写异步的代码；

API：

Effect： 一个简单的对象，这个对象包含了一些给 middleware 解释执行的信息。所有的Effect 都必须被 yield 才会执行。
put：触发某个action，作用和dispatch相同；


# 二、

# 三、Zarm
https://zarm.design/#/components/collapse







