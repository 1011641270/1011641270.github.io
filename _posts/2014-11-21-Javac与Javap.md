---
layout: post
title: Javac与Javap
category: 技术
comments: true
---

源代码：

```java
public class JavaSampleAppend {
 /**
  * 此代码只是简单的加法，在命令行中，编译后
  * 使用 javap -verbose chapter2.JavaSampleAppend
  * 即可得到编译后的指令集，反编译工具也是这样的道理
  * @param args
  */
 public static void main(String []args) {
  int a = 2;//[-1~5]用iconst_m1、iconst_[0-5]、否则用bipush指令
  int b = 2;
  int c = a + b;
 }
  
 /**
  * 同样的代码对比一下字节码，要多一个本地变量this
  */
 public void test() {
  int a = 2;
  int b = 2;
  int c = 100;
  int d = 100;
  int e = 100;
  int f = 100;
  int g = 100;
  int m = 100;
  int t = 100;
  int ff = 100;
  int aaa = 100;
  int bb = 100;
  int x = 100;
  int y = 100;
  //int c = a + b;
 }
  
 /**
  * 同样的代码对比一下字节码
  */
 public static void testStatic() {
  int a = 2;//[-1~5]用iconst_m1、iconst_[0-5]、否则用bipush指令
  int b = 2;
  int c = a + b;
 }
}
```

然后：执行javac，javap指令，得到字节码和指令【这里要注意你的java文件的路径】：

![ui-demo](https://github.com/1011641270/blog/blob/gh-pages/images/019.jpg?raw=true)

然后就可以查看指令了：

![ui-demo](https://github.com/1011641270/blog/blob/gh-pages/images/020.jpg?raw=true)

然后用打开字节码的工具查看编译后的代码：

```java
public class JavaSampleAppend
{
  public static void main(String[] paramArrayOfString)
  {
    int i = 2;
    int j = 2;
    int k = i + j;
  }
  public void test()
  {
    int i = 2;
    int j = 2;
    int k = 100;
    int l = 100;
    int i1 = 100;
    int i2 = 100;
    int i3 = 100;
    int i4 = 100;
    int i5 = 100;
    int i6 = 100;
    int i7 = 100;
    int i8 = 100;
    int i9 = 100;
    int i10 = 100;
  }
  public static void testStatic()
  {
    int i = 2;
    int j = 2;
    int k = i + j;
  }
}
```
