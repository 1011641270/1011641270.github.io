---
layout: post
title: Ubuntu安装Hadoop
category: 技术
comments: true
---

1、下载hadoop，解压

 这个可以到[http://hadoop.apache.org/](http://hadoop.apache.org/)里面找到
 
2、配置.bashrc文件

![ui-demo](http://static.oschina.net/uploads/space/2015/0509/120837_UeQk_1863482.png)

3、创建hadoop数据保存

 我创建在了hadoop文件里，名字叫做data
 
4、配置hadoop-env.sh文件

![ui-demo](http://static.oschina.net/uploads/space/2015/0509/121131_87HI_1863482.png)

5、配置mapred-site.xml文件

  首先，在/home/tian/Downloads/hadhoop/hadoop-2.6.0/etc/hadoop文件夹里面有个mapred-queues.xml.template文件，我们需要复制一份，名字叫做mapred-site.xml

  ![ui-demo](http://static.oschina.net/uploads/space/2015/0509/121345_0nup_1863482.png)
  
6、配置core-site.xml文件

  ![ui-demo](http://static.oschina.net/uploads/space/2015/0509/121715_AONj_1863482.png)
  
7、配置yarn-site.xml文件

 ![ui-demo](http://static.oschina.net/uploads/space/2015/0509/121531_v2nk_1863482.png)
 
8、配置hdfs-site.xml文件

 ![ui-demo](http://static.oschina.net/uploads/space/2015/0509/121620_qK4l_1863482.png)
 
9、设置面密码登录

 ![ui-demo](http://static.oschina.net/uploads/space/2015/0509/121823_3Om1_1863482.png)
 
10、格式化hadoop数据

 ![ui-demo](http://static.oschina.net/uploads/space/2015/0509/122015_UfBZ_1863482.png)
 
11、启动hadoop

 ![ui-demo](http://static.oschina.net/uploads/space/2015/0509/122139_UBRj_1863482.png)
 
12、查看hadoop

 http://127.0.0.1:50070

 ![ui-demo](http://static.oschina.net/uploads/space/2015/0509/122347_EyBP_1863482.png)
