---
layout: post
title: "javascript DOM高级程序设计 学习笔记"
date: 2016-08-12 18:37
categories: note javascript
tags: node对象
---



一本停产的书籍，《javascript DOM高级程序设计》，是的，没错，各大网上书店都没有，实体店可能还能找到，所以我只能下个电子版来看看，果然，不负各大书评，书，的确是好书，前几章已经跟其它书籍有所不一样，感觉自己可以有动力看完一本啦(O.S 很少冷静地看完一本技术书)




## Event事件
- 事件：不以"on"开头，如onclick不是事件而是用来引用一个对象的属性，可以通过它来为DOM元素指定一个click事件
- 事件侦听器：即当指定的事件发生时会执行的*函数或方法*
- 事件注册：指为一个DOM元素的具体事件，指定事件侦听器的*过程*
- 事件代理：由于事件冒泡，可把子节点的监听函数定义在父级上，经父级的监听统一处理多个子级事件  

### 事件类型
对象事件、鼠标事件、键盘事件、表单事件、W3C事件

#### 对象事件
- load和unload 如使用window对象的load事件(在页面完全载入成功后调用)
- error和abort error既适用于window对象也适用于图像对象
- resize
- scroll

#### 鼠标事件

##### 鼠标移动事件
- mousemove
- mouseover 当指针移动到一个新对象上触发
- mouseout

##### 鼠标单击事件
- mousedown
- mouseup
- click
- dblclick

#### 表单事件
- submit和reset  
```
ADS.addEvent(ele, 'submit', function(){})
```  
匹配格式A#A #A# (字母数字字母 中间任意量空格 数字字母数字)  
 .match(正则表达式) 返回匹配的值  
```
str.match(/[A-Z][0-9][A-Z]\s*[0-9][A-Z][0-9]/i)
```  

- blur和focus  
适用于label、input、select、textarea、button等表单元素  
focus会在单击元素或tab切换下一个元素时被调用  
blur会在单机元素之外的地方或tab切换时，基于focus调用上被调用  
```
ADS.addEvent(ele, 'focus', function(){
    当元素获得焦点时执行...
});
ADS.addEvent(ele, 'blur', function(){
    当元素失去焦点时执行...
})
```

- change  
适用于input、select、textarea表单元素，在focus事件发生后被调用

### 事件流
- 捕获阶段：浏览器会从最外层祖先元素向内部依次调用事件到目标元素
- 冒泡阶段：从目标元素向外依次通过祖先元素的顺序来冒泡事件  
- 事件注册/移除  
```
ele.addEventListener(type, listener, true/false)
```  
type：事件类型  listener：执行函数  true启用捕获阶段，false启用冒泡阶段  

ele.removeEventListener(type, listener, true/false)

```
ele.addEventListener(type,function(){
  console.log(this.value);
  this.removeEventListener(type, arguments.callee); //可移除自身注册事件
});

```
  
`e.stopPropagation()`  阻止事件冒泡
`e.preventDefault()`  取消默认动作

### 事件对象的属性
- bubbles 为布尔值，true为处于冒泡，false为处于捕获
- cancelable 为布尔值，true为事件默认动作可取消，false则不可以取消
- currentTarget 指的是当前正在处理的事件侦听器所在的事件流中的DOM元素  `e.currentTarget` 等价于 `this`  **表示注册事件的元素**
- target 为DOM中*最早调用*事件序列的目标对象，即发生事件的元素 (注：this != e.target)
- timestamp 由创建事件开始时间起经过的*毫秒数*
- type 事件名称的字符串值
- eventPhase表示当前事件侦听器处于事件流的哪个阶段  
```
function eventListener(e){  
    switch(e.eventPhase){  
    case Event.CAPTURING_PHASE:
    // 如果在捕获阶段执行的代码
    break;
    case Event.AT_TARGET:
    // 如果当前是目标对象时执行的代码
    break;
    case Event.BUBBLING_PHASE:
    // 如果在冒泡阶段执行的代码
    break;
}
}
```

### 事件对象的方法
- initEvent(eventType, canBubble, cancelable) 用于初始化由documen.createEvent('Event')创建的事件对象
- preventDefault() 取消对象默认动作
- stopPropagation() 停止事件流的进一步执行，包括捕获、冒泡阶段，目标对象。当调用这个方法时，所有侦听器依旧会在当前层次上执行，但事件流不会超出currentTarget

### 鼠标事件对象的属性 
- altKey、ctrlKey、shiftKey 表示鼠标事件发生时是否同时按住了键盘上Alt、Ctrl、Shift等键
- button 表示鼠标按下的键值，0为左键，1为中键，2为右键  
```
if(e.button == 0){
// 鼠标左键单击执行的代码
}
// 等价于
if(e.button == MouseEvent.BUTTON_LEFT){}
```
- clientX、clientY 指事件发生位置相对于DOM实现的客户端区域的水平和垂直坐标
- screenX、screenY 指事件位置相对于客户端所在屏幕的水平和垂直坐标
- relatedTarget 引用的是与事件相关的次要目标，一般情况值为null，但在mouseover它引用的是**鼠标离开前**一个对象，在mouseout它引用的是**鼠标之前进入**的那个对象

## 事件的兼容问题

### 访问事件的目标元素
- W3C: e.target
- IE: e.srcElement
- Safari: 文本节点会代替包含它的元素变成事件的目标对象  
解决方案：重新将目标对象指定为它的父元素  
```
if(e.target.nodetype == ADS.node.TEXT_NODE){
    target = node.parentNode
}
```

### 处理鼠标的位置
鼠标的光标应相对于文档原点的位置  
经过一些**能力检测**并根据访问页面的浏览器来调整要使用的属性  
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -  
W3C和IE都定义了clientX和clientY属性，但在确定针对浏览器*滚动后*的位移属性，两者各不相同，W3C使用document.documentElement.scrollTop，而IE使用document.body.scrollTop。
而Safari则把位置信息各放在事件的pageX和pageY属性中。  

```
var x = e.pageX
    || (e.clientX + (document.documentElement.scrollLeft
    || document.body.scrollLeft));
var y = e.pagey
    || (e.clientY + (document.documentElement.scrollTop
    || document.body.scrollTop));

```