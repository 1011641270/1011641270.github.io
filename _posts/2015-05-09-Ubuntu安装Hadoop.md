---
layout: post
title: Ubuntu安装Hadoop
category: 技术
comments: true
---

1、下载hadoop，解压

 这个可以到[http://hadoop.apache.org/](http://hadoop.apache.org/)里面找到
 
2、配置.bashrc文件

![ui-demo](https://github.com/1011641270/blog/blob/gh-pages/images/049.png?raw=true)

3、创建hadoop数据保存

 我创建在了hadoop文件里，名字叫做data
 
4、配置hadoop-env.sh文件

![ui-demo](https://github.com/1011641270/blog/blob/gh-pages/images/050.png?raw=true)

5、配置mapred-site.xml文件

  首先，在/home/tian/Downloads/hadhoop/hadoop-2.6.0/etc/hadoop文件夹里面有个mapred-queues.xml.template文件，我们需要复制一份，名字叫做mapred-site.xml

  ![ui-demo](https://github.com/1011641270/blog/blob/gh-pages/images/051.png?raw=true)
  
6、配置core-site.xml文件

  ![ui-demo](https://github.com/1011641270/blog/blob/gh-pages/images/052.png?raw=true)
  
7、配置yarn-site.xml文件

 ![ui-demo](https://github.com/1011641270/blog/blob/gh-pages/images/053.png?raw=true)
 
8、配置hdfs-site.xml文件

 ![ui-demo](https://github.com/1011641270/blog/blob/gh-pages/images/054.png?raw=true)
 
9、设置面密码登录

 ![ui-demo](https://github.com/1011641270/blog/blob/gh-pages/images/055.png?raw=true)
 
10、格式化hadoop数据

 ![ui-demo](https://github.com/1011641270/blog/blob/gh-pages/images/056.png?raw=true)
 
11、启动hadoop

 ![ui-demo](https://github.com/1011641270/blog/blob/gh-pages/images/057.png?raw=true)
 
12、查看hadoop

 http://127.0.0.1:50070

 ![ui-demo](https://github.com/1011641270/blog/blob/gh-pages/images/058.png?raw=true)
