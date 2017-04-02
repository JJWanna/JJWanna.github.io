---
title: 'Redux:Store'
date: 2017-04-01 15:59:32
tags: Redux Store
---
* Redux 应用里的 Actions 表示在应用里发生的事情，Reducers 会根据发生的事情去更新应用的状态。再来了解一下 Store。

* Store 的主要任务是：

1.持有应用的状态。
2.允许访问状态：getState()。
3.允许更新状态：dispatch(action)。
4.注册监听器：subscribe(listener)。
5.用 subscribe(listener) 返回的东西，可以注销监听器。

注意在 Redux 应用里只有一个 Store。如果想分离数据处理逻辑，可以使用组合 Reducer 的方式。
<!--more-->
有了 Reducer ，很容易用它去创建一个 Store 。之前我们用 combineReducers() 把几个 Reducer 组合成了一个，现在我们可以去导入它，然后把它交给 createStore()。

    import { createStore } from 'redux'
    import todoApp from './reducers'
    let store = createStore(todoApp)

也可以提供一个初始状态，作为 createStore() 的第二个参数：
    
    let store = createStore(todoApp, window.STATE_FROM_SERVER)
    
    
### 调度动作

创建了 Store 以后，再去测试一下应用能不能正常运行：

    import { addTodo, toggleTodo, setVisibilityFilter, VisibilityFilters } from './actions'
    
    // Log the initial state
    console.log(store.getState())
    
    // Every time the state changes, log it
    // Note that subscribe() returns a function for unregistering the listener
    let unsubscribe = store.subscribe(() =>
      console.log(store.getState())
    )
    
    // Dispatch some actions
    store.dispatch(addTodo('Learn about actions'))
    store.dispatch(addTodo('Learn about reducers'))
    store.dispatch(addTodo('Learn about store'))
    store.dispatch(toggleTodo(0))
    store.dispatch(toggleTodo(1))
    store.dispatch(setVisibilityFilter(VisibilityFilters.SHOW_COMPLETED))
    
    // Stop listening to state updates
    unsubscribe()
    
观察 Store 里的状态是怎么变化的：
![](/images/170401-1.png)

在写界面之前，我们已经设置好了应用的一些行为。

Source Code

index.js

    import { createStore } from 'redux'
    import todoApp from './reducers'
    
    let store = createStore(todoApp)
