---
title: require
date: 2019-06-25 15:20:21
tags: javascript require
---

## require.ensure()

* 代码分隔 基于webpack，针对体积大的模块单独导出js文件，用于异步调用，可结合同步代码
* 用法：require.ensure( [], fn(), '' ) 
参数1为异步函数的依赖
参数2为回调, 异步调用的函数写于这里
参数3为chunk命名

```javascript
// 异步按需加载的方式导出swiper
export default function getSwiper() {
  return new Promise(
    res => {
      require.ensure(
        [],
        require => {
          res(require('xxx/swiper'));
        },
        'chunck-swiper'
      );
    }
  );
}
```