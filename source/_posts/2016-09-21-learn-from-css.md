---
layout: post
title: "CSS权威指南 学习笔记"
date: 2016-09-21 13:10
categories: css
tags: css
---



css三本经典书籍之一，《CSS权威指南》




## 表布局
使用 `display: table;` 来为非表元素的标签和添加语义标签来定义表结构，如：  

```
<style>
  scores{
    display: table; // 表格显示
  }
  headers{
    display: table-header-group; // 表格标题行组(列标题)
  }
  game{
    display: table-row-group; // 一个或多个行的组
  }
  team{
    display: table-row; // 单元格构成的行
  }
  label, name, score{
    display: table-cell; // 单个单元格
  }
</style>

<body>
  <scores>
    <headers>
      <label>Team</label>
      <label>Score</label>
    </headers>
    <game sport="MLB" league="NL">
      <team>
        <name>Reds</name>
        <score>8</score>
      </team>
      <team>
        <name>Reds</name>
        <score>8</score>
      </team>
      <team>
        <name>Reds</name>
        <score>8</score>
      </team>
    </game>
  </scores>
</body>
```

该注意的是，当没为以table-cell显示的元素设置以table-row显示的包含块时，会自动在加上**table-row的匿名对象**，同理，在一个table-row-group、table-header-group、table-footer-group等子元素的父元素不是table的话，会在该子元素其父级间插入**匿名table元素**  
以此类推，根据表格的结构，哪个位置需放怎样的元素，没有的话，自动生成对应的元素插入元素之间

### 表标题
假设页面有自定义标签<caption>，设置其样式为 `caption{caption-side: top;}` ，caotion-side来指明标题在表格中的位置  

## 列表
- list-style-type  列表标志，如有序列表ol的标志1. 2. 3. ...等等
- list-style-image  为列表指定标志图像  

```
ul{
  list-style-type: square; // 当图像失效时，标志为方块
  list-style-image: url(xxx.gif);
}
ul ul{
  // 嵌套列表
  // 该属性可继承
  list-style-image: none; // 设为none将不显示xxx.gif 而继承了square
}
```

- list-style-position  列表标志位置，值为inside会使标志靠向列表内容
- 简写，`list-style: url(xxx.gif) square inside;`

## 生成内容
:before与:after 这两伪元素根据其**content属性**来把生成内容放置在元素的前或后  

`a[href]:before{ content: "(link) ";}`  
插入图像：`a.pdf-doc:after{ content: url(xxx.gif);}`  

该注意的是：当这两伪元素的主体**为块级**时，其display属性只能为none、inline、block，其余值都处理为block; 而主体**为行内**时，display为none或inline，其余为inline  

### 插入属性值
`a[href]:after { content: " [" attr(href) "] ";}`  
attr()来获取其主体的属性值，为了避免冲突，以串值来表示  

## 鼠标光标
`cursor: pointer;`显示*手指*光标  
`cursor: text;`显示*"I"光标*选择文本  
`cursor: move;`显示类似*十字样*光标表示可拖动 
`cursor: progress;`显示类似*沙漏样*光标表示等待  
`cursor: help;`显示类似*箭头右下标问号*光标，当其主体有title属性时，光标经过会显示title的内容 ...