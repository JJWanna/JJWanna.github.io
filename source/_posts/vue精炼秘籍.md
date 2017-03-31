---
title: vue精炼秘籍
date: 2017-03-30 19:20:40
tags: vue vuejs
---

* Vuex理论介绍
* Vuex核心概念
* Vuex搭载vue-Resource&vue-router
* Vue2+Webpack2+KOA2构建大型应用

### Vuex理论基础
1.Vuex 是一个专为 Vue.js 应用程序开发的状态管理模式。它采用集中式存储管理应用的所有组件的状态，并以相应的规则保证状态以一种可预测的方式发生变化。Vuex 也集成到 devtools。
2.不开发大型单页应用尽量不要使用Vuex。
3.多个视图依赖于同一状态。来自不同视图的行为需要变更同一状态。
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
    
### Vuex核心模块
* State
* Getters
* Mutations
* Actions
* Modules

1.State
* 单一状态树，用一个对象就包含了全部的应用层级状态。
* mapState 当一个组件需要获取多个状态时候，将这些状态都声明为计算属性会有些重复和冗余。
* 对象展开符号......mapState({})


    import { mapState } from 'vuex'
    export default {
      computed: mapState({
        count: state => state.count,
        countAlias: ‘count',
        countPlusLocalState (state) {
          return state.count + this.localCount
        }
      }
      //…mapState({count})扩展函数符
    }
    
2.Getters
* 从 store 中的 state 中派生出一些状态
* mapGetters 辅助函数仅仅是将 store 中的 getters 映射到局部计算属性
* getters（可以认为是 store 的计算属性）


    const store = new Vuex.Store({
      state: {
        todos: [
          { id: 1, text: '...', done: true },
          { id: 2, text: '...', done: false }
        ]
      },
      getters: {
        doneTodos: state => {
          return state.todos.filter(todo => todo.done)
        }
      }
    })
    
    computed: {
      doneTodosCount () {
        return this.$store.getters.doneTodosCount
      }
    }
     
    computed: {
        ...mapGetters([
          'doneTodosCount'
        ])
      }
      
3.Mutations
* 更改 Vuex 的 store 中的状态的唯一方法是提交 mutation（不能直接调用句柄，必须通过触发）
* mutations就是vue methods
* 每个 mutation 都有一个字符串的 事件类型 (type) 和 一个 回调函数 (handler)
* 使用常量替代 Mutation 事件类型
* mutation 必须是同步函数


    // mutation-types.js
    export const SOME_MUTATION = ‘SOME_MUTATION'
    
    // store.js
    import { SOME_MUTATION } from './mutation-types'
    
    const store = new Vuex.Store({
      state: { ... },
      mutations: {
        [SOME_MUTATION] (state) {
          // mutate state
        }
      }
    })
    
    
    //Mutations (调用)
    import { mapMutations } from ‘vuex'
    import { SOME_MUTATION } from './mutation-types'
    
    export default {
      methods: {
        test(){
              this.$store.commit(SOME_MUTATION)
        },
        ...mapMutations([
          'SOME_MUTATION' 
          // 映射 this.increment() 为 this.$store.commit('SOME_MUTATION')
        ])
      }
    }
    
    
4.Actions
* Action 提交的是 mutation
* Action 可以包含任意异步操作
* mapActions 辅助函数将组件的 methods 映射为 store.dispatch 调用
* view    store.dispatch(‘increment’)
* action  commit('someMutation')


    actions: {
      async actionA ({ commit }) {
        commit('gotData', await getData())
      },
      async actionB ({ dispatch, commit }) {
        await dispatch('actionA') // 等待 actionA 完成
        commit('gotOtherData', await getOtherData())
      }
    }
    
    
    //Actions（调用）
    import { mapActions } from 'vuex'
    export default {
      methods: {
        test(){
             store.dispatch(‘actionB');
        },
        ...mapActions([
          ‘actionB'
          // 映射 this.increment() 
         //this.$store.dispatch(‘actionB’)
        ])
      }
    }
    
5.Modules
* Vuex 运行我们将 store 分割到模块（module）。每个模块拥有自己的 state、mutation、action、getters、甚至是嵌套子模块——从上至下进行类似的分割。
* store 创建之后，你可以使用 store.registerModule 方法注册模块：

    
    const moduleA = {
      state: { ... },
      mutations: { ... },
      actions: { ... },
      getters: { ... }
    }
    
    const moduleB = {
      state: { ... },
      mutations: { ... },
      actions: { ... }
    }
    
    const store = new Vuex.Store({
      modules: {
        a: moduleA,
        b: moduleB
      }
    })
    
    store.state.a // -> moduleA 的状态
    store.state.b // -> moduleB 的状态
    
    store.registerModule('myModule', {
      // ...
    })
    
    
    
6.Plugins
* Vuex 的 store 接受 plugins 选项，这个选项暴露出每次 mutation 的钩子。Vuex 插件就是一个函数，它接收 sotre 作为唯一参数。
* 在插件中不允许直接修改状态——类似于组件，只能通过提交 mutation 来触发变化。
* 自定义的状态快照。

    
    const myPlugin = store => {
      // 当 store 初始化后调用
      store.subscribe((mutation, state) => {
        // 每次 mutation 之后调用
        // mutation 的格式为 { type, payload }
      })
    }
    
    const store = new Vuex.Store({
      plugins: [myPlugin]
    })
    
    
### 补充

* Vuex的思维处理表单 <input :value="message" @input="updateMessage">
* 测试Actions需要增加一个mocking服务层，测试文件中用 mock 服务回应 API 调用。为了便于解决 mock 依赖，可以用 Webpack 和 inject-loader 打包测试文件。
* Hot Module Replacement API，Vuex 支持在开发过程中热重载 mutation、modules、actions、和getters。


    测试Actions演示
    import { expect } from 'chai'
    const actionsInjector = require('inject!./actions')
    // 使用 mocks 创建模块
    const actions = actionsInjector({
      '../api/shop': {
        getProducts (cb) {
          setTimeout(() => {
            cb([ /* mocked response */ ])
          }, 100)
        }
      }
    })
    
    
    测试代码
    describe('actions', () => {
      it('getAllProducts', done => {
        testAction(actions.getAllProducts, [], {}, [
          { type: 'REQUEST_PRODUCTS' },
          { type: 'RECEIVE_PRODUCTS', payload: { } }
        ], done)
      })
    })
    
    
### vuex-router-sync

1.视图层
    
     <div id="app">
       <h1>Hello App!</h1>
       <p>
         <router-link to="/foo">Go to Foo</router-link>
         <router-link to="/bar">Go to Bar</router-link>
       </p>
       <router-view></router-view>
     </div>
     
2.结合 Vuex

    import { sync } from 'vuex-router-sync'
    import store from './vuex/store' 
    import router from './router' 
    sync(store, router) 
    this.$store.state.route.path   // current path (string)
    this.$store.state.route.params // current params (object) this.$store.state.route.query  // current query (object)

