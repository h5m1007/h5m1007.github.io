---
layout: post
title: "git命令 全记录"
date: 2016-10-16 15:37
categories: javascript requireJs
tags: git git命令
---



版本管理神器 Git




## 什么是Git
Git 作为一个源码管理系统，致力于分享精神，有利于团队合作，极大方便了独立开发者和公司项目的管理  

## 命令提交姿势

```
// 在新目录根目录下
git add .  // 把根目录的所有文件加入待提交的空间并生成.git隐藏文件夹
git status  // 在提交修改等操作后查看状态 
git commit -m "xxx"  // 为每个提交的文件写个备注
git remote add origin http:xxx  // 指明要提交的仓库地址 前提已经创建了该仓库
git pull origin master  // 提交前可先拉取该仓库的文件并和本地合并
git push -u origin master  // 一切就绪就推送到仓库的主分支master吧

```

## 分支管理姿势

```
git branch  // 返回所有本地分支
git branch -r  // 返回所有远程分支
git branch -a  // 返回所有本地和远程分支

git branch (分支名)  // 新建分支
git checkout (分支名)  // 切换到指定的分支

```

## 删除姿势

```
git rm .  // 把所在位置的文件全部删除(注意命令中的.字符表示全部文件)
```

