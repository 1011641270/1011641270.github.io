---
layout: post
title: hibernate session问题大总结
category: 技术
comments: true
---

## openSessionInView的作用！

OpenSessionInViewFilter是Spring提供的一个针对Hibernate的一个支持类，其主要意思是在发起一个页面请求时打开Hibernate的Session，一直保持这个Session，直到这个请求结束，具体是通过一个Filter来实现的。由于Hibernate引入了Lazy Load特性，使得脱离Hibernate的Session周期的对象如果再想通过getter方法取到其关联对象的值，Hibernate会抛出一个LazyLoad的Exception。所以为了解决这个问题，Spring引入了这个Filter，使得Hibernate的Session的生命周期变长。

## hibernate通过sessionFactory有两种方式获取session：一种是openSession，一种是getCurrentSession

openSession创建session时autoCloseSessionEnabled参数为false，即在事物结束后不会自动关闭session， 那么必须显示的关闭session，也就是调用session.close()方法。这样commit后，session并没有关闭，如果不关闭将导致session关联的数据库连接无法释放，最后资源耗尽而使程序当掉。    

getCurrentSession创建session时autoCloseSessionEnabled，flushBeforeCompletionEnabled都为true，并且session会同sessionFactory组成一个map以sessionFactory为主键绑定到当前线程。Session 在第一次被使用的时候，即第一次调用getCurrentSession()的时候，其生命周期就开始。然后她被Hibernate绑定到当

前线程。当事物结束的时候，不管是提交还是回滚，Hibernate会自动把Session从当前线程剥离，并且关闭。若在次调用 getCurrentSession()，会得到一个新的Session,并且开始一个新的工作单元。这是Hibernate最广泛的thread- bound model，支持代码灵活分层(事物划分和数据访问代码的分离)。用户可以不再考虑session的管理,事务的开启关闭.只需配置事务即可.
 <property name="current_session_context_class">thread</property>

## hibernate session为什么线程不安全? 解决方案？

[http://fengbin2005.iteye.com/blog/1160428](http://fengbin2005.iteye.com/blog/1160428)

其实ThreadLocal并非是一个线程的本地实现版本，它并不是一个Thread，而是thread local variable(线程局部变量)。也许把它命名为ThreadLocalVar更加合适。线程

局部变量(ThreadLocal)其实的功用非常简单， 就是为每一个使用该变量的线程都提供一个变量值的副本，是每一个线程都可以独立地改变自己的副本，而不会和其它线程的副本冲突。从线程的角度看，就好像每 一个线程都完全拥有一个该变量。

## hibernate  session与事物的关联。

[http://aixiangct.blog.163.com/blog/static/9152246120113652732924/](http://aixiangct.blog.163.com/blog/static/9152246120113652732924/)
