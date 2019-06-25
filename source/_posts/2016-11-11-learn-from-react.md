---
layout: post
title: "react 回忆录二"
date: 2016-11-11 10:55
categories: react redux jsx
tags: react redux jsx 
---



革命性创新 性能出众 代码逻辑灰常简单的 React




## 什么是Redux
本身为一种架构，将Flux与函数式编程结合在一起

## 适用场景

1. 用户使用方式复杂
2. **不同身份**用户拥有*不同使用方式*
3. 多用户间可**协作**
4. 与*服务器*大量交互，亦或使用*WebSocket*
5. View需从**多个来源**获取数据
6. 某组件的*状态*需**共享**
7. 某状态需在*任何地方*都可拿到
8. 单个组件需改变**全局状态**
9. 一个组件需改变*另一个*组件状态

## 设计思想

- Web App是一个状态机，视图和状态是一一对应的
- 所有状态保存至一个对象里面

## API

### Store

用来保存所有数据的容器，整个App只能有一个，由 `createStore(fn)` 来生成，接受一个Reducer函数作为参数，即后面所说到的Reducer，一个用来根据Action的不同而生成此刻的State快照的函数  

```
import { createStore } from 'redux';
const store = createStore(fn);
```

### State

当想从Store里获得**某时间点**的数据，可对Store生成快照，而这种时间点下的数据集合称为**State**  

而state储存在一棵对象树state tree中，且这棵树只存在于唯一的store中  

当前时刻的State可由 `store.getState()` 获得  

State与View一一对应，即State相同View就相同  

State本身是**只读**的，而改变它的方法就是触发action  

### Action

本身为一个描述已发生事件的对象，接受的属性有**type**(必选 表示Action名称)和其它自定义属性  

因State的变化需由View来导致，而Action为View发出的**通知**，告诉State应有所变化了  

Action描述当前所发生的事情，它是改变State的唯一办法，它会运送数据至Store  

Action是把数据从应用传到store的有效载荷，是store数据的唯一来源，一般经 `store.dispatch()` 传action到store  

```
const action = {
    type: 'ADD_TODO', // 名称
    payload: 'Learn Redux' // 携带信息
};
```

可封装一个生成Action的函数，只需提供Action的名称即可  

```
// Action Creator
const ADD_TODO = '添加 TODO';
function addTodo(text){
    return {
        tyep: ADD_TODO,
        text
    }
}
const action = addTodo('Learn Redux');
```

#### dispatch()

`store.dispatch()`  是View发出Action的唯一办法，接受Action对象作为参数  

```
import { createStore } from 'redux';
const store = createStore(fn);
store.dispatch(addTodo('Learn redux'));
```

#### Reducer

当Store收到Action时，Store需给出一个新的State快照，从而View才会变化，整个一个过程称为Reducer，它接受Action和此刻的State快照作为参数，并返回一个新的State  

即 `(previousState, action) => newState`  

简单来说，就是为了描述action是如何改变state tree的  

```
const defaultState = 0;
const reducer = (state = defaultState, action) => {
    switch (action.type) {
        case 'ADD':
            return state + action.payload;
        default:
            return state;
    }
}
const state = reducer(1, {
    type: 'ADD',
    payload: 2
})
```

这里的Reducer需手动调用，而当把它作为参数传入 `createStore(reducer)`，可实现每当 `store.dispatch()` 发来一个新Action时，自动调用Reducer并得到新State  

##### Reducer拆分

面对所有reducers最终只会生成唯一的state对象，这意味着该state包含所有数据，导致本身体积十分庞大，进而有了对reducers的拆分  

拆分后，单独的reducer可独立操作state tree中某个state，可控制它们被调用的顺序，传入附加数据，编写可复用的reducer来处理一些通用任务  

```
const chatReducer = (state = defaultState, action = {}) => {
    /*
     * 假设 action = {type: 'add', payload: 'str'};
     * 则声明 const { type, payload } = action;
     * 调用type返回'add'，调用payload返回'str'
     */
    const { type, payload } = action; // 解构赋值
    switch(type){
        case 'ADD_CHAT':
            return Object.assign({}, state, {
                    // Object.assign({}, {}, ...) 合并多个对象
                    // 遇相同属性时 位于后面的属性覆盖前面的
                    chatLog: state.chatLog.concat(payload) // str.concat(str) 拼接指定字符串
                });
        case 'CHANGE_STATUS':
            return Object.assign({}, state, {
                    statusMessage: payload
                });
        default:
            return state;
    }
}
```

可看出上述代码，有两种Action分别改变State的两个属性  

- ADD_CHAT => chatLog属性
- CHANGE_STATUS => statusMessage属性

把上述各个reducer进行拆分  

```
// 主reducer
const chatReducer = (state = defaultState, action = {}) => {
    return {
        // 子reducer
        chatLog: chatLog(state.chatLog, action),
        statusMessage: statusMessage(state.statusMessage, action)
    }
}
```

每个子reducer分别处理全局state中的一部分，各自的state参数都是不同的，分别对应它管理的那部分state数据  

再由 `combineReducers()` 来合并reducer成一个大的Reducer  

```
import { combineReducers } from 'redux';
const chatReducer = combineReducers({
    chatLog,
    statusMessage
})
export default todoApp;
```

##### reduce()

当一组actions为数组时，存储着多个Action对象，此时可调用 `actions.reduce(reducer, 0)` ，reducer作为参数，这样可实现同时处理一个actions数组的Action对象，并得到最终结果  

#### subscribe()

`store.subscribe(listener)`  Store设置监听函数，可实现一旦store的快照state发生变化后，就会自动执行这个函数  

用于把组件的render方法亦或setState方法作为listener监听对象，state变化的同时自动渲染View页面  

```
let unsubscribe = store.subscribe(() => {
    console.log(store.getState());
});
unsubscribe(); //直接调用能解除监听
```

## Redux工作流程

![结构图1](http://img.blog.csdn.net/20151210233944838)

![结构图2](http://img.blog.csdn.net/20151210234529139)

1. 用户与View交互发出Action
2. `store.dispatch(Reducer(state, action))` store经 `store.dispatch()` 向View发送state，而Action和此刻state作为参数传入Reducer，Reducer处理后返回新的state，发送给View
3. 一旦State有新变化，Store调用监听 `store.subscribe(listener)` ，侦听器listener可能是 `store.getState()` 获取此刻新state，亦或通知经组件state有变化渲染View