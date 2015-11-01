title: "Git's Life Cycle - clone"
date: 2015-08-23 22:43:20
alias: 
tags: 
- 笔记 
- 工具
categories: 总结与分享

---


从这篇 git clone文章开始，我开始来倒腾git的这么几个常用的生命周期。从去年九月开始项目一直在用git，刚开始觉得git很好用，概念很清楚，用着用着没什么问题。随着项目人娄的增加以及项目规模慢慢变大，模块划分越来越清楚，也开始多出了几个仓库。。是有必要再次回顾git的这几条命令了。期间请教过几次同事，向她学习，现将自己一些总结作些记录。
## git clone语法

``` 
'git clone' [--template=<template_directory>]
	  [-l] [-s] [--no-hardlinks] [-q] [-n] [--bare] [--mirror]
	  [-o <name>] [-b <name>] [-u <upload-pack>] [--reference <repository>]
	  [--dissociate] [--separate-git-dir <git dir>]
	  [--depth <depth>] [--[no-]single-branch]
	  [--recursive | --recurse-submodules] [--] <repository>
	  [<directory>]
```
**`git clone`** - 已知一个远程仓库地址，从远程仓库中克隆一份到本地
## git clone 语法示例：
```
$ git clone http://linbao-lee.github.com/blog-src.git                  /*从远程仓库克隆不带分支名称的分支到当前目录下的my-linux目录*/
$ git clone -b branch-name http://linbao-lee.github.com/blog-src.git . /*克隆指定分支到当前目录*/
$ git clone -l -s -n . ../copy                                         /*拷贝当前目录下的分支到copy目录但不checkout这个分支*/

```
## 可被git接受的clone地址格式
``` java

ssh://[user@]host.xz[:port]/path/to/repo.git/   /*以user这个用户名去访问远程仓库*/
git://host.xz[:port]/path/to/repo.git/          /*git访问方式*/
http[s]://host.xz[:port]/path/to/repo.git/      /*http访问方式，所用协议不同而已，下同*/
ftp[s]://host.xz[:port]/path/to/repo.git/       /*ftp*/
rsync://host.xz/path/to/repo.git/
/path/to/repo.git/                              /*访问本地仓库的地址语法格式*/
\file:///path/to/repo.git/                      /*同上*/

```
## 参考文档
{% blockquote Git-SCM http://git-scm.com/docs/git-clone git-add官方文档%}
{% endblockquote %}
