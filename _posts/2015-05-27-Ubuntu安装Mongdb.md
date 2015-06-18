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
![ui-demo](http://static.oschina.net/uploads/space/2015/0527/183551_IO6T_1863482.png)

## 2、安装Robomongo

 网址：[http://robomongo.org/download.html](http://robomongo.org/download.html)
 
## 3、基本操作

![ui-demo](http://static.oschina.net/uploads/space/2015/0527/184455_aJCQ_1863482.png)
 
 这个时候我们连接mongdb图形化操作工具，可以查看：
 
 ![ui-demo](http://static.oschina.net/uploads/space/2015/0527/184530_WMKS_1863482.png)
 
 ![ui-demo](http://static.oschina.net/uploads/space/2015/0527/184624_ADQS_1863482.png)

<font color="red">注意：默认情况下mongdb的端口是27017，如果我们没有设置数据库的话，默认会建立test数据库。默认数据会保存在/var/lib/mongodb里。</font>
 
