---
layout: post
title: "JavaScript DOM编程艺术 学习笔记"
date: 2016-10-01 15:37
categories: note javascript
tags: node对象
---



往前复习javascript基础，由《JavaScript DOM编程艺术》出发，入门必学，相信大家都看过，基础但又有些容易忽略忘记的点，这些在这本书都能体现出来




## 动态修改样式
- ele.style.backgroundColor = 'red' 等价于 ele{background-color: red;}  
在js中设置样式，遇带连字符(-)的形式的都需转换为驼峰形  
在DOM2样式规范中提供了setProperty()方法，则以上写法可写成ele.style.setProperty('background-color', 'red')，但该方法IE不支持  

- for(item in obj){ }  循环对象(键值对) 以键(属性名)为循环项  

- 获取元素样式(行内)  ele.style.property(wordWord)  

- array.join(str)  以str分割array数组返回分割后的字符串  

- document.styleSheets  返回包含样式表(以<link>、<style>或行内等形式引入的样式表)的数组  

- DOM2规范下的document.styleSheets[i].cssRules 或 IE下的document.styleSheets[i].rules  来获取样式规则表(数组)

- DOM2规范下的document.styleSheets[i].insertRule(selector, index) 或 IE下的document.styleSheets[i].addRule(selector, styles, index)  新增样式到样式表指定位置

- 移除节点  node.parentNode.removeChild(node)指定node的父节点来操作removeChild(node)  

- 获取嵌入行内或引用样式表的样式  

  DOM2规范下的document.defaultView.getComputedStyle(ele).getPropertyValue(property) 或 IE下的ele.currentStyle.property  

  因元素最终样式是综合各种规则计算出来的，`getComputedStyle(node, 'node伪元素')` 能得到最终计算出来的样式规则  


## 动态添加标签
document.write()方法 与 innerHTML属性的区别  

- document.write()的使用需在`<body>` 中添加 `<script>` 标签，在`<script>`中进行调用，innerHTML则反之，任何位置调用都行  

- 由DOM方法添加标记，能提供更为**详细**的*DOM结构*，包括每个父子节点，而innerHTML则只是把整个标签添加，不会有任何**细节**  

- document.createElement("ele")  创建的标签只是文档碎片，此时并不属于DOM结构的任何一部分  

该注意的是，document.createElement()只会创建一次即每次创建一个新标签，所以，要添加列表`<ul><li></li> ...</ul>`时，需要把document.createElement()的声明**放进循环**中


## Ajax

```
var request = new XMLHttpRequest();
request.open('GET', 'xxx.php', true);
request.onreadystatechange = function(){
    if(request.readyState === 4 && request.status === 200){
      // 响应状态为4表示响应完成
      alert(request.responseText); // 取得服务器返回的文本数据
    }
}
request.send();
```


## animation动画  
实现每隔10毫秒移动1像素，起始位置(50, 100)，结束位置(200, 100)  

```
function moveMsg(){       
    var elem = document.getElementById('msg');
    var posX = parseInt(elem.style.left),
    posY = parseInt(elem.style.top);
    if(posX == 200 && posY == 100){
        return true; // 退出函数
    }
    if(posX < 200){
        posX++;
    }else if(posX > 200){
        posX--;
    }
    if(posY < 100){
        posY++;
    }else if(posY > 100){
        posY--;
    }
    elem.style.left = posX + 'px';
    elem.style.top = posY + 'px';

    <!-- 实现每隔10毫秒移动1像素 -->
    <!-- 每隔10毫秒重复执行函数本身 -->
    movement = setTimeout(moveMsg, 10);
}

function posMsg(){
    <!-- 在起始位置调用移动函数moveMsg() -->
    var msg = document.getElementById('msg');
    msg.style.position = 'absolute';
    msg.style.left = '50px';
    msg.style.top = '100px';
    movement = setTimeout(moveMsg, 2000); // 未经var声明的标量为全局变量
}
```


## 定时器
- setTimeout(func|code, delay)  
该函数传入两参数，第一个参数是将要推迟执行的函数名或代码，第二个参数是推迟执行的**毫秒数**  

值得注意的是：  
1. 推迟执行的代码必须以字符串形式，如`setTimeout("alert('for test!')", 1000)`  
2. 推迟执行的是*函数*时，可直接以函数名，如`setTimeout(func, 1000)`  
2.1 但作为参数的func带有参数的话，那得用`匿名函数`包裹，如  

```
function func(arg1, arg2, ...){
    ...
}

setTimeout(function(){
    func(arg1, arg2, ...);
}, 1000);
```


## iframe标签  
iframe内联框架元素，用来在当前页面内嵌另一个HTML页面  

`<iframe src="xxx.html" name="frame" id="frame"></iframe>`  

当标签属性带有name时，可经`<a>`标签和`<form>`标签的target属性值做锚  

在父窗口可由window.frames["iframe窗体name或ID"]引用子窗口frame  

子窗口可由window.parent访问父窗口  

通过contentWindow属性即iframe.contentWindow，来访问iframe元素所包含的HTML页面的window对象，类似的可以`iframe.contentWindow.document`获取到document对象后，进行对DOM元素的访问  

一切对iframe的访问操作，都需要等待iframe加载后才进行，即  

```
<iframe src="xxx.html" name="frame" id="frame"></iframe>

<script>
    var iframe = document.getElementById("frame");
    iframe.onload = function(){
        var frameDoc = iframe.contentWindow.document,
            frameEle = frameDoc.getElementById(element);
    }
</script>
```
