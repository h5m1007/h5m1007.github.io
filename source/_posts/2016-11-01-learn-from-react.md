---
layout: post
title: "react 回忆录一"
date: 2016-11-01 10:55
categories: react jsx
tags: react jsx 
---



革命性创新 性能出众 代码逻辑灰常简单的 React




## 什么是React
React 起源于Facebook的内部项目，项目本身也越滚越大，从最早的UI引擎变成了一整套前后端通吃的 Web App 解决方案

## 怎么玩
引入三大法宝： `react.js`、`react-dom.js`、`browser.js`  

`react.js` 是 React 的核心库  
`react-dom.js` 是提供与 DOM 相关的功能  
`browser.js` 的作用是将 JSX 语法转为 JavaScript 语法  

- JSX
允许 HTML 与 JavaScript 的混写，遇到 HTML 标签（以 < 开头），就用 HTML 规则解析；遇到代码块（以 { 开头），就用 JavaScript 规则解析

## 方法

### 应用

```
<!-- ... -->
<div id="example"></div>
<script type="text/babel">
    var names = ['Leon', 'Leo'],
        arr = [
            <h1>React is awesome</h1>,
            <h2>good app!</h2>
        ],
        Msg = React.createClass({
            render: function(){
                return <h1>Hello, {this.props.name}</h1>
            }
        });
    ReactDOM.render(
        // <h1>Hello world!</h1>,
        // <div>
        //  {
        //      names.map(function(name){
        //          return <div>Hello, {name}!</div>
        //      })
        //  }
        // </div>,
        // <div>{arr}</div>,
        <Msg name="Leon"/>,
        document.getElementById('example')
    );
</script>
```

- 分析

- `ReactDOM.render("HTML", "DOM")`  基本法，将模版转为HTML语言，并插入指定节点  
- `<div>{arr}</div>`  变量是一个数组，结果 JSX 会把它的所有成员，添加到模板

- 组件

**组件**并不是真实的 DOM 节点，而是存在于内存之中的一种数据结构，叫做**虚拟 DOM** （virtual DOM）。只有当它插入文档以后，才会变成真实的 DOM

- DOM diff 算法

DOM的所有变动，都先在**虚拟 DOM** 上发生，然后再将实际发生变动的部分，反映在真实 DOM上，这种算法叫做 **DOM diff** ，它可以极大提高网页的性能表现

- `React.createClass({})`  用于生成一个组件类，如 `Msg`，模板插入 <Msg /> 时，会自动生成 Msg 的一个实例  

- 组件注意事项： 

组件类的第一个字母必须大写;  
只能包含一个顶层标签;  
组件的属性可以在组件类的 `this.props` 对象上获取; 
`class` 属性需要写成 `className` ，`for` 属性需要写成 `htmlFor`  

- 组件参数

- 组件属性  

`this.props` 表示那些一旦定义，就不再改变的特性  

```
<script>
    // ...
    Msg = React.createClass({
        // 判断传入参数是否符合要求
        propTypes: {
            // 限制参数为字符串
            title: React.PropTypes.string.isRequired
        },
        // 设置属性默认值
        getDefaultProps: function(){
            return {
                title: "Hello Leon"
            }
        },
        render: function(){
            return <h1>Hello, {this.props.name}</h1>
        }
    });
    // ...
</script>
```

- 组件状态

组件看成是一个**状态机**，一开始有一个初始状态，然后用户互动，导致状态变化，从而触发重新渲染 UI  

`this.state` 是会*随着用户互动而产生变化*的特性  

所以，该属性一般在应用在表单比较多  

```
<script>
    // ...
    Slt_btn = React.createClass({
        // 定义初始状态(对象)
        getInitialState: function(){
            return {
                opt: false
            }
        },
        clickSlt: function(){
            this.setState({
                /**
                 * 修改状态值
                 * 每次修改以后
                 * 自动调用 this.render 方法再次渲染组件
                 */
                opt: !this.state.opt
            });
        },
        render: function(){
            var text = this.state.opt ? "select" : "don't select";
            return(
                <p onClick={this.clickSlt}>
                    You {text} this. Click to toggle!
                </p>
            )
        }
    });
    // ...
</script>
```

- 组件生命周期

分别有：  

1. Mounting：已插入真实 DOM
2. Updating：正在被重新渲染
3. Unmounting：已移出真实 DOM

三种状态提供五种处理函数：  

1. `componentWillMount()`  组件**载入之前**调用
2. `componentDidMount()`  组件**载入之后**调用
3. `componentWillUpdate()`  组件**更新之前**调用
4. `componentDidUpdate()`  组件**更新之后**调用
5. `componentWillUnmount()`  组件**卸载之前**调用


