---
layout: post
title: "javascript DOM高级程序设计 学习笔记"
date: 2016-08-13 18:37
categories: note javascript
tags: node对象
---



一本停产的书籍，《javascript DOM高级程序设计》，是的，没错，各大网上书店都没有，实体店可能还能找到，所以我只能下个电子版来看看，果然，不负各大书评，书，的确是好书，前几章已经跟其它书籍有所不一样，感觉自己可以有动力看完一本啦(O.S 很少冷静地看完一本技术书)




## node对象
- 每个元素都拓展自node对象，同时具有自己的属性与方法
- *每种类型对象的nodeName*

| obj              | return value     |
| :------          | :------          |
| Element.nodeName | 元素的名称(大写) |
| Attr.nodeName    | 属性的名称(小写) |
| Text.nodeName    | #text            |

*每种nodeType的等价常量*

| nodeType值 | 等价常量            |
| :------    | :------             |
| 1          | Node.ELEMENT_NODE   |
| 2          | Node.ATTRIBUTE_NODE |
| 3          | Node.TEXT_NODE      |

### 子节点
- ele.childNodes 获取ele的所有子节点，其中包括不以标签形式出现的文本节点(空白也算)，在IE下文本节点不包括
- ele.parentNode ele.firstChild ele.lastChild ele.previousSibing ele.nextSibling
- **特别注意：以上获取ele的节点都会包括空白的文本节点(#text)**

### 遍历item()
- array.item(i) 等价于 array[i]，遍历数组
- ele.getNamedItem('属性名') 等价于 ele.getAttribute('属性名')

### hasChildNodes() hasAttributes()
- ele.hasChildNodes() 返回布尔值 判断元素是否存在节点
- ele.hasAttributes() 返回布尔值 判断元素是否存在属性

### appendChild() insertBefore() replaceChild() removeChild()
- ele.appendChild(新值) // 向ele节点最后位置新增子节点  

- parentEle.insertBefore(newEle, parentEle.firstChild) // newEle将插入到parentEle.firstChild(参考值)前面  

- ele.parentNode.replaceChild(新值, ele)  

- ele.parentNode.removeChild(ele)

### 对Node对象的引用
- document.getElementById() 是对node节点的应用，而并非为副本

### cloneNode()
- node.cloneNode(true) 复制了node节点
- parentNode.appendChild(node.cloneNode(true)) 等价于移动了该节点

## Element对象
- DOM文档树由element节点构成
- 都拥有node对象的属性和方法

### 操纵ele对象的属性
- getAttribute(name)
- setAttribute(name, value)
- removeAttribute(name)

### 增加js原生对象的方法或属性
- 如：对string对象增加方法
  `String.prototype.fn = function(){}`

### 转换大小写
- obj.toUpperCase() 转成大写
- obj.toLowerCase() 转成小写

### .indexOf()
- stringObj.indexOf(str)
- 返回str在stringObj中首次出现的位置(下标)
- 找不到则返回 -1

### .substring()
- stringObj.substring(start,stop)
- 返回在stringObj中，指定开始位置和结束位置间的*字符串*(包括start上的字符)

### 三元运算
boolean ? true(0) : false(1)

### switch判断
```
switch(x1 or x2){
    case x1:
        ...
    break;
    case x2:
        ...
    break;
    default:
        ...
    break;
}
```

### string分割
.split() 返回数组

1. `str.split("指定分割符")`
2. `str.split(正则表达式)`
3. `str.split("xxx|xxx")，"|" 符号连接两分割符`

`注意：特殊字符如"."、"|"，需.split("//.")、.split("//|")`

### 迭代
```
for(property in object){
    if(xxx) continue // 中断迭代
}
```