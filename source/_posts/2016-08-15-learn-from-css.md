---
layout: post
title: "CSS权威指南 学习笔记"
date: 2016-08-15 14:37
categories: css
tags: css
---



css三本经典书籍之一，《CSS权威指南》




## 选择器（未意识到的知识点）
- 部分属性选择：如p[class="urgent warning"] 完全值匹配, p[class~="warning"] {font-weight: bold;} 等价于 p.waring {font-weight: bold;} , 而波浪号(~)实现部分选择匹配，即属性值中出现的一个用空格分隔的词来完成选择;   
除了class属性外，"~="选择器还适用于任何属性，如img[title~="Figure"], 没有title属性或title值不包含"Figure"的图像都不会被匹配
- 子串匹配属性选择器:  

  | 类型            | 描述                                 |
  | :-----------    | :---------------------------------   |
  | [foo^="bar"]    | 选择foo属性值以"bar"开头的所有元素   |
  | [foo$="bar"]    | 选择foo属性值以"bar"结尾的所有元素   |
  | [foo*="bar"]    | 选择foo属性值包含子串"bar"的所有元素 |

- 选择子元素(>): 如h1 strong {color: red;}与h1>strong {color: red;}, 是不同个选择范围, 第一个是h1下任意的strong标签, 而第二个是h1的*下级子元素*strong标签
- 选择相邻兄弟元素(+): 如h1+p {margin-top: 0;}, 选择紧跟与h1标签同级的p标签, 样式应用于p标签

### 伪类和伪元素
静态伪类：:link、:visited, 动态伪类：:focus、:hover、:active  
样式表中伪类排列顺序推荐写法：link-visited-focus-hover-active  

- 锚的伪类：a:link、a:visited, 在选择a:link来应用样式时，可以**避免**让非链接的a标签(不带href属性)也被应用到链接样式
- :first-child  **注：易被混淆**  如 li:first-child {font-weight: bold;} 这里的 :first-child 表示某元素(在html中必须是ol或ul)的第一个子元素的所有li标签
- 伪类组合  
如  a:link:hover {color: red;} a:visited:hover {color: green;}  表示鼠标停在未访问链接时链接变红，第二个表示鼠标停在已访问链接时变绿  

伪元素：设置首字母样式(:first-letter)、设置第一行样式(:first-line)、设置之前元素样式(:before)、设置之后元素样式(:after)  

- p:first-letter {color: red; font-size: 200%;}  段落中首个单词字母红色且两倍大小于其余字母
- p:first-line {color: purple;}  只应用于段落中第一行变紫
- h2:before {content:"()"; color:silver;}  在h2标题中内容前面插入生成的内容("()"), 所生成的内容为单独的主体
- body:after {content:" The End!"}  在文档的最后插入结束语  

## 尺寸、大小、单位
- 百分数：百分数值是相对于另一个值，一般可能是同一元素另一个属性值，或是从父元素继承的一个值，或是祖先元素的一个值  

### 相对单位
- em  当一个元素一单位的font-size为14px时，相对于该元素1em就是14px，如  
`h1{font-size:24px} h2{font-size:18px;} h1, h2{margin:1em;}`  此时margin相对于h1来说1em就是24px，相对于h2来说1em就是18px，*在设置字体大小时，em会相对于父元素字体大小改变*  

(**注：em与百分数单位不同的是，em都是相对于父级的font-size来设置的，而百分数是相对父级对应的属性设置的，如子级宽度设置了百分数则它是相对父级宽度，子级字体设置了百分数则它是相对父级字体**)  

## @import
在样式表中引入另一个样式表，在html由`<link type='text/css' rel='stylesheet' href='http://xxx/xxx.css'>`，而在xxx.css中由@import url(yyy/yyy.css)引入另一个样式表，浏览器会以http://xxx/yyy/yyy.css的地址来查找另一个样式表  

## 关键字
### inherit  
该关键字是所有属性所公有，它使一个属性值与其父元素的值相同  

```
#toolbar{ background:blue; color:white; }
#toolbar a{ color: inherit; } // 经inherit继承父元素的color白色
<div id="toolbar">
    <a href="#">One</a> //浏览器预设链接为蓝色
</div>
```  

### 字体加粗级别
font-weight值分别有：normal、bold、bolder、lighter、100~900、inherit
关键字100~900来映射字体预设级别，级别梯度分界线可能是400对应normal，700对应bold  

bolder对应比所**继承的父元素**font-weight值更粗的一个字体加粗，要先指定父元素的font-weight值  
当指定的字体库所能提供只有两加粗度即regular和bold，那么父元素值为100默认normal。则子元素值为bolder即700  

加粗度上移，如 

```
p {font-weight: 100;}
p span {font-weight: 400;} // 默认normal
p span strong {font-weight: bolder;} // 值为700
p span strong b {font-weight: bolder;} //值为800(值由700上移至800)
```  

加粗度下移，如 

```
p {font-weight: 900;}
p span {font-weight: 700;} // 默认normal
p span strong {font-weight: lighter;} // 值为400
p span strong b {font-weight: lighter;} //值为300(值由400上移至300)
```  

### 字体大小
font-size值分别有: xx-small、x-small、small、medium、large、x-large、xx-large、smaller、larger、inherit  
共有7个绝对值，对应于html标签中`<font size="7">` 一般默认字体大小为3(即small)  
其中*相对大小*是关键字larger、smaller，两者使元素大小相对于其父元素大小在*绝对大小*梯度上上移或下移，计算绝对大小采用**缩放因子**(一般为1.2)  
不同于加粗的相对值，字体大小的相对值无须限制在绝对大小范围内

字体大小值为百分数，根据父元素继承的大小来计算  

```
body {font-size: 15px}
p {font-size: 12px}
p em {font-size: 120%} //14.4px
p em strong {font-size: 135%} //14.4 * 135% = 19.44px(可积累)
```  

有时em会等价于百分数，即1em等于100%  

```
p.one {font-size: 166%;}
p.two {font-size: 1.6em;}
```  

#### font属性
值：font: font-style font-variant font-weight font-size/line-height font-family  

**注**：  

- style、variant(变形)、weight这三者顺序可任意，但size、family顺序固定且必须有值
- 当使用font来简写字体时，为声明的font属性将被设成默认值，如  

```
h1 {font: 200% sans-serif;}
<!-- 等价于 -->
h1 {font: normal normal normal 200% sans-serif;}
h2 {font-size: 150%} <!-- 无变化 -->
```
