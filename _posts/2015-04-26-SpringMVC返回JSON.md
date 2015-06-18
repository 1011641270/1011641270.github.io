---
layout: post
title: SpirngMVC返回JSON
category: 技术
comments: true
---

Spring MVC 3.0 返回JSON数据的方法<br>
1. 直接 PrintWriter 输出<br>
2. 使用 JSP 视图<br>
3. 使用Spring内置的支持<br>
// Spring MVC 配置

```java
<bean class="org.springframework.web.servlet.mvc.annotation.AnnotationMethodHandlerAdapter">
 <property name="messageConverters">
  <list>
   <bean
    class="org.springframework.http.converter.json.MappingJacksonHttpMessageConverter" />
  </list>
 </property>
</bean>
```
// WEB-INF/lib 中添加
jackson-mapper-asl-1.6.4.jar
jackson-core-asl-1.6.4.jar
// Controller 中 使用：@ResponseBody 标注方法，该方法返回 Java对象（支持复杂对象），MappingJacksonHttpMessageConverter 会将对象转换为 JSON 输出
4. 自定义<br>
// 以下是实现方法，仅仅是个演示
// 配置 BeanNameViewResolver

```java
<bean class="org.springframework.web.servlet.view.BeanNameViewResolver">
 <property name="order" value="1" />
</bean>
```
<bean id="jsonView" class="test.TestJsonView" />
// 实现 TestJsonView

```java
public class TestJsonView extends AbstractView {
 @Override
 protected void renderMergedOutputModel(Map<String, Object> model, HttpServletRequest request,
         HttpServletResponse response) throws Exception {
  JsonBuilder jb = new JsonBuilder();
  PrintWriter out = response.getWriter();
  out.print(jb.encode(model));
 }
}
```
// 配置 View
```java
<bean id="jsonView" class="test.TestJsonView" />
// Controller 中可以这样写：
ModelAndView mav = new ModelAndView("jsonView");
// mav.addObject ...
return mav;
```
5. 使用 spring-json.jar<br>
// 就是第4种方法，但是强大的多。。。
