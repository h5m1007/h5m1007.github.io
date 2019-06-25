---
layout: post
title: "javascript DOM高级程序设计 学习笔记"
date: 2016-08-10 17:10
categories: note javascript
tags: js对象 实例化
---



一本停产的书籍，《javascript DOM高级程序设计》，是的，没错，各大网上书店都没有，实体店可能还能找到，所以我只能下个电子版来看看，果然，不负各大书评，书，的确是好书，前几章已经跟其它书籍有所不一样，感觉自己可以有动力看完一本啦(O.S 很少冷静地看完一本技术书)




## javascript
- javascript，一种原型式的面向对象(OO)语言，没有类，所有的一切派生于对象的一个副本

## js对象
* 对象 即一组变量(属性)和函数(方法)的集合的**实例**
* 从函数到字符都是对象，分为两类：Function对象、Object对象
* 内置对象：Array、String、Boolean、Number、Math、Date、RegExp...

### 覆盖作用域链中的对象
1. 函数实际都是window对象的方法
2. 当需拓展js内置函数时，需对其进行包装函数，不然直接拓展对导致无限地递归循环

### 函数抽象
函数抽象指的是把一个非常具体的东西改进为一个较为通用的东西的过程

## 类(func)继承
由函数来定义类似关键字类CLASS，而类继承可通过`obj.prototype`来模拟  

```
<script>
    function Animal(){};

    function Ferret(){};

    // 实现继承
    Ferret.prototype = new Animal();

    var ferret = new Ferret();

    // 经instanceof来检测对象是否存在原型链上
    ferret instanceof Ferret; // 返回true
    ferret instanceof Animal; // 返回true
</script>
```

## 对象的实例化
- 所实例化的对象必须是一个构造函数
- new关键字告知js基于给定对象来创建一个全新的对象
- 新创建的对象将被赋值给一个变量
- 通过该变量访问对象的新实例
- **对于Object对象的实例化，不能再基于新实例，来new创建另外的实例**
- **但对于Function对象的实例化，它的实例可作为构造器方法，即通过new再创建另外的实例**

```
<script>

    var fn = function (msg){
        // 充当 类CLASS
        alert(msg);
        this.msg = msg;

        // 私有变量
        var separator = 'private';

        // 私有方法
        function claerMsg(){
            this.msg = '';
        }

        // 特权方法
        this.specialFn = function(){
            alert("this is a private function");
        }
    }

    // 静态方法
    fn.alertName = function(){
        alert("fn.alertName");
    }

    // 添加一个公有方法
    fn.prototype.alertMsg = function(){
        return this.msg; // 来自fn函数体内定义的属性
        alert("fn.prototype.alertMsg");
    }
    var myObj = new fn('msg for test');
    myObj.alertMsg() == 'msg for test'; // 返回true

</script>
```

- 对象(myObj)被实例化后，构造函数(fn)会立即执行它所包含的代码
- 而fn里的msg属性，也只会被fn的实例(myObj)中可用，fn函数本身无效
- 而fn的alertName函数，fn的实例(myObj)是不能使用
- 而向fn的原型(prototype)添加方法(alertMsg)，实例(myObj)可以调用该方法
- 而在fn内的特权方法(specialFn)，myObj可以调用且可通过作用域链访问私有成员separator的权限
- 小结：1. 因私有和特权成员在*函数*内部，所以它们会被带到*函数*的每个实例中；
        2. 公有的原型成员为*对象蓝图*的一部分，适用于由new实例化该对象的每个实例
        3. 静态成员只能是被对象本身使用，其余对象的实例无效

## this
1. this引用的是包含它的函数，当作为某个对象的方法被调用时的那个对象
2. 
```
    var sound = 'Roar!';
    function myOrneryBeast(){
        this.style.color = 'green';
        <!-- 未经调用该函数下的this，引用的是window全局对象 -->
        alert(sound);
    }
    function initPage(){
        var example = ADS.$('example);
        ADS.addEvent(example, 'mouseover', myOrneryBeast);
        <!-- 此处this引用的是example -->
    }
```

## 闭包
闭包是能够访问**自由变量**的函数，在闭包中定义的函数可以记忆它被创建的环境  

而**自由变量**指的是即不是本地声明也不作为参数传递的变量  
在一个作用域中使用的变量，并不是该作用域声明的，则该变量对于该作用域来说就是自由变量  

```
<script>
    function numGenerator(){
        var num = 1;
        function checkNum(){
            console.log(num); // 这里的num对于checkNum来说，就是自由变量
        }
        num++;
        return checkNum;
    }
    var number = numGenerator(); // 返回checkNum函数
    number(); // 2
</script>
```

## apply()与call()
1. 两者是用来重新定义执行环境
2. call()接受参数列表，而apply()接受参数数组  

```
<script>
    /*
        改变fn的执行环境为obj
        当fn有参数时，需写在obj的后面
        使用apply时，fn的参数需以数组的形式
    */
    fn.call({obj}, argument1, argument2, ...)
    fn.apply({obj}, ['argument1', 'argument2'])
</script>
```

3. 应用  

```
<script>
    function a(){
        window == this; // 返回true
    };
    function c(){
        this.c == 'd'; // 返回true
    };
    function e(argument1, argument2){
        argument1 == 'arg1'; // 返回true
        argument2 == 'arg2'; // 返回true
    };
    a(); // 执行后，函数体内this指向全局window
    c.call({ c: 'd'}); // 改变c作用域为d
    e.call({e: 'f'}, 'arg1', 'arg2'); // 改变e作用域为f且把f的参数也一并传给e
    e.apply({e: 'f'}, ['arg1', 'arg2']); // 等价上述只是接受参数形式为数组
</script>
```


## try{} catch(){}
- 
```
    try{
        errorFn();
    } catch(theException){
        alert(theException.name + theException.messagae);
    }
```

## 对象字面量
1. 即*键值对*来表示
2. 
```
    obj.prototype = {
        fn1: function(){

        },
        fn2: function(){

        }
    }
```

## 窗口宽度
1. .innerWidth包括窗口宽度和滚动条，在IE下并不支持该属性
2. .clientWidth窗口可视区域，只包括元素宽度，在IE下支持

## typeof
`typeof 判断对象 === '指定类型'`  返回布尔值，当遇判断对象为 null、数组、对象时，一律判断为Obj对象类型  

## instanceof
`判断对象 instanceof 指定类型/类`  应用于判断实例对象是否为指定类的实例，和判断数组是否为数组类型、对象是否为object  

## innerHTML
obj.innerHTML 当obj存在html标签但以字符串显示时，.innerHTML转换obj以html输出

## toString()和String()
把给定的值强制转换为字符串

1. xxx.toString()
2. String(xxx)

## 正则匹配
/主体/g: /指定匹配字符/, g: 指定在整个字符串中匹配

## .replace() 替换
- stringObj.replace(旧值, 新值)
- stringObj.replace(正则匹配, 新值)