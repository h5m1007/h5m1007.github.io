---
layout: post
title: "重新排版本博客 记录"
date: 2016-10-15 10:30
categories: javascript css
tags: javascript css
---



重构博客，简化博客排版和样式，增加页面浏览，一些额外辅助功能




## 样式

- box-sizing
设置或检索对象(元素)的盒模型组成模式  

值有:  
`border-box`  padding与border会被包含其对象(元素)的width及height之内，即元素的实际(总)宽度等于width  
默认的`content-box`  表现为标准模式的盒模型  

应用：  
设置全局标签块`box-sizing:border-box;`，在页面中，侧边栏与中间主内容块，之间的间隔将得到很好的处理  

- background  
| `background-size: contain;`  等价于 `background-size: 100% auto;`  背景高度等于容器高度，宽度auto  
| `background-size: cover;`  等价于 `background-size: auto 100%;`  背景宽度等于容器宽，高度auto  
| `background-origin`  可选属性有：`border-box` 指定背景在**容器边框**左上角开始延展、'padding-box' **容器内边距**左上角开始延展、'content-box' **容器内容区域**开始延展  
| `background-clip` 背景图位置不变，只是裁剪背景图的显示区域，可选属性有：`border-box` 从容器的border区域(不包含border)开始向**四周**裁剪背景、'padding-box' 从容器的padding区域(不包含padding)开始向**四周**裁剪背景、'content-box' 从content区域开始向**四周**裁剪背景  


### 动画
**关键字**：`transform` 变换属性  `transition` 渐变效果  `@keyframs`关键帧  

- `transform`  
| 1. `transform:rotate(60deg)`  使元素**旋转**，元素将沿中轴线顺时针偏转60度，可选值还有 `rotateX()`、`rotateY()`、`rotateZ()``rotate3d()`，而 `rotate3d(x,y,z,deg)` 是提供在其它向量上应用旋转，如 `rotate3d(1,-1,0,60deg)`  
| 2. `transform:skew(30deg,10deg)`  使元素**变形**，元素将在x轴偏转30度，y轴偏转10度  
| 3. `transform:scale(1.1,1.1)`  使元素**缩放**，元素将在x、y周放大1.1倍，但元素内容会被拉伸而失真，可选值有：`scaleX()`、`scaleY()`、`scaleZ()`  
| 4. `transform:translate(100px,20px)`  使元素**位移**，元素将在x轴向右位移100px，在y轴向下位移20px，可选值有：`translateX()`、`translateY()`、`translateZ()`  

- `transition`  使元素产生渐变过渡效果  
`transition: all 2s ease 2s;`  <== 需要事件来触发，如鼠标划过、鼠标点击等  最多接受四个参数，分别是属性、过渡时间、过渡函数、延迟时间  
| 1. 属性指的是需要增加过渡效果的属性名，如

```
<style>
    #content{
        width: 100px;
        height: 100px;
        transition: height 2s;
    }
    #content:hover{
        height: 200px; /*由100px在2s内平滑过渡至200px*/
    }
</style>
```

| 2. 过渡时间指的是渐变过渡效果作用到指定属性所需时间  

| 3. 过渡函数指的是设置渐变动画，可选值有：  
| 3.1 `ease`  默认值，逐渐变慢  
| 3.2 `linear`  匀速  
| 3.3 `ease-in`  加速  
| 3.4 `ease-out`  减速  
| 3.5 `ease-in-out`  加速再减速  

- `@keyframs`
关键帧，结合 `animation` 属性来制作动画  

两种写法：  

```
<style>
    /*第一种写法*/
    @keyframes spin{
        from{
            transform: rotate(0);
        }
        to{
           transform: rotate(360deg); 
        }
    }
    
    /*第二种写法*/
    @keyframes spin{
        0%{
            transform: rotate(0);
        }
        50%{
            transform: rotate(180deg);
        }
        100%{
            transform: rotate(360deg);
        }
    }
</style>
```

`@keyframes`  声明后需写上动画名称，而 `{}` 内就是一些在**不同时间段**(具体值由 `animation` 指定时间决定 )应用的样式，配合元素中定义的animation属性，来定义动画  

`animation`  完整写法：  

`animation: name time speed delay loop alternate;`  

| 1. name指的是动画名称，由**关键帧**的声明后写的**动画名**决定  
| 2. time指的是动画**执行一次**所需时间  
| 3. speed指的是动画**速度函数**，和 `transition` 的过渡函数值一致  
| 4. delay指的是动画开始前的**延迟时间**  
| 5. loop指的是动画**循环次数**，值为 `infinite` 无限循环  
| 6. alternate指的是动画正向循环完后再反向循环  

如：`animation: spin 2s linear 2s infinite alternate;`  spin动画在2秒后匀速以每2秒正方向无限循环播放一次

### 媒体查询@media
在css的控制上，实现所谓的响应式设计，关键字： `@media`  

- `@media screen and (min-width: 600px) and (max-width: 900px)`  

[1] screen指的是媒介类型，可选还有 `only-screen`  
[2] and作为条件，可叠加多个条件  
[3] and条件表示限制的屏幕范围，常用属性width，参考可选有：>=320px为小屏幕手机，320px~768px为大屏幕手机，768px~1024px为平板，>=1024为PC  
[4] and条件判断横竖屏，可选属性有： `orientation: portrait`  判断设备为横向； `orientation: landscape`  判断设备为竖向  

## 排版

- 侧边栏
内容采用无样式标签，类名为`wrap`，表示包，包裹其内容，有历史文章、分类、标签  

### 弹性盒子

关键字： `box-flex`  在分栏布局上，各种方案层出不穷，但唯独 `box-flex` 来得更加简单高效，只是兼容性不佳，应用在移动端更为适用  

- 用法  

[1] 对其父元素声明  `.father{ display: box}`  先声明父元素空间可被子元素分配  
[2] 子元素声明  

```
<style>
    son1{
        box-flex: 1;
    }
    son2{
        box-flex: 3;

        /*亦可设值为固定px*/
        /*box-flex: 400px;*/
    }
    son3{
        boxf-flex: 1;
    }
</style>
```

实现 1:3:1 三栏布局  

[3] 兼容写法  

```
.father{
    display: -webkit-box;
    display: box;
}
.son{
    -webkit-box-flex: 3;
    box-flex: 3;
}
```

- 属性

[1] box-align  设置box在垂直方向上的对齐方式，可选值有stretch、start、end、center、baseline

[2] box-pack  
设置box在水平剩余空间的分配，可选值有start、end、center、justify

[3] box-direction  
设置box的子元素的排列顺序，可选值有normal、reverse、inherit