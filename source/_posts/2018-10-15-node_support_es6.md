---
layout: post
title: "node支持ES6语法配置"
date: 2018-10-15 11:31
categories: node
tags: node es6
---



让node支持ES6语法的配置方案

## package.json

```
"babel-cli": "^6.26.0",
"babel-plugin-transform-runtime": "^6.23.0",
"babel-preset-es2015": "^6.24.1",
"babel-preset-stage-0": "^6.24.1",
"babel-runtime": "^6.26.0"
```

## .babelrc

* 根目录添加 babel 配置文件 .babelrc

```
{
    "presets": [
        "es2015",
        "stage-0"
    ],
    "plugins": [
        "transform-runtime"
    ]
}

```

## entry.js

* 在入口文件里引入

`require('babel-core/register');`
