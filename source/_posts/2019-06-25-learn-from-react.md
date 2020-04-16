---
title: react 开发记录一
date: 2019-06-25 15:01:28
tags: react
---

## exports && import

1. module exports export import
由于nodejs采用CommonJS模块规范，其中module表示一个对象变量，本身的exports属性是对外接口。因node为每个模块提供一个exports变量，所以有两种模块导出方式：`module.exports = { obj }` 和 `exports.obj = { obj }`；
而ES6的export和export default，导出对外接口以提供给import导入，需要注意的是：
```javascript
// export-index.js
export const var = 1;

//  import-index.js
import { var } from ‘export-index’;
```
由export直接导出的，import导入必须是对象且需知变量名

```javascript
// export-index.js
const var = 1;
export default var;

//  import-index.js
import _var from ‘export-index’;
```
而由export default导出的，import导入可以是自定义变量名


## class A extends B {}
```javascript
class A extends B {
	constructor(params) {
		/*
			super指向父类B的构造函数
			但super内部的this指向A
		*/
		super(params); // B.prototype.constructor.call(this, params)
	}
}
```

## 组件间的引用

当子组件调用父组件函数时，

```javascript

// Parent
import Child from './child.jsx';

class Parent extends React.Component {
	constructor(props) {
		super(props);

		this.state = {}
	}

	handleFn = () => {
		// 这里使用箭头函数，当子组件调用时
		// 可以获得当前作用域的this
		this.setState({});
	}

	render() {
		return <Child handleFn={handleFn} />
	}
}

// Child
export default class Child extends React.Component {
	constructor(props) {
		super(props);
	}

	render() {
		const { handleFn } = this.props;

		return (
			<div onClick={ () => handleFn() } />
		);
	}
}
```

## react中的注释：

1.单行注释– 
{/* 注释内容 */} 

2.多行注释– 
/* 
注释1 
注释2 
*/ 
ps：切记不能用 `<!-- 注释 -->`

## react 生命周期

```javascript
constructor(props) { 
	// 只调用一次
	super(props); // 必须存在 
} // 初始化
↓
componentWillMount() { 
	// 只调用一次，此时可修改state
} // 组件即将挂载
↓
// 第一次 render() {}
↓
componentDidMount() {
	只调用一次，此时真实的DOM已被渲染出来
} // 组件完成挂载
↓
componentWillReceiveProps(nextProps) {
	// 组件接收到新的props
	// 父组件发生render时，子组件调用
	// 可用于组件间通信，父组件作为中间层
} // 组件即将接收父组件值
↓
shouldComponentUpdate(nextProps, nextState) {
	// 组件是否应当渲染新的props和state
} // 返回false不执行下一个生命周期
↓
componentWillUpdate(nextProps, nextState) {
	// 接收到新的props和state之后，渲染之前
} // 组件即将更新
↓
// 第二次 render() {}
↓
componentDidUpdate(prevProps, prevState) {
	// 完成新的props和state更新，渲染之后调用
	// 此时可访问真实DOM
} // 组件完成更新
↓
componentWillUnmount() {
	// 组件被移除前调用
} // 组件卸载

```

* 在render阶段，只能经 this.prop 和 this.state 访问数据，不能改变状态，不能修改DOM输出

## react 指定位置render

```javascript
class ChildComponent extend React.Component {}

function close() {
  let div = document.createElement('childClass');
  
  if ( div ) {
    ReactDOM.unmountComponentAtNode(div);
    div.parentNode.removeChild(div);
  }
}

export default function() {
  let div = document.createElement('childClass');
  
  if ( !div ) {
    div = document.createElement('div');
    div.className = 'childClass';
    document.body.appendChild(div);
  }

  ReactDOM.render(<ChildComponent />, div);
}

```

# 性能篇
1. 使用 pureComponent
内部处理的是浅比较，应用于业务场景，数据变化不复杂的情况

2. defaultProps 初始化组件属性值
引入 ``, 避免组件重复渲染

3. 展示组件 / 容器组件
抽离业务逻辑至容器组件，纯 UI 层面放置展示组件

```javascript
import Layout from './Layout'; // 展示组件

export default class LayoutContainer extends React.Component {
	// ...业务逻辑 fn
	render() {
		return <Layout />;
	}
}
```

导出接受展示组件的函数

```javascript
export default function(Component) {
	return class Container extends React.Component {
		render() {
			return <Component />;
		}
	}
}

```