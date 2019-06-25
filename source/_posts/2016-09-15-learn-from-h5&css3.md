---
layout: post
title: "HTML5和CSS3实例教程 学习笔记"
date: 2016-09-15 11:00
categories: note HTML5 CSS3
tags: h5 css3
---



《HTML5 and CSS3》译名《HTML5和CSS3实例教程》




## H5新标签
- `<header>`  页面头部
- `<footer>`  页面尾部
- `<nav>`  页面导航
- `<section>`  页面逻辑区域或内容组合，对内容进行归类分组，如侧边栏
- `<article>`  页面中一篇完整内容，对具体内容的描述
- `<aside>`  页面补充或相关内容，如旁白、引言、图表、相关链接
- `<meter>`  进度条固定值
- `<progress>`  进度条动态值

### 自定义属性  
- data-attr  

```
<a href="#" data-width="600" data-height="400" class="popup">News</a>

<script>
    (function(){
      var popup = document.getElementsByClassName('popup')[0];
      var popupW = popup.getAttribute('data-width');
      var popupH = popup.getAttribute('data-height');
      popup.onclick = function(e){
        e = e || window.event;
        e.preventDefault();
        window.open(this.href, 'popup', "width=" + popupW + ", height=" + popupH);
      }
    })();
</script>
```

- 表单新特性  

input标签新类型type有：  
1. range创建滑块，属性值有min和max  
2. number在原有属性值上增加min和max设置上下限，页面显示有上下箭头  
3. date创建日期控件
4. email让不同客户端在输入email时，显示的键盘输入有所区别  
5. url同上  
6. color创建颜色选择器

input新属性：  
1. autofocus设置后，页面刷新进入自动获得焦点  
2. autocomplete = "off" 设置后，会取消某些浏览器自动为表单填充数据  
3. contenteditable = "true" 设置后，会让任何元素变为可编辑状态  

## CSS3  
- `ele:nth-of-type(参数){}`  作用于指定特定标签ele，可带参数有：  

1. 表示奇偶数 even odd  
2. 表示倍数 n 2n 3n ...
3. 表示偏移 n+1 n+2 n+3 ...

- `ele:nth-child(参数){}`  遍历标签ele的子元素，可带参数同上  
- `ele:last-child{}`  定位标签ele最后一个子元素  
- `ele:first-child{}`  定位标签ele第一个子元素  
- `ele:nth-last-child(参数){}`  从标签ele最后一个子元素**往前**遍历，可带参数同上  

- ele:after{ content:; }  在标签ele后追加内容但不占html  

## 增强可访问性  
主要针对有朗读功能的屏幕阅读器  

- 新增属性  

1. role属性  向阅读器说明某元素用途  
2. aria-live  标识一个可能会被ajax自动更新的区域  
2.1 aria-live="polite" 则会在阅读完前面内容后接着阅读更新的内容  
2.2 aria-live="assertive" 则会停止当前内容直接阅读更新的内容
3. aria-atomic  标识某动态区域的阅读方式  
3.1 aria-atomic="true" 让朗读已变化区域的**全部内容**  
3.2 aria-atomic="false" 仅让朗读已变化区域的**节点内容**，如列表项

## canvas绘画  
`<canvas></canvas>` 由canvas标签创建绘画画板，经js来绘制，css不起作用  

- `var context = canvas.getContext('2d')`  得到绘制2d图形的画布  
- `context.fillStyle = "#fff"`  声明绘画填充颜色  
- `context.strokeStyle = "#fff"`  声明绘画画笔颜色  
- 绘制线条  

```
context.lineWidth = 2;
context.beginPath(); // 开启路径

// 绘制点，任何一条画线都从这点出发
context.moveTo(0, 40); 

context.lineTo(30, 0);
context.lineTo(60, 40);
context.lineTo(285, 40);
context.stroke(); // 结束绘画
context.closePath(); // 关闭路径
```

- 绘制文字  

```
context.font = 'italic 40px sans-serif';
context.textBaseline = 'top'; // 声明文字起始基线
context.fillText('AwesomeCo', 60, 0) // 声明文字内容和绘制开始位置(60, 0)
```

- `context.translate(20, 20)`  // 转移绘制绘制起始点(开始是原点(0, 0))  

- `context.fillRect(0, 0, 20, 20)`  // 在(0, 0)处绘制20*20的矩形  

- `context.fill()`  // 进行填充图形

## H5多媒体  

- `<audio>`  放置音频标签，应用代码如下：  

```
<audio controls>
  <source type="audio/ogg" src="xxx.ogg">
  <source type="audio/mpeg" src="xxx.mp3">
  <a href="xxx.mp3">Download xxx.mp3</a>
</audio>
```

- `<video>`  放置视频标签，应用代码如下：  

```
<video controls>
  <source src="xxx.mp4">
  <source src="xxx.ogv">
  <p>Your browser does not support the video tag.</p>
</video>
```


