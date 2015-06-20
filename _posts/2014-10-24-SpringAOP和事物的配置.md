---
layout: post
title: SpringAOP和事务配置
category: 技术
comments: true
---

![ui-demo](hhttps://github.com/1011641270/blog/blob/gh-pages/images/008.jpg?raw=true)

```java
<!--//对hibernate3进行事物的管理 -->
<bean id="transactionManager"
  class="org.springframework.orm.hibernate3.HibernateTransactionManager">
  <property name="sessionFactory">
   <ref local="sessionFactory" />
  </property>
 </bean>
```

```java
<!-- 配置事务的属性 对应的方法等等 AOP通知，事务的边界告诉方法，不是完整的事务切面。 -->
<tx:advice id="txAdvice" transaction-manager="transactionManager">
  <tx:attributes>
   <tx:method name="add*" propagation="REQUIRED" rollback-for="Exception" />
   <tx:method name="del*" propagation="REQUIRED" rollback-for="Exception" />
   <tx:method name="remove*" propagation="REQUIRED"
    rollback-for="Exception" />
   <tx:method name="update*" propagation="REQUIRED"
    rollback-for="Exception" />
   <tx:method name="modify*" propagation="REQUIRED"
    rollback-for="Exception" />
   <tx:method name="submit*" propagation="REQUIRED"
    rollback-for="Exception" />
   <tx:method name="insert*" propagation="REQUIRED"
    rollback-for="Exception" />
   <tx:method name="save*" propagation="REQUIRED"
    rollback-for="Exception" />
   <tx:method name="update*" propagation="REQUIRED"
    rollback-for="Exception" />
   <tx:method name="Insert*" propagation="REQUIRED"
    rollback-for="Exception" />
  </tx:attributes>
 </tx:advice>
```

```java
<!-- 配置哪些类哪些方法使用事务 proxy-target-class="true" spring的声明式事务（最常用） -->
<aop:config>
  <aop:pointcut id="allManagerMethod"
   expression="execution(* com.common.weixvn.service.*.impl.*.*(..))" />
  <aop:advisor advice-ref="txAdvice" pointcut-ref="allManagerMethod" />
 </aop:config> 
 <!-- advisor是一种特殊的Advice ,而Advice是Aspect里面的具体通知 -->
 <!-- 意思是： 在切入点的所有方法中，匹配切面的通知，完成事物 -->
```

## 总结：

AOP的主要编程对象是切面（aspect），横切关注点抽象（模块化）即为切面，它与类相似，只是两者的关注点不一样，类是对物体特征的抽象，而切面是横切性关注点的抽象。<br>
1.我们的DAO层在执行增删改查的时候需要进行事物的管理，而这个事物管理跟我们的业务流程是没有关系的，所以我可以把这个事物管理抽象出来。而这个事务管理的功能就是我们所说的横切关注点，是我们要关注的部分。<br>
2.我们把事物抽象出来，单独为一个类，这个类就叫做切面（Aspect）,切面是横切关注点的抽象画，模块化。<br>
3.在Aspect这个类里，可以写个方法，就是我们要具体执行事务的，这个方法就是我们定义的通知（Advice）,描述了具体要执行的工作。<br>
4.然后，我们还要有Pointcut，即我们定义的advice要执行的地方，在spring的事物管理中，切入点就是DAO层里面的增删改查等等操作方法，当然在切入点中还有一些要切入的语法。将这些比较公用的逻辑从业务主逻辑代码中划分出来，通过分离，我们可以将它们独立到非主导业务逻辑的单独方法中。<br>
   这样就把与对业务逻辑无关的逻辑部分进行隔离，从而改变这些行为的时候不影响业务逻辑的代码。使得业务逻辑各部分之间的耦合度降低，提高程序的可重用性，同时因为高复用而提高了开发的效率。



