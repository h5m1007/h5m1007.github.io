---
title: learn-from-js
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

### 模拟setInterval

```javascript
setTimeout( function () { // 这里不能用箭头函数，否则拿不到 arguments
  // func
  setTimeout( arguments.callee, 500);
}, 500);
```

### Event Bus

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

### 双向绑定
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