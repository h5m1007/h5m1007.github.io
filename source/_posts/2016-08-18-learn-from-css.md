---
layout: post
title: "CSS权威指南 学习笔记"
date: 2016-08-18 12:37
categories: css
tags: css
---



css三本经典书籍之一，《CSS权威指南》




## 背景与颜色
- background-repeat  
当值为repeat-x时，可指定图像只向x轴方向水平重复平铺  
当值为repeat-y时，可指定图像只向y轴方向垂直重复平铺  

- background-position  
1. 值为0% 0%  图像处于背景左上角  
2. 值为50% 50%  图像处于背景水平垂直居中  
3. 值为100% 100%  图像处于背景右下角  
4. 值为0 100%  图像处于背景左下角  
5. 值为100% 0  图像处于背景右上角  

可知*左值*代表**水平位置**，*右值*代表**垂直位置**，当只指定一个值时，默认当作是水平值，而另一个值默认为垂直值且为center即50%  

- background-attachment  
值为fixed  图像从**可视区域左上角**开始定位，而不是从设置该图像为背景的**元素左上角**开始定位  

## 浮动元素  
浮动元素会以某种方式从文档正常流中删除，自身成一类，但还是会对文档剩余部分有影响  

浮动元素的*外边距***不会合并**，如一个浮动的img在段落p中，img的外边距不会与p的外边距合并  

浮动元素会生成一个**块级框**，且无论元素本身是什么标签，如a标签本身为行内元素，但对其浮动后，a标签会生成一个块级框，以块级元素一样显示  

### 浮动元素位置的限制  
- 浮动元素顶端**不会超过**其包含块(父元素)内边界顶端
- 浮动元素**不能比**其之前所有浮动元素或块级元素的顶端*更高*
- 如果一行内没有足够空间，浮动元素会被挤到下一新行中
- 当为浮动元素的父元素设置浮动后，父元素将重新包含浮动元素  


### 清除浮动
当不想元素周围的浮动元素对其起到影响时，可以使用属性`clear: both;`，其中有left或right值可选，分别是用来清除左或右的浮动元素对该元素的影响  

如：h3标签本身为块级，而在h3周围有img标签其浮动在h3左边时，应用`h3{clear: left;}`后，h3将推至过下一行(使h3内容区在浮动元素下边界的下面，则会增加h3元素本身的外边距)  

又如：当父级没有设置任何宽高时，其子级浮动脱离文本流，父级将不会包裹其子级  
解决方案有：  
1. 为父级设置溢出隐藏(overflow: hidden)后，父级将重新包裹其子级  
2. 为父级设置浮动(float: left)和宽度(width)后，父级将重新包裹其子级  

```
.container{
  background-color: black;
  overflow: hidden;
}

.div1{
  background-color: aqua;
  width: 100px;
  height: 100px;
  float: left;
}
.div2{
  background-color: red;
  width: 100px;
  height: 100px;
  float: left;
}

<div class="container">
  <div class="div1"></div>
  <div class="div2"></div>
</div>
```

## 定位
position，可选值有：static、relative、absolute、fixed  
relative：元素仍保持未定位前的形态，所占空间仍保留，相对于最近的包含块定位  
absolute：元素从文档流*完全被删除*且相对于其**包含块**定位，所占空间关闭，定位后生成块级框  
fixed：类似于absolute，但是相对于**视窗本身**  
如，让一个div占据其包含块的1/2

```
div{
  position: absolute;
  top: 0;
  bottom: 0;
  left: 0;
  right: 50%;
}
<!-- 等价于 -->
div{
  width: 50%;
  height: 100%;
  position: absolute;
  top: 0;
  bottom: 0;
  left: 0;
  right: 50%;
}
```

### 绝对定位
- 给定值引起的限制  
如样式表中某标签为绝对定位，且设置有外边距：

```
div{
  position: relative;
  width: 25em;
}
div span{
  position: absolute;
  top: 0;
  left: 1em;
  right: 1em; //将被忽略掉
  width: 10em;
  margin-left: 1em;
  margin-right: 1em;
}
```

因由span的样式中，top+left+right+width+margin-left+margin-right要等于其包含块div的width值25em，还差11em，所以span的right值将被忽略  

(**注：一般情况下，样式中只要有一个属性值设为auto，那么会根据水平x轴上定位偏移值+内外边距+边框+宽度=其包含块大小这公式，来忽略某值设为auto的属性，以来延伸大小至其包含块大小**)

- 垂直方向的定位
类似于水平方向定位所会出现的受限问题，如

```
div{
  position: relative;
  width: 10em;
  height: 10em;
}
div span{
  width: 100%;
  height: 5em;
  position: absolute;
  top: 0;
  bottom: 0; // 会被计算值5em所覆盖即bottom: 5em;
  margin: 0;
}
```

- z-index
设置其元素的叠放顺序，坐标轴的Z轴，离用户越近其值越大，反之值越小  
一旦元素指定了z-index值，就会建立自己的**局部叠放上下文**，则该元素的后代相对于其祖先都有其*自己的叠放顺序，*如：  

```
#one{
  width: 50%;
  heigth: 10em;
  position: absolute;
  top: 0;
  left: 0;
  z-index: 10;
}
#two{
  width: 50%;
  heigth: 10em;
  position: absolute;
  top: 5em;
  left: 25%;
  z-index: 7; 
}
#one b{
  width: 50%;
  heigth: 10em;
  position: absolute;
  top: 11em;
  left: 0;
  z-index: -404; //尽管是负值但其父级#one在#two前面，所以#one b也在#two前面
}
```

又如：  

```
<body>
  <p style="position: absolute; z-index: -1;">Where am I?</p>
</body>
```

因body元素与p的父元素在同一个**叠放上下文**中，即其z-index为0，并不会建立一个新的叠放上下文，所以p与body处于*相同的*叠放上下文中(即初始包含块的叠放上下文)，p会被body的内容所覆盖，但不会被*body的背景*所覆盖  

### 固定定位
fixed  类似与absolute，会完全从文档流中去除，不占空间，但是相对视窗来定位的，应用如：  

```
div#header{
<!-- 页面固定头部，不随页面滚动而滚动 -->
  position: fixed;
  top: 0;
  bottom: 80%;
  left: 20%;
  right: 0;
}
div#footer{
<!-- 页面固定页脚，不随页面滚动而滚动 -->
  width: 100%;
  height: auto;
  position: fixed;
  bottom: 0;
}
div#sidebar{
<!-- 页面固定侧边栏，不随页面滚动而滚动 -->
  position: fixed;
  top: 0;
  bottom: 0;
  left: 0;
  right: 80%;
}
div#main{
<!-- 页面固定内容区 -->
  position: fixed;
  top: 20%;
  bottom: 0;
  left: 20%;
  right: 0;
  overflow: scroll;
}
```

### 相对定位
可知当元素相对定位后，该元素会为其子元素建立一个**新的包含块**，而这个包含块对应于该元素*原来所在的位置*，即该元素依旧在文档流中且依旧占空间  

### 最大最小值
min-width、min-height  至少宽高  
max-width、max-height  至多宽高  

如，当让元素宽是其包含块的3/4且达到400px就不能再宽  
`left: 0%; right: auto; width: 75%; max-width: 400px;`  
最大最小属性可以相对安全地混合使用不同单位，允许百分数且可设置基于长度的限制  

(**注：设置其属性值为百分数时，都是相对其包含块即父元素的对应的值来设置的**)  


## 内容剪裁
`clip: rect(top, right, bottom, left);`  

指定其元素的显示区域大小，其中top、right等是来指定距*基于元素左上角*的距离，如：
`rect(0, 20px, 20px, 0)` 来剪裁覆盖元素左上角20px * 20px的正方形  
其中需要注意的是，其延伸方向只能是该元素的向右或向下地延伸  

## 元素可见性
visibility  可选值有：visible、hidden  

当设值为hidden时，与`display: none;`有所区别的时，后者将会在文档删除，前者依旧**占空间**，只是**不可见**而已  

该注意的是，这属性**可被继承**，所以当设其父元素为hidden时，后代均不可见，需显式地设置后台元素为visible  

