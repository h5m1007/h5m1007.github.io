---
layout: post
title: "对比Less和Sass"
date: 2016-10-01 15:37
categories: Less Sass
tags: Less Sass
---



强大的两款CSS预处理器：Less 和 Sass




## Less与Sass的博弈
在选择**Less**或者**Sass**时，可以考虑下自己的身份，是一名后端猿还是前端猿  

Sass更适合后端猿，因它是基于**Ruby**在服务器端处理  
Less适合前端猿，因它是基于Javascript，从而它需*额外的时间*来编译处理.less文件，再输出.css文件，供客户端使用  

> 分别从8个方面对比Less和Sass

1. 混入(mixins) --> class中的class
2. 参数混入 --> 传参的class，类似于函数
3. 嵌套规则 --> class嵌套class "DRY"
4. 运算 --> css的数学
5. 颜色功能 --> 编辑颜色
6. 命名空间 --> 分组样式
7. 作用域 --> 局部修改css
8. js赋值 --> css利用js赋值

- 变量

Less使用"@"来声明，如 `@color`  
Sass使用"$"来声明，如 `$color`  

- mixins

Less用法  

```
<style>
    .border {
        border-top: 1px dotted #333;
    }
    article.post {
        background: #eee;
        .border; /*直接调用类名*/
    }
    ul.menu {
        background: #ccc;
        .border;
    }
</style>
```

Sass用法  

```
<style>
    @mixin border {
        /*由@mixin声明*/
        border-top: 1px dotted #333;
    }
    article.post {
        background: #eee;
        @include border; /*由@include调用*/
    }
    ul.menu {
        background: #ccc;
        @include border;
    }
</style>
```

- 参数混入

Less用法  

```
<style>
    .border-radius(@radius: 3px) {
        -webkit-border-radius: @radius;
        -moz-border-radius: @radius;
        border-radius: @radius;
    }
</style>
```

Sass用法  

```
<style>
    @mixin transform($value) {
      -webkit-transform: $value;
      -moz-transform: $value;
      transform: $value;
    }
</style>
```

- 选择器继承

Less不提供

Sass用法  

```
<style>
    .menu{
        border: 1px solid #ddd;
    }
    .footer{
        @extend .menu;
    }
</style>
```

- 嵌套规则

Less与Sass类似用法  

```
<style>
    #site-body { …
        .post { …
            .post-header { …
                h2 { … }
                a { …
                    &:visited { … }
                    &::hover { … }
                }
            }
        }
    }
</style>
```

- 运算
不常用  
...占位

- 颜色函数

```
<style>
    @blue: #369;
    .submit {
        padding: 5px 10px;
        border: 1px solid @blue;
        /*...兼容方案...*/
        background: linear-gradient(top, lighten(@blue, 10%) 0%, @blue 100%); /*W3C*/
        color: #fff;
        text-shadow: 0 -1px 1px rgba(0,0,0,0.4);
    }
</style>
```

*`lighten()` 函数用百分比值来减轻颜色*  

**外传** `linear-gradient()`  渐变属性  

`linear-gradient( [<point> || <angle>,]? <stop>, <stop> [, <stop>]* )`  

![线性渐变](http://www.w3cplus.com/sites/default/files/moz-gradient.png?_=2882724)  

参数：其共有三个参数，第一个参数表示线性渐变的方向，top 是从上到下、left 是从左到右，如果定义成 left top，那就是从左上角到右下角。第二个和第三个参数分别是起点颜色和终点颜色  

- 条件语句

Less不支持  

Sass用法  

```
<style>
   @if $support-legacy {
      // ...
    } @else {
      // ...
    }

    @for $i from 1 through 10 {
      .foo:nth-of-type(#{$i}) {
        border-color: hsl($i * 36, 50%, 50%);
      }
    }
</style>
```

- 命名空间

将一些公用的样式分组，然后在用的时候直接使用  

Less用法  

```
<style>
    #defaults {
        .nav_list () { /* 注意写法 () */
            list-style: none;
            margin: 0; padding: 0;
        }
        /*...*/
    }
    nav ul {
        #defaults > .nav_list; /* 注意调用方法 > */
    }
</style>
```

Sass用法  

CSS部分属性遵循同样的命名空间，如background-color、font-size、padding-top，等等，分别在各自的命名空间中，如background命名空间、font命名空间、padding命名空间  

```
<style>
    .funky {
      font: {
        family: fantasy;
        size: 30em;
        weight: bold;
      }
    }
    /*转译为CSS如下*/
    .funky {
      font-family: fantasy;
      font-size: 30em;
      font-weight: bold;
    }
</style>
```

- 作用域

Less用法

```
<style>
    @color: #00c; /* 蓝色 */
    #header {
        /* 改变了变量color 只在#header作用域有效 */
        @color: #c00; /* red */
        border: 1px solid @color; /* 红色边框 */
    }
    #footer {
        /*变量依旧是原始值 #00c */
        border: 1px solid @color; /* 蓝色边框 */
    }
</style>
```

Sass用法  

```
<style>
    $color: #00c; /* 蓝色 */
    #header {
        /* 改变了变量color 在#header以后的作用域下都有效 */
        $color: #c00; /* red */
        border: 1px solid $color; /* 红色边框 */
    }
    #footer {
        /*变量继承#header作用域下的$color值#c00 */
        border: 1px solid $color; /* 红色边框 */
    }
</style>
```

