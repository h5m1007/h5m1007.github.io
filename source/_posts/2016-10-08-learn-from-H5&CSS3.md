---
layout: post
title: "HTML5和CSS3实例教程 学习笔记"
date: 2016-10-08 11:00
categories: note HTML5 CSS3
tags: h5 css3
---



《HTML5 and CSS3》译名《HTML5和CSS3实例教程》




## CSS3新属性  
- border-radius  实现圆角

```
border-radius: 5px;
-moz-border-radius: 5px;
-webkit-border-radius: 5px;
```

- linear-gradient(起始点,起始颜色,结束颜色)  实现渐变  

```
background-image: -moz-linear-gradient(top, #fff, #efefef);
background-image: -webkit-gradient(
  linear, left top, left bottom,
  color-stop(0, #fff),
  color-stop(1, #efefef)
);
background-image: linear-gradient(top, #fff, #efefef);
```

- box-shadow  实现阴影，接受4个参数，第一个参数水平偏移量，第二个垂直，第三个模糊半径，最后一个阴影颜色  

```
-moz-box-shadow: 5px 5px 5px #333;
-webkit-box-shadow: 5px 5px 5px #333;
-o-box-shadow: 5px 5px 5px #333;
box-shadow: 5px 5px 5px #333;
```

- transform  实现旋转，值有rotate(角度)中心旋转，`rotate(360deg)`  

```
-moz-transform: rotate(-7.5deg);
-o-transform: rotate(-7.5deg);
-webkit-transform: rotate(-7.5deg);
-ms-transform: rotate(-7.5deg);
transform: rotate(-7.5deg);
```

- @font-face  实现自定义字体  

```
@font-face{
  font-family: 'GarogierRegular'; /*自定义字体名*/
  src: url('fonts/Garogier_unhinted-webfont.eot'); /*for IE*/ 
  src: url('fonts/Garogier_unhinted-webfont.woff') format('woff'), 
  url('fonts/Garogier_unhinted-webfont.ttf') format('truetype'),
  url('fonts/Garogier_unhinted-webfont.svg#webfontew0qE009') format('svg'); /*for IOS*/ 
  font-weight: normal;
}

body{
  font-family: 'GarogierRegular'; /*使用自定义字体*/
}
```

## 跨域信息交互与离线应用
离线存储，大多数使用cookie，一般会应用在客户端和服务器数据共享，如维护web请求间的状态信息等，除了以上这些，一些本地设置如对网站的外观设置、参数设置等，可通过html5提供的Web Storage(localStorage或sessionStorage)和Web SQL Databases来完成  

- localStorage  
当浏览器关闭后，本地保存的数据**依旧存在**

```
localStorage.setItem('name', value); //以键值对方式保存
localStorage.getItem('name'); // 取得保存的键值
```

- sessionStorage  
当浏览器关闭后，本地保存的数据被**销毁**

```
sessionStorage.setItem('name', value); //以键值对方式保存
sessionStorage.getItem('name'); // 取得保存的键值
```

- Web SQL Databases  

连接数据库  

```
var db = null;
function func(){
  db = window.openDatabase(
    'awesome_notes', // 数据库名称
    '1.0', // 版本号
    'AwesomeNotes Database', // 描述
    1024*1024*3 // 描述大小
  );
}
```

  创建数据库表  

```
function func(){
  // 访问数据库格式
  db.transaction(function(tx){
    // 在事务中触发SQL
    tx.executeSql(
      // 查询语句
      "CREATE TABLE notes (id INTEGER \
          PRIMARY KEY, title TEXT, note TEXT)",
      [],
      function(){
        // 执行成功
        alert('Notes database created successfully!');
      },
      function(tx, error){
        // 执行失败
        alert(error.message);
      }
    );
  });
}
```

  查询数据库(查询整张表)

```
function func(){
  db.transaction(function(tx){
    tx.executeSql(
      'SELECT id, title, note FROM notes',
      [],
      function(SQLTransaction, data){
        for(var i = 0; i < data.rows.length; i++){
          var row = data.rows.item(i),
          id = row['id'],
          title = row['title'];

          // 获取数据加载到页面
          addToNotesList(id, title);
        }
      }
    );
  });
}
```

  查询数据库(根据id查询某特定记录)

```
function func(id){
  db.transaction(function(tx){
    tx.executeSql(
      // ? 问号占位符 由第二个参数数组值填充
      'SELECT id, title, note FROM notes where id = ?',
      [id],
      function(SQLTransaction, data){
        var row = data.rows.item(0);

        title.value = row["title"];
        title.setAttribute("data-id", row["id"]);
        note.value = row["note"];
        document.getElementById("delete_button").style.display = 'inline';
      }
    );
  });
}
```

  插入新记录  

```
function func(title, note){
  db.transaction(function(tx){
    tx.executeSql(
      "INSERT INTO notes (title, note) VALUES (?, ?)",
      [title.value, note.value],
      function(tx, result){
        // 经插入操作得到的结果集result
        // 使用结果集的insertId属性获取插入记录的id
        var id = result.insertId;

        alert("Record " + id + " saved!");
        title.setAttribute("data-id", result.insertId);
        addToNotesList(id, title.value);
        document.getElementById("delete_button").style.display = 'inline';

      },
      function(){
        alert("The note could not be saved");
      }
    );
  });
}
```

  更新数据库  

```
function func(title, note){
  var id = title.getAttribute("data-id");
  db.transaction(function(tx){
    tx.executeSql(
      "UPDATE notes set title = ?, note = ? where id = ?",
      [title.value, note.value, id],
      function(tx, result){
        alert("Record " + id + " updated!");
        document.querySelector("#notes>li[data-id = '" + id + "']").innerHTML = title.value;
      },
      function(){
        alert("The note was not updated!");
      }
    );
  });
}
```

  删除旧记录

```
function func(){
  var id = title.getAttribute("data-id");
  db.transaction(function(tx){
    tx.executeSql(
      "DELETE from notes where id = ?",
      [id],
      function(tx, result){
        alert("Record " + id + " deleted!");
        document.querySelector("#notes>li[data-id = '" + id + "']").remove();
        title.value = "";
        note.value = "";
      },
      function(){
        alert("The note was not deleted!");
      }
    );
  });
}
```

- History  管理浏览器历史记录  
window.history.pushState(后续交互对象，历史记录项标识)  执行window对象history的pushStare，可以向浏览器添加历史状态  
在点击后退按钮时，页面返回之前点击显示的内容，在这种情况中，window.history得到应用，如下代码：  

```
var stateObj = {tab: tab};
window.history.pushState(stateObj, tab);

window.onpopstate = function(e){
  <!-- 为window后退事件onpopstate注册侦听器 -->
  if(e.state){
    var tab = (e.state["tab"]);
    $(".visible")
      .addClass("hidden")
      .removeClass("visible")
      .hide();
    $(tab)
      .removeClass("hidden")
      .addClass("visible")
      .show();
  }
}

```

- 跨文档消息机制  
Cross-document Messaging  可让处于不同域的脚本具备相互传递消息的功能  

使用postMessage(消息，目标地址)方法可让跨域在两个窗口或iframe间发送数据信息，类似于跨域ajax但不同的是**它不是浏览器与服务器间的交互，而是两客户端间的通信**  

应用：假设有两页面http://www.test.html和http://test.html，两页面主机名不同  

```
<!-- http://www.test.html -->
<script>
  window.postMessage(msg, "index2.html");
</script>

<!-- http://test.html -->
<script>
  window.onmessage = function(event){
    // 当成功接收页面index1发送消息时
    // 触发message事件
    console.log(event.source);  // 输出消息发送的窗口信息
    console.log(event.origin); // 输出消息发送方地址即index1.html
    console.log(event.data); // 输出消息发送过来的数据即msg
  }
</script>
```

- Web Sockets  
工作方式：向服务器发起一个连接，接着监听服务器发回的事件并做适当响应  
1. `onopen() // 与服务器的连接建立后触发该事件`  
2. `onmessage() // 与服务器的连接发送消息触发该事件`  
3. `onclose() // 与服务器的连接丢失或关闭时触发该事件`  
