---
layout: post
title: "javascript DOM高级程序设计 学习笔记"
date: 2016-08-30 18:37
categories: note javascript
tags: node对象
---



css三本经典书籍之一，《精通CSS：高级Web标准解决方案》




## html结构设计

- 元素名的设计  
元素名即ID或类名的设计，尽可能保持名称与其表现方式无关，应根据该元素是**用来干什么**来为元素命名，而不是根据其的*外观如何*来命名，建议用**连字符**胜于驼峰写法，`andy-budd  andyBudd`  

ID与类名的选择，应根据该元素是否在页面只会出现唯一的一次则使用ID，否则选择类名来命名那些相似的且可出现在多个位置的元素  

"多类问题"：应简化结构，某部分内容整体应减少类名的出现，使用div标签来分组这部分内容并为其命名类名，分组内容避免使用类名  

"多div问题"  应只在没有现有元素能够实现区域分割的情况下才使用div，根据*条目的意义或功能*用div来分组，一般div针对*块级元素*进行分组  
同样，span标签可对*行内元素*进行分组或标识

## 属性选择器

```
div[title='box']{
  
}
```

```
a[href]:hover, a[title].focus{
  
}
```

```
a[rel~='co-worker']{
  <!-- 匹配条件为 -->
  <!-- 有rel属性且值包含co-worker关键字的a标签 -->

}
```

## 样式
- 盒模型的元素框总尺寸由内容区域、内外边距、边框的大小所构成，其中**内容区域**的宽高由width、height设置  

- 外边距叠加  相邻两个盒模型的外边距会叠加，最终的*外边距值*取两叠加值的最大值  
- 行内框  修改行内框尺寸唯一办法是修改其行高或**水平**边框、内外边距  
可为行内元素设置 `display: inline-block;` 使行内元素依旧保持水平排列，但也能像块级元素一样设置宽高、垂直的内外边距  

- 清除浮动  
当为元素设置浮动时，周边的元素可能会受其影响，如周边元素会*向浮动元素靠拢*，此时可对周边元素设置 `clear: both;` 来清除浮动元素对其的影响，而 `clear: both;` 实际上是对周边元素**增加合适的外边距**，使其远离浮动元素  

如下代码应用：  

```
.new{
  background-color: gray;
  border: 1px solid black;
}

.new img{
  float: left;
}

.new p{
  float: right;
}

.new .clearfloat{
  clear: both;
}
<!-- 第三种方案 -->
.clear:after{
  height: 0;
  content: '.';
  display: block;
  visibility: hidden;
  clear: both;
}

<div class="news clear">
  <img src="xxx.png" alt="my-pic">
  <p>Some text</p>
  <div class="clearfloat"></div>
</div>
```

上述代码会出现的问题时，.new父级的子级img和p元素都为浮动，脱离的文档流，.new将不能包围它们，解决方案有：  
1. 可在子级中在加入 `clear: both;` 的元素，即在html*额外***添加空标签div**并添其样式 `clear: both;`  
2. 向父级添加 `overflow: hidden;` 或 `overflow: auto;` 样式，但会在某些情况下截断内容或产生滚动条  
3. (兼容性高) 向父级增加类名，并为该类名添加伪元素 `:after` ，样式为：  

```
.clear:after{
  height: 0;
  content: '.';
  display: block;
  visibility: hidden;
  clear: both;
}
```

- 多背景  
在CSS3下支持一个元素拥有多个背景，如：  

```
.box{
      width: 20em;
      background: url(xxx.gif) no-repeat top left,
            url(xxx.gif) no-repeat top right,
            url(.xxx.gif) no-repeat bottom left,
            url(xxx.gif) no-repeat bottom right;
    }
```

- border-image  
border-image有六大属性值，分别是：  
1. 图片地址border-image-source，值为url(xxx.gif)  
2. 图片切片border-image-slice，值只允许数字及百分数，简写类似margin  
3. 图片宽度border-image-width，值类似border-width控制边框宽  
4. 图片外凸border-image-outset，值可影响边框距中间内容区域的距离  
5. 图片重复border-image-repeat，值有repeat、round、stretch  

简写规则：border-image: 地址 剪裁 /边框宽/(离中间区域距离)可选 (拉伸)可选;  

```
.border-image{
  border-image: url(xxx.gif) 25% 25px/25px round;
}
```

- 不透明度  
1. 直接作用到元素  `opacity: .8`，`filter: alpha(opacity=80)` (兼容IE)  
2. 直接作用到元素背景  `background-color: rgba(0, 0, 0, .8);`，这里的0.8指不透明度，即20%透明度  
3. 使用PNG透明背景图像  `filter: progid:DXImageTransform.Microsoft.AlphaImageLoader(src="xxx.png", sizingMethod="crop");` (兼容IE)  

- 链接目标设样式  
同一个页面中，由a标签跳转#id 如 `<a href="#id"></a> <div class="cont" id="id"></div>`，点击a标签跳到#id标签的内容，可设 `.cont:target { background: red;}`，让每个不同id但相同类名的div标签作用样式  

- slc[attr=val]  
属性选择器，修改指定属性值val的某标签slc，如a[href="http:"]  
同样，slc[attr^=val]修改指定以属性值val开头的某标签slc  
slc[attr$=val]修改指定以属性值val结尾的某标签slc

- 