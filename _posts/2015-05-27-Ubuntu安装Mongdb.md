---
layout: post
title: Ubuntu安装Mongdb,Robomongo
category: 技术
comments: true
---

## 1、安装Mongdb

```java
1.sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 7F0CEB10
2.sudo apt-get update
3.sudo apt-get install -y mongodb-org
```

启动(默认状态下安装好是启动成功了的)...

```java
sudo service mongod start
```
![ui-demo](https://github.com/1011641270/blog/blob/gh-pages/images/066.png?raw=true)

## 2、安装Robomongo

 网址：[http://robomongo.org/download.html](http://robomongo.org/download.html)
 
## 3、基本操作

![ui-demo](https://github.com/1011641270/blog/blob/gh-pages/images/067.png?raw=true)
 
 这个时候我们连接mongdb图形化操作工具，可以查看：
 
 ![ui-demo](https://github.com/1011641270/blog/blob/gh-pages/images/068.png?raw=true)
 
 ![ui-demo](https://github.com/1011641270/blog/blob/gh-pages/images/069.png?raw=true)

<font color="red">注意：默认情况下mongdb的端口是27017，如果我们没有设置数据库的话，默认会建立test数据库。默认数据会保存在/var/lib/mongodb里。</font>
 
