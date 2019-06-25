---
layout: post
title: "NodeJs 全记录二"
date: 2016-10-21 10:00
categories: node javascript
tags: node nodejs 
---



前端程序猿的第二春 使用老本行JS来构建服务器端的神器  NodeJs




## 高并发
高并发指的是在同一个时间点，有多用户同时访问URL地址，如淘宝双11双12，就会产生高并发  

- 后果  
服务器被占满崩溃，数据存储、更新结果和理想设计不一样，如出现重复数据记录，多次添加用户记录。而对于用户来说，就是访问网站延迟，网站加载缓慢等等  

### nodeJs处理高并发
nodeJs引入一个概念，那就是“共享状态的并发”  
面对的问题是，在对回调函数中修改**当前**内存中的变量(状态)需要多加考虑  

```
<script>
    var arr = [1, 2, 3];
    function sevArr(){
        var html = "<p>" + arr.join("+") + "</p>";

        arr = []; // 数组置空

        return html;
    }
</script>
```

假设用户每次访问请求`/array`URL时，会执行`sevArr()`  

Node会这样处理，第一个请求是，带有数组用"+"连接后返回的字符串的完整html插入当前页面；第二个请求是，数组被置空。这里该注意的是，如果是php的话，两个请求都是完整html被插入页面  

这里，是因为node是采用一个长期运行的进程，相反，Apache会产生多个线程，且每次都是刷新状态，所以在php中解析器每次运行，变量`arr`都被重新赋值。但在node下，`sevArr`每次被调用，作用中的变量不受影响  

## 阻塞和非阻塞IO
在讨论前，先引入几个专名：单线程、任务队列、事件轮询(Event Loop)  

### 阻塞
因php写的服务器执行的是同步任务，会有阻塞线程的情况，如`sleep()`延迟函数会阻塞线程执行，当阻塞延迟时间未到，不会有任何操作，即同步的  

### 非阻塞
而node相反，非阻塞异步  
node使用的是**事件轮询**，与`sleep()`对应的`setTimeout()`在这里是非阻塞的  

### 单线程
js是单线程即一个时间内只能做一件事

### 任务队列
**任务队列**(task queue)  指一个事件的队列，一个消息队列  
其中，进入队列的事件包括：IO设备(键盘输入、显示器打印机输出等)的事件、用户事件(鼠标点击、页面滚动等)，凡是有回调函数，这些事件触发前就会进入任务队列，待主线程读取

因js为单线程，所以所有任务需要排队进行，这是CPU空闲  
所有任务分为*同步*和*异步*，同步指在主线程排队的任务需一个等一个结束地执行，而异步指任务不进入**主线程**，而是进入**任务队列**(task queue)

### 事件轮询
在node中，并发、timeout及http等原生模块的IO一样采用事件轮询  

而事件轮询又指的是，**主线程**由**任务队列**读取事件，整个过程循环不断，这个运行机制称Event Loop  

- 所有任务在主线程执行，从而形成一个**执行栈**
- 与主线程并存的一个**任务队列**(task queue)，当异步任务运行有结果，则在**任务队列**放置一个事件
- 当**执行栈**完成所有同步任务，则会接着读取**任务队列**，检查队列里哪些事件对应哪些异步任务，让事件进入**执行栈**并执行
- **主线程**循环不停以上三步操作 --> **事件轮询**

![Event Loop](http://image.beekka.com/blog/2014/bg2014100802.png)

如图所示，主线程运行后产生堆(heap)栈stack，栈中代码(同步任务)调用各种外部API，它们在**任务队列**加入各种对应的事件，待栈中代码执行完毕，主线程就读取**任务队列**(异步任务)中事件对应的回调函数

### node事件轮询
nodejs同为单线程事件轮询，其机制与浏览器有所不同，如图  

![NodeJs Event Loop](http://image.beekka.com/blog/2014/bg2014100803.png)

- 经Chrome V8引擎解析js
- 解析后代码经node的API
- 接着libuv库来负责node Api 执行，由它来将不同任务分发个不同线程，从而构成事件轮询，异步方式将任务执行结果又回到V8

### 定时器
来观察下**定时器**，原生js的setTimeout()和node的两个process.nextTick(func)及setImmediate(func)，同样控制其任务的执行顺序  

```

<script>
    /*
        setTimeout(func, 0)
        timer0和timer1属于同一级事件轮询
        timer0 --> timer1 --> timer2
    */
    setTimeout(function(){
     console.log("timer0");
     setTimeout(function(){
         console.log("timer2");
     }, 0);
    }, 0);

    setTimeout(function(){
      console.log("timer1");
    }, 0);

    /*
        process.nextTick(func)
        把原本应在第二次事件轮询的timer2
        放到了第一次事件轮询上
        因此 timer0 --> timer2 --> timer1
    */
    process.nextTick(function A(){
     console.log("timer0");
     process.nextTick(function B(){
         console.log("timer2");
     });
    });

    setTimeout(function(){
      console.log("timer1");
    }, 0);

    /*
        setImmediate(func)
        把事件注册到下一次事件轮询上
        因此 timer0 --> timer1 --> timer2
    */
    setImmediate(function(){
        setImmediate(function A(){
            console.log("timer0");
            setImmediate(function B(){
                console.log("timer2");
            });
        });

        setTimeout(function timeout(){
            console.log("timer1");
        }, 0);
    });
</script>

```

从代码可知，  `process.nextTick(func)`  可让指定任务在所有异步任务之前发生，会在*下一次事件轮询前*触发回调  
而  `setImmediate(func)`  可让指定任务在*下一次事件轮询时*发生，类似于  `setTimeout(func, 0)`  

- 小结  
`process.nextTick(func)`  指定的回调在本次事件轮询触发，  `setImmediate(func)`  指定的回调在下次事件轮询触发，因此前者比后者发送早，效率高，不用检查队列  

## 错误处理
node对于错误处理格外重视，一旦遗漏某个错误，当发生后会很难追踪，因调用信息的上下文丢失  

因node应用置于一个拥有大量**共享状态**的大进程里，所以一旦如HTTP请求的某个回调发生错误，整个进程崩溃  

```
<script>
    var http = require('http');
    http.createServer(function(){
     throw new Error("错误未被捕获！");
    }).listen(8080);
</script>
```

代码所示错误未经  `try{} catch(e){}`  来捕获，当访问服务器进程崩溃  

```
 Error: 错误未被捕获！
   at Server.<anonymous> (..\deal-error.js:3:8)
   at emitTwo (events.js:87:13)
   at Server.emit (events.js:172:7)
   at HTTPParser.parserOnIncoming [as onIncoming] (_http_server.js:533:12)
   at HTTPParser.parserOnHeadersComplete (_http_common.js:103:23)
```

- uncaughtException事件  
使用该事件处理器后，未被捕获的错误将不会导致进程崩溃，且后续操作可控  

```
<script>
    var http = require('http');
    http.createServer(function(){
        throw new Error("错误未被捕获！");
    }).listen(8080);
    process.on('uncaughtException', function(err){
        console.error(err);
        process.exit(1); // 手动关闭进程
    });
</script>

```

## 堆栈跟踪
堆栈跟踪指的是错误发生后，在后台显示的一系列函数调用信息，如  

```
<script>
    function c(){
        b();
    };
    function b(){
        a();
    };
    function a(){
        /*
            未引入捕获代码块
            进程将会直接中断
        */
        throw new Error('here');
    };
    c();
</script>
```

后台显示的调用信息，从下至上可发现其调用路径  

```
Error: here
    at a (..\stack-trace.js:10:8)
    at b (..\stack-trace.js:6:2)
    at c (..\stack-trace.js:2:2)
    at Object.<anonymous> (..\stack-trace.js:13:1)
    at Module._compile (module.js:409:26)
    at Object.Module._extensions..js (module.js:416:10)
    at Module.load (module.js:343:32)
    at Function.Module._load (module.js:300:12)
    at Function.Module.runMain (module.js:441:10)
    at startup (node.js:139:18)
```

而当引入**定时器**后，将会*增多一次事件轮询*  

```
<script>
    function c(){
        b();
    };
    function b(){
        a();
    };
    function a(){
        try{
            /*
                引入捕获代码块
                进程不中断且后续操作可控
            */
            setTimeout(function(){
                throw new Error('here');
            }, 10);
        }catch(e){
            console.log('捕获到错误！' + e);
        }
    };
    c();
</script>
```

```
Error: here
    at null._onTimeout (..\stack-trace.js:12:10)
    at Timer.listOnTimeout (timers.js:92:15)
```

增多一次轮询将会丢失一部分有价值的函数调用信息  