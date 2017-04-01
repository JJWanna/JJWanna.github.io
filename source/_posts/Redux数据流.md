---
title: Redux数据流
date: 2017-04-01 13:56:11
tags: Redux
---

* Redux 应用里的数据流是单向的。数据的生命周期有四个阶段：
1.**调用 store.dispatch(action)**
一个动作就是一个纯对象，描述了发生的事情，比如：
    
    
    {type:'LIKE_ARTICLE',articleId:42}
    {type:'FETCH_USER_SUCCESS',response:{id:3,name:'Mary}}
    {type:'ADD_TODO',text:'Read thr Redux docs.'}
    
把动作想成是一些简要的新闻： “Mary liked article 42.” ， “‘Read the Redux docs.' was added to the list of todos.”。在应用的任何地方你可以调用 store.dispatch(action)，包括组件，XHR 回调等等。
<!--more-->
2.**Redux Store 会调用你给它的 reducer**
store 会把两个参数传递给 reducer：当前的状态树，还有动作。比如在一个任务列表应用里。

3.Reducer 是纯函数，它只计算出下一个状态。给它同样的输入，任务时候它都会计算出同样的结果。Reducer 不能执行有副作用的动作，比如去调用 API，这些动作应该在调度动作之前完成。

     // The current application state (list of todos and chosen filter)
     let previousState = {
       visibleTodoFilter: 'SHOW_ALL',
       todos: [ 
         {
           text: 'Read the docs.',
           complete: false
         }
       ]
     }
    
     // The action being performed (adding a todo)
     let action = {
       type: 'ADD_TODO',
       text: 'Understand the flow.'
     }
    
     // Your reducer returns the next application state
     let nextState = todoApp(previousState, action)
     
4.Root Reducer 可能会把多个 reducer 输出的东西组合成一个状态树。
 Root Reducer 的结构完全取决于我们自己的设计。Redux 里面带个帮手函数：combineReducers()，你可以把 Root Reducer 分离成不同的 Reducer，每个 Reducer 都只管理属于它们自己的数据，然后用 combineReducers() 可以把这些分离出来的 Reducer 组合到一块儿，成为一个 Root Reducer。
   
 比如你有两个 Reducers，一个是为了任务列表，一个是为了当前选择的过滤器：
 
 
     function todos(state = [], action) {
       // Somehow calculate it...
       return nextState
     }
    
     function visibleTodoFilter(state = 'SHOW_ALL', action) {
       // Somehow calculate it...
       return nextState
     }
    
     let todoApp = combineReducers({
       todos,
       visibleTodoFilter
     })
执行了动作以后，combineReducers 返回的 todoApp 会同时调用两个 Reducers：

     let nextTodos = todos(state.todos, action)
     let nextVisibleTodoFilter = visibleTodoFilter(state.visibleTodoFilter, action)
     
然后会把这两个分支的结果组合成一个状态树：

     return {
       todos: nextTodos,
       visibleTodoFilter: nextVisibleTodoFilter
     }
     
5.Redux store 会把 root reducer 返回的状态树保存起来。
  这个新的状态树就是应用的下一个状态。所有 store.subscribe(listener) 注册的监听器会被执行。监听器可以使用 store.getState() 得到当前的状态。现在，UI 会根据新的状态被更新，如果把 React 与 Redux 放到一块儿用，这时候可以去调用  component.setState(newState) 。