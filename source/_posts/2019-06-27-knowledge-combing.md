---
title: 知识梳理一
date: 2019-06-27 10:36:10
tags: 
---

## 遍历

1. for循环
```javascript
const arr = [1, 2, 3];

for ( let i, len = arr.length; i < len; i++) {
  // 缓存arr长度，这一步新版本的浏览器已自行实现
}
```

2. forEach
es5的forEach，实际性能比for还差

```javascript
const arr = [1, 2, 3];

arr.forEach( ( value, index ) => {} )
```

* 不能break中断循环，且无法return

3. map
```javascript
const arr = [1, 2, 3];

arr.map( ( value, index ) => {} )
```

* 支持return

4. for-of
es6的for-of

```javascript
const arr = [1, 2, 3];

for( let index of arr ) {

}
```

* 支持数组，类数组对象，字符串
* 支持break, continue, return

5. for-in
遍历对象，可遍历数组但数组的下标会被转成字符串

```javascript
const obj = {
  key1: 'value1',
  key2: 'value2',
  key3: 'value3',
};

for( let key in obj ) {
  console.log(`obj.${key} = ${obj[key]}`)
}
```