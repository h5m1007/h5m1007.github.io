---
layout: post
title: "online resume record"
date: 2016-11-05 10:03
categories: javascript gulp jade
tags: javascript gulp jade
---



在线简历构建过程记录




## Jade
...占位

### include && extends

- `include`  引入页面补充部分，哪个位置缺某部分，包含进来作补充
- `extends`  先给出整体，再替换局部

include应用  

```
  // index.jade
  doctype html
  html
      include header
      body
          h1 welcome xxx
          p content
      include footer
```

extends应用  

```
  // layout.jade
  doctype html
  html
      head
          block title
              title Default Title
      body
          block content

  // index.jade
  extends layout
  block title
      title New Title
  block content
      h1 new title
      p new content
```

## Gulp

### 用法

- gulp执行指定任务并回调  

```
<script>
    gulp.task('build', ['task1', 'task2'], function(){
        // 只执行任务build
        // 同时会执行task1, task2
        gulp.src('src') // 将返回对象流
            .pipe(plugin1());
    });
    gulp.task('task1', function(){
        gulp.src('src1')
            .pipe(plugin1());
    });
    gulp.task('task2', ['task1'], function(){
        // 可让task1执行完
        // 再执行task2
        gulp.src('src2')
            .pipe(plugin2());
    });
</script>
```

`gulp task` 命令行执行任务名，直接执行指定任务  

*gulp.src()方法输入一个如匹配一个或多个文件的字符串，或者glob数组，然后返回一个可以传递给插件的数据流*  

`pipe()` 把**对象流**对接在插件上，并让插件处理对象  

- Plugins

`gulp-load-plugins`  该插件自动按需(当需被调用时才加载)加载 `paskage.json` 中Gulp插件，并把它们附加在一个对象上  

```
<script>
    var gulp = require('gulp'),
        gulpLoadPlugins = require('gulp-load-plugins'),
        plugins = gulpLoadPlugins();
    gulp.task('js', function () {
       return gulp.src('js/*.js')
          .pipe(plugins.jshint())
          .pipe(plugins.jshint.reporter('default'))
          .pipe(plugins.uglify())
          .pipe(plugins.concat('app.js'))
          .pipe(gulp.dest('build')) // 指定输出目录
          .pipe(gulp.livereload()); // 文件被修改自动加载刷新网页
    });
</script>
```

- Watch

`gulp.watch()`  监听文件修改状态，在文件被改动时执行一个或多个任务  

```
<script>
    var watcher = gulp.task('watch', function (e) {
       gulp.watch('xxx.scss', ['buildSass']);
       console.log('Event type: ' + e.type); // added, changed, or deleted
       console.log('Event path: ' + e.path); // The path of the modified file
    });
    watcher.on('change', function(){})
</script>
```

### Gulp && Grunt

#### 不同点
1. Gulp和Grunt的工作方式很不一样。Grunt不使用数据流，而是使用文件，对文件执行单个任务然后保存到新的文件中，每个任务都会重复执行所有进程，文件系统频繁的处理任务会导致Grunt的运行速度比Gulp慢  

2. Grunt任务拥有大量的配置，会引用大量你实际上并不需要的对象属性，但是Gulp里同样的任务也许只有几行而已  

3. Grunt比Gulp更加频繁地操作文件系统，所以使用数据流的Gulp总是比Grunt快。对于一个小的LESS文件，gulpfile.js通常需要6ms，而gruntfile.js则需要大概50ms——慢8倍多  

