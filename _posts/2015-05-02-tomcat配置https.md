---
layout: post
title: Tomcat配置https
category: 技术
comments: true
---

1.进入jdk的安装目录，找到bin目录：

![ui-demo](https://github.com/1011641270/blog/blob/gh-pages/images/039.png?raw=true)

2.输入命令：

  keytool -v -genkey -alias tomcat -keyalg RSA -keystore D:\360downloads\tomcat7.0\tomcat.keystore  -validity 36500
  
     解释：1、默认情况下证书会保存90天，所以通过-validity 36500来增加证书的有效时间为100年；

           2、D:\360downloads\tomcat7.0\tomcat.keystore 为保存证书的路径和名称；

3.保存和生成证书：

![ui-demo](https://github.com/1011641270/blog/blob/gh-pages/images/040.png?raw=true)

     解释：名字与姓氏这个很重要，目前本地可以为localhost，如果像远程访问的话，可以为互联网可访问的iP地址！
     
4.修改tomcat配置文件
 <Connector SSLEnabled="true" clientAuth="false" keystoreFile="./conf/tomcat.keystore" keystorePass="123456" maxThreads="150" port="8443" protocol="org.apache.coyote.http11.Http11Protocol" scheme="https" secure="true" sslProtocol="TLS"/>
    解释：keystorePass="123456"为之前生成证书的时候自己制定的密钥
    
          keystoreFile="./conf/tomcat.keystore"制定证书路径（我把生成的证书复制到了tomcat的conf文件夹下了）
          
5.修改WEB工程的web.xml配置文件，实现自动由http协议跳转https协议

```java
	<login-config>
 <auth-method>CLIENT-CERT</auth-method>
 <realm-name>Client Cert Users-only Area</realm-name>
   </login-config>
<security-constraint>
 <web-resource-collection>
  <web-resource-name>SSL</web-resource-name>
  <url-pattern>/*</url-pattern>
 </web-resource-collection>
 <user-data-constraint>
  <transport-guarantee>CONFIDENTIAL</transport-guarantee>
 </user-data-constraint>
</security-constraint>
```

6.验证：打开工程，输入链接

 https://localhost:8443/IswustServer/getUser?user_id=1

![ui-demo](https://github.com/1011641270/blog/blob/gh-pages/images/041.png?raw=true)

至此，一个简单的https就配置成功了！
          
