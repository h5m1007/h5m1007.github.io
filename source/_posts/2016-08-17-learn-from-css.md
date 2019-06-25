---
layout: post
title: "CSS权威指南 学习笔记"
date: 2016-08-17 13:00
categories: css
tags: css
---



css三本经典书籍之一，《CSS权威指南》




## 文本样式
- text-indent  首行缩进，只对块级元素(如p标签)起作用  
  正负值和百分数值都有效，当设置负值时注意得**再设置边距**以免超出浏览器边界  
  当值为百分数值时，它会继承其父元素宽度  
- line-height  行高，其计算方式如： 
 
```
p{
    font-size: 14px;
    line-height: 18px;
}
```

  该设置会得到一个18px高的行内框，由(18-14)/2=2得到上下**行间距**2px  
所以行间距计算公式：(line-height) - (font-size) 得到总的行间距，当得到负值时，上下两行会重叠一起  
当设置值为缩放倍数(因子)时，如  

```
div{
    line-height: 1;
}
div > p{
    font-size: 14px;
}
```  

  在此p标签会继承div标签的line-height，得到line-height为 1 * 14 = 14px  

- vertical-align  垂直对齐，只作用于行内元素或替换元素如img、form标签    
当其值设置为baseline即基线对齐时，那么该元素底端会与其父元素基线对齐  
当设置为super或sub即上标或下标时，该元素会在父元素基线上升高或降低位置  
当设置为百分数值时，会相对于父元素的line-height进行计算  

- word-spacing  字(单词或文字)之间的距离  
- letter-spacing  每个字母之间的距离  
- text-transform  文本大小写，当值为capitalize时，设置每个单词首字母大写;值为uppercase和lowercase分别转换为大写和小写  
- text-decoration  文本装饰  值有underline、overline或line-through上下划线或贯穿线  
- white-space  处理空白符，值为normal默认把空白符合并成一个空格，值为nowrap禁止文本换行  

## 布局
- 水平格式化：设置宽度(元素内容宽)、边框、内外边距，width、border-left、border-right、padding-left、padding-right、margin-left、margin-right即共7个属性  
这7个属性值加起来必须是**元素包含块**(即父级元素)的宽度，即块元素的**父元素**的width  
  其中，只有width、margin-left、margin-right可设置为auto，当其父级width为固定值时，这三个属性任一为auto补全值至父级width，如：  

```
div{
    width: 400px;
}
div p{
    width: 100px;
    margin-left: auto; // auto: 400 - 100 * 2 = 200px
    margin-right: 100px;
}
```

  又这三个值都设置为固定值，但三个值加起来未等于父级width，那么margin-right会被补全使总值等于父级width  

```
div{
    width: 400px;
}
div p{
    width: 100px;
    margin-left: 100px; // (margin-left) + 100 + 100 * 2 = 400px
    margin-right: 100px;
}
```

  当其值为负值时，如：  

```
div{
    width: 400px;
    border: 3px solid black;
}
div p{
    width: auto; // auto: 400 - (3 + 10 - 50 + 0 + 0) = 437px
    margin-left: 10px;
    margin-right: -50px;
}
```

  为了补全值至父级width，子级外边距设置负值后，子级width会大于父级而超出父级范围甚至是浏览器边界  
**注**：只有外边距可设置负值，其它如内容宽高度、边框、内边距都不能为负值  

  设置元素内容是其包含块宽度的2/3，可以如：

```
p{
    width: 67%;
    padding-left: 5%;
    padding-right: 5%;
    margin-left: 5%;
    margin-right: auto; //auto: 100% - (67% - 5% - 5% - 5%) = 18%
}
```  

- 垂直格式化  同水平格式化，设置高度(元素内容高)、边框、内外边距，height、border-top、border-bottom、padding-top、padding-bottom、margin-top、margin-bottom即共7个属性  

  也只有height、margin-top、margin-bottom可设为auto，其中margin-top和margin-bottom会被计算为0即没有外边距  

  当值为百分数时，依旧是继承父级元素高度的关系，如：  

```
div{
    height: 6em;
}
div p{
    height: 50%; // 6 * 50% = 3em
}
```

**垂直外边距的合并**  合并(重叠取最大值合并最小值)的前提是元素*没有*内边距和边框  

### 行布局
- 概念理解：  
1. `<em>` 标签  其高度由font-size来决定
2. 行间距  由line-height减去font-size来得到，允许有负值
3. 内容区类似于块级元素的内容框
4. 行内元素背景会作用于内容区及所有内边距  


- 行框  
对于行内元素，当其font-size为15px时，其**内容区**高度也为15px，又有line-height为21px时，其**行间距**为21 - 15 = 6px，则**行内框**高为15 + 6 = 21px。由行内框来确定行框的高度，即行中最高行内框顶端到最低行内框底端间的距离

- 设置line-height导致重叠  
当line-height大于本身的font-size时，间距会有负值则导致上下行重叠  
可做以下设定，如：  

```
p{
    font-size: 14px;
    line-height: 1em; //根据font-size来决定值
}
```

  最佳方案：设置line-height为倍数值(即缩放因子)，如：  

```
p{
    font-size: 15px;
    line-height: 1.5;
}
p small{
    font-size: 66%;  //font-size = 15 * 66% = 9.9px, line-height = 9.9 * 1.5 = 15px
}
```

- display  
  改变元素的**显示方式**，而不会改变*元素的类型*，如行内元素*只能是*块级元素的后代(子级)  

  而改变行内元素为**块级显示**(display: block)，*不代表*以块级显示的行内元素就可以成为块级元素的父级，类似a标签无论如何设计样式都不能成为p标签的父级  

  而块级元素总是从新行开始，行内元素与其它元素同一行  

  当处于以下情形时，可以应用这一属性：  
  1. 让一个inline元素从新行开始  `display: block`  
  2. 让块元素和其他元素保持在一行上  `display: inline`  
  3. 控制inline元素的宽度（对导航条特别有用）  `display: block`  
  4. 控制inline元素的高度  `display: block`  
  5. 无须设定宽度即可为一个块元素设定与文字同宽的背景色  `display: inline`  

  当值为inline-block(行内块元素)时，显示效果如替换元素在文本行中的显示一样，如img在p标签中，均可设置其宽高  
(**注**  行内元素无宽高及文本居中可设置)  

  当值为run-in(块/行内元素混合)，可以让某些块级元素成为下一个元素的行内部分  
首先知道一个元素display设置为block会生成一个**块级框**，设置为inline会生成一个**行内框**，所以设置为run-in会生成**run-in框**，当后面是一个块级框则该run-in元素将成为块级框开始处的一个*行内框*，但即使run-in框格式化另一个元素中的行内框，这两者也*不会存在*父子级继承关系  

```
h3{
    font-size: 125%;
    display: run-in;
}
p{
    padding-top: .5em;
}
```

这样h3元素将变成p元素内容开始处的一个行内框  
(**注**：只有当run-in框**后面**是**一个块级框**时run-in才起作用)  

- 行内元素的外边距  
只有**左右外边距**会*有作用*，而**上下外边距***不会起作用*，从而不会增加行内元素的line-height，(tips: 只有line-height、font-size、vertical-align会改变行间距)  
而行内替换元素如img、form等标签，外边距都会起作用  

- 行内元素的内边距  
当为段落设置边框时，加上内边距使至上下行不会太靠近  
所以可知上下内边距会对行内元素起作用  
可知行内元素的外边距会合并，但其内边距是**不会合并**的  
在处理段落布局时，可添加一下样式：  

```
p{
  margin: 0; //因段落上下行会有默认的margin隔开
  padding: .5em 0;
}
```

这样的设置，各段落上下内边距相加，构成上下行间有1em的间隔