---
layout: post
title: "javascript DOM高级程序设计 学习笔记"
date: 2016-08-30 18:37
categories: note javascript
tags: node对象
---



本地服务器搭建  
可选择有IIS、Apache、Xampp、APMServ...  
这次选择配置Apache + php(历经安装Xampp和APMServ后，发现apache始终启动不了，无奈只能直接来配置apache)




## 开始  
1. [下载地址](http://www.apachehaus.com/cgi-bin/download.plx)  
版本有2.4+ 安装类型为解压缩zip

2. 解压到指定位置后，打开目录下的X:\Apache24\conf\httpd.conf，找到内容为：

```
Define SRVROOT "X:/Apache24"
ServerRoot "${SRVROOT}"
```

然后再修改默认端口80为81或88等  

```
ServerName localhost:81
...
Listen 81
```

修改网站主目录  

```
#
# DocumentRoot: The directory out of which you will serve your
# documents. By default, all requests are taken from this directory, but
# symbolic links and aliases may be used to point to other locations.
#
DocumentRoot "X:/xxx"
<Directory "X:/xxx">
...
</Directory>
```

cmd控制台，跳转到目录X:\Apache24\bin\下，输入`httpd.exe -w -n 'apache' -k start`，可以安装apache服务到系统且可以发现安装后启动服务时出现的错误  

3. 配置php  
依旧打开X:\Apache24\conf\httpd.conf，在任意地方输入以下代码：  

```
# php5 support
LoadModule php5_module X:/php/php5apache2_4.dll
AddType application/x-httpd-php .php .html .htm
# configure the path to php.ini
PHPIniDir "X:/php"
```

其中，php5apache2_4.dll很重要，由它来解析php文件，没有它页面只能显示源代码









