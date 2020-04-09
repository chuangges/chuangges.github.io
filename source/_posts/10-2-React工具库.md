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
> 从具象上来说是为函数组件（纯函数）提供副作用能力的 React API，从抽象意义上来说是确定状态源的解决方案。

## Class Component 的问题

### 组件复用困局
> 对于组件之间的数据共享问题，React官方采用单向数据流（Flux）来解决。对于组件的复用，React 团队一直在探索解决方案：早期使用 __CreateClass + Mixins__ (混入：将对象复制到另一个对象)，然后使用 Class Component 取代 CreateClass 之后又设计了 __Render Props__ ()、__Higher Order Component__ (高阶组件：接收原组件为参数并返回一个新组件的函数)，直到再后来的 __Function Component + Hooks__ 设计。

* Mixin 缺陷
  * 命名冲突：多个 Mixin 可能定义了相同的 state 字段而导致数据覆盖问题。
  * 相关依赖：组件与 Mixin、多个 Mixin 之间都可能存在依赖关系，维护成本较高。
  * 增加复杂性：一个组件引入过多 mixin 时，代码逻辑将会非常复杂，过多的状态也降低了应用的可预测性。
* HOC 优势
  * HOC 不会影响组件内部的状态，不存在冲突和互相干扰，这就降低了耦合度。
  * 不同于 Mixin 的打平+合并，HOC 具有天然的层级结构（组件树结构），降低了复杂度。
* HOC 缺陷
  * 嵌套地狱：过多的嵌套会导致难以溯源，而且存在属性覆盖问题。
  * 静态构建：HOC 只是声明了新组件但不会马上渲染，只有在新组件被渲染时才会执行。
* 


  

```js
// HOC 高阶组件
function getComponent(WrappedComponent) {
  return class extends React.Component {
    render() {
      return <WrappedComponent {...this.props}/>;
    }
  }
}
```



Render Props：

数据流向更直观了，子孙组件可以很明确地看到数据来源
但本质上Render Props是基于闭包实现的，大量地用于组件的复用将不可避免地引入了callback hell问题
丢失了组件的上下文，因此没有this.props属性，不能像HOC那样访问this.props.children


# 一、Redux


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







