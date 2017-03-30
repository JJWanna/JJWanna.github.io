---
title: React Flux架构
date: 2017-03-30 18:44:12
tags: React 、Flux
---

* Flux架构介绍
* React性能调优
* Redux介绍与实战
* GraphQL and Relay

### Flux架构介绍
* React :本身只涉及UI层，如果搭建大型应用，必须搭配一个前端框架。
* Flux :是一种架构思想，专门解决软件的结构问题。它跟MVC 架构是同一类东西，但是更加简单和清晰。
* View : 视图层
* Action（动作）: 视图层发出的消息（比如mouseClick）
* Dispatcher（派发器）: 用来接收Actions、执行回调函数
* Store（数据层）: 用来存放应用的状态，一旦发生变动，就提醒Views要更新页面
<!--more-->
![](/images/170330-11.png)
1.用户访问 View
2.View 发出用户的 Action
3.Dispatcher 收到 Action，要求 Store 进行相应的更新
4.Store 更新后，发出一个"change"事件
5.View 收到"change"事件后，更新页面
### React性能调优

    this.state.list.get("items").map(function(label) {
            return <div><CheckboxWithLabel onChange={this.onChange} ></CheckboxWithLabel></div>
    });
    
    onChange:function(){      
     this.setState({label:123})
    }
    
    shouldComponentUpdate: function(nextProps, nextState) {
         return nextProps.label !== this.props.label;
    }
    
    mixins: [React.addons.PureRenderMixin],
    constructor(props) {
         super(props);
         this.shouldComponentUpdate = React.addons.PureRenderMixin.shouldComponentUpdate.bind(this);
    }
    
  子组件的更新触发父组件。父组件更新全部子组件要Diff,一旦更改了state类型，上面全部办法歇菜。

### Redux介绍与实战    
    
* Flux存在多种实现（至少15种）Redux还算不错
* redux.min.js、react-redux.js、redux-thunk.min.js、keyMirror.js、immutable.min.js、reqwest.js（fectch）、ReduxThunk.js
* 管理应用的 state :
    * 通过 store.getState() 可以获取 state 
    * 通过 store.dispatch(action) 来触发 state 更新
    *  通过 store.subscribe(listener) 来注册 state 变化监听器
     * 通过 createStore(reducer, [initialState]) 创建
    
* Provider(ReactRedux)注入store <Provider store={store}<App/>  </Provider>
* Actions  JavaScript 普通对象 通过constants取到
* 对应 Actions Reducer 返回规律，更具体的是返回状态( Redux.combineReducers返回唯一的 Reducer)。
* Store(Redux.createStore(rootReducer,Redux.applyMiddleware(thunkMiddleware)))具体实施的载体
* components具体React的组件但是不涉及状态
* components->APP容器 react-redux 提供 connect 的方法 链接React组件和Redux类

### Redux架构
![](images/170330-22.png)
异步的Reducer
![](images/170330-33.png)

    import configureStore from './store/configureStore.es';
    import App from './containers/App.es';
    const Provider = ReactRedux.Provider;
    const store = configureStore();
    //通过store.getState()可以获取state
    //通过store.dispatch(action)来处罚state更新
    //通过store.subscribe(listener)来注册state变化监听器
    //通过createStore(reducer,[initialState])创建
    
    ReactDOM.render(
        <Provider store={store}>
            <App/>
        </Provider>,
        document.getElementById('JS-container')
    )
    
    
