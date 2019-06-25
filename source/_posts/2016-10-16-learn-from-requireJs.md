---
layout: post
title: "require.js 学习笔记"
date: 2016-10-16 15:37
categories: javascript requireJs
tags: requireJs 按需加载 模块化
---



神奇的模块加载引擎，RequireJs。




## 什么是RequireJs  
RequireJs致力于代码的模块化，使用了不同于传统`<script>`标签的脚本加载步骤，可用来加速并优化代码。  

## 解决的问题  
1. 实现js文件的异步加载，避免网页失去响应
2. 管理模块之间的依赖性，便于代码的编写和维护  

## 如何使用  
1. 在模版文档如名为template.html，来加载各模块，模版文档html的head位置，加载requireJs  

```
<script src="require.js" data-main="main" defer async="true"></script>
```

其中，data-main指明主模块js即所有代码的入口，首先被require.js加载，.js后缀省略，而async设为true指明该js文件异步加载，来避免网页失去响应，defer一样指明异步加载，只是这属性IE支持。  

2. 主模块main.js的写法，如  

```
<script>
    require.config({
        baseUrl: "js", // 可配置根目录(可选)
        paths: {
            // 配置各模块路径(可选)
            "jquery": "jquery.min", // 命名该模块并指明路径，.js后缀省略
            "text": "text", // 插件text用来解析html文档
            "domReady": "domReady", // 插件domReady可让回调函数在页面DOM结构加载完成后再运行
            ... // 其它模块路径
        },
        // 加载非AMD规范的模块
        // 使用shim来定义它们的特征
        shim: {
            // shim属性用来配置不兼容的模块
            "jquery.scroll": {
                deps: ["jquery"], // deps接受数组，标明该模块的依赖性
                exports: "jQuery.fn.scroll" // 标明该模块外部调用时的名称
            }
        }

    })
</script>
```

3. 其它模块采用AMD规范，即使用define()函数来定义，当该模块依赖其它模块时，define()接受的第一个参数为依赖的其它模块，同时必须是以数组的形式，如  

```
<script>
    // model1
    define(function(){
        // 注意写法，函数命名最后返回该函数
        var add = function(){
            console.log("model 1");
        }

        return {
            add: add
        }

        // 或者
        return add;
    })

    // model2
    define(["model1"], function(model1){
        model1.add;

        // 或者
        model1();
    })
</script>
```

