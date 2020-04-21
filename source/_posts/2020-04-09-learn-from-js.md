---
title: 各种应用工具类实现
date: 2020-04-09 16:07:43
tags:
---

## 深浅拷贝

### 浅拷贝

```javascript
let copy1 = { ...{x: 1} }; // ...{} 解构实现

let copy2  = Object.assign({}, {x: 1}); // object.assign 对象合并实现
```

### 深拷贝

```javascript
let obj = {a: 1, b: {x: 3}};
JSON.parse(JSON.stringify(obj)); // JSON.stringify 序列化对象成字符串再转对象

// 递归处理
function deepCopy(obj) {
  let copy = obj instanceof Array ? [] : {};

  for ( let k in obj ) {
    if ( obj.hasOwnProperty(k) ) {
      copy[k] = typeof obj[k] === 'object' ? deepCopy(obj[k]) : obj[k];
    }
  }

  return copy;
}
```

## 模拟setInterval

```javascript
setTimeout( function () { // 这里不能用箭头函数，否则拿不到 arguments
  // func
  setTimeout( arguments.callee, 500);
}, 500);
```

## Event Bus

```javascript
class EventBus {
  constructor() {
    this.events = this.events || new Map();
  }

  addListener( type, fn ) {
    if ( !this.events.get(type) ) {
      this.events.set(type, fn);
    }
  }

  emit( type ) {
    let handle = this.events.get(type);

    handle.apply(this, [...arguments].slice(1));
  }
}

let emitter = new EventBus;

emitter.addListener('event', function() {
  console.log(...arguments);
});
emitter.emit('event', '1', '2');
```

## 双向绑定
简易版主要理解实现核心，getter() & setter()

```javascript
let obj = {};

const input = document.getElementById('input');
const html = document.getElementById('html');

Object.defineProperty(obj, 'text', {
  configurable: true, // 可配置，如对对象key，value进行配置
  enumerable: true, // 可枚举，如对被循环遍历
  get() {
    console.log('get data');
  },
  set( newValue ) {
    input.value = newValue;
    html.innerHTML = newValue;
  }
});

input.addEventListener( 'keyup', e => {
  obj.text = e.target.value;
} );
```

## 滚动更新

所参与计算的需要的变量值：
1. 可视窗口大小：
```javascript
// 标准浏览器及IE9+ || 标准浏览器及低版本IE || 低版本混杂模式
// jq: $(window).height()
window.innerHeight || document.documentElement.clientHeight || document.body.clientHeight
```
2. 窗口顶部与文档顶部间的距离：
```javascript
// jq: $(document).scrollTop();
window.pageYOffset || document.documentElement.scrollTop || document.body.scrollTop
```
3. 元素顶部与文档顶部间的距离：
```javascript
// jq: $(el).offset().top
function getElTop(el, initValue = 0) {
  let _top = el.offsetTop + initValue;
  const parent = el.offsetParent;

  if ( parent !== null ) {
    _top += parent.clientTop;
    getElTop(parent, _top); // 尾递归
  } else return _top;
}

// 循环实现
function getElTopLoop(el) {
  let _top = el.offsetTop;
  let parent = el.offsetParent;

  while( parent !== null ) {
    _top += parent.offsetTop + parent.clientTop;
    parent = parent.offsetParent;
  }

  return _top;
}

// getBoundingClientRect()
const EL = document.getElementById('#el');
EL.getBoundingClientRect().top;
```

### 场景：判断元素是否进入视区

1. 方式一：根据元素到文档顶部的距离是否小于当前视口相对于文档顶部的距离

```javascript
function isInSight(el) {
  const clientHeight = window.innerHeight || document.documentElement.clientHeight || document.body.clientHeight;
  const scrollTop = window.pageYOffset || document.documentElement.scrollTop || document.body.scrollTop;
  const scrollHeight = clientHeight + scrollTop;

  return getElTop(el) < scrollHeight;
}
function loadImg(el) {
  if ( !el.src ) {
    el.src = el.
  }
}
function checkImgs() {
  const imgs = document.getElementsByTagName('img');

  Array.from(imgs).forEach( img => {
    if ( isInSight(img) ) {
      loadImg(el);
    }
  } );
}

window.addEventListener('scroll', checkImgs, false);
window.onload = checkImgs;
```