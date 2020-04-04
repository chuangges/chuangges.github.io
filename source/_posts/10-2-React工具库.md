---
title: React 工具库
tags:
  - React
categories: React
top: false
keywords:
  - React
date: 2019-08-29 14:27:28
description: Redux、Mobox
---

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







