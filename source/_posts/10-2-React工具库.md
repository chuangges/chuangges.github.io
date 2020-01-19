---
title: React 工具库
tags:
  - React
categories: React
top: false
keywords:
  - React
date: 2019-08-29 14:27:28
description: Mobox 机制
---

# 一、状态管理工具 MobX
> 对于应用开发中的常见问题，React 和 MobX 都提供了最优和独特的解决方案，两者是一对强力组合。React 提供了优化 UI 渲染的机制，它使用虚拟 DOM 减少了昂贵的 DOM 变化的数量，将应用状态转换为可渲染组件树并对其进行渲染。MobX 则提供了优化应用状态与 React 组件同步的机制，它通过透明的函数响应式编程使得状态管理变得简单和可扩展，使用虚拟依赖状态图表实现在需要时更新最新数据。

  https://cn.mobx.js.org/



# 六、Redux

### 相关库
> 框架绑定：`React react-redux、Angular ng-redux、Angular2 ng2-redux、Backbone backbone-redux、Falcor redux-falcor、Deku deku-redux`。



【react-redux】

定位：react-redux是为了让redux更好的适用于react而生的一个库，使用这个库，要遵循一些规范；

主要内容

UI组件：就像一个纯函数，没有state，不做数据处理，只关注view，长什么样子完全取决于接收了什么参数（props）
容器组件：关注行为派发和数据梳理，把处理好的数据交给UI组件呈现；React-Redux规定，所有的UI组件都由用户提供，容器组件则是由React-Redux自动生成。也就是说，用户负责视觉层，状态管理则是全部交给它。
connect：这个方法可以从UI组件生成容器组件；但容器组件的定位是处理数据、响应行为，因此，需要对UI组件添加额外的东西，即mapStateToProps和mapDispatchToProps，也就是在组件外加了一层state；
mapStateToProps：一个函数， 建立一个从（外部的）state对象到（UI组件的）props对象的映射关系。 它返回了一个拥有键值对的对象；
mapDispatchToProps：用来建立UI组件的参数到store.dispatch方法的映射。 它定义了哪些用户的操作应该当作动作，它可以是一个函数，也可以是一个对象。
       以上，redux的出现已经可以使react建立起一个大型应用，而且能够很好的管理状态、组织代码，但是有个棘手的问题没有很好地解决，那就是异步；  


【redux-saga】：

定位：react中间件；旨在于更好、更易地解决异步操作（action）；redux-saga相当于在Redux原有数据流中多了一层，对Action进行监听，捕获到监听的Action后可以派生一个新的任务对state进行维护；

特点：通过 Generator 函数来创建，可以用同步的方式写异步的代码；

API：

Effect： 一个简单的对象，这个对象包含了一些给 middleware 解释执行的信息。所有的Effect 都必须被 yield 才会执行。
put：触发某个action，作用和dispatch相同；




# Zarm
https://zarm.design/#/components/collapse







