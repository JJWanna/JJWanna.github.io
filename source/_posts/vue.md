---
title: vue精炼秘籍 
date: 2017-03-31 10:13:07
tags: vuejs   vue
---

* Vuex理论介绍
* Vuex核心概念
* Vuex搭载vue-Resource&vue-router
* Vue2+Webpack2+KOA2构建大型应用

### Vuex理论基础
1.Vuex 是一个专为 Vue.js 应用程序开发的状态管理模式。它采用集中式存储管理应用的所有组件的状态，并以相应的规则保证状态以一种可预测的方式发生变化。Vuex 也集成到 devtools。
2.不开发大型单页应用尽量不要使用Vuex。
3.多个视图依赖于同一状态。来自不同视图的行为需要变更同一状态。
<!--more-->
![](/images/vue1.png)
* state，驱动应用的数据源；
* view，以声明方式将state映射到视图；
* actions，响应在view上的用户输入导致的状态变化。
![](/images/vuex1.png)
### vuex 的不同
1.Vuex 的状态存储是响应式的。当 Vue 组件从 store 中读取状态的时候，若 store 中的状态发生变化，那么相应的组件也会相应地得到高效更新。
2.你不能直接改变 store 中的状态。改变 store 中的状态的唯一途径就是显式地提交 mutations。这样使得我们可以方便地跟踪每一个状态的变化，从而让我们能够实现一些工具帮助我们更好地了解我们的应用。
* store


    Vue.use(Vuex)
    const store = new Vuex.Store({
      state: {
        count: 0
      },
      mutations: {
        increment (state) {
          state.count++
        }
      }
    })
    store.commit('increment')
    console.log(store.state.count) // -> 1
    