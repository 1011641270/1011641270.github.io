---
layout: post
title: SpringMVC实现简单的文件上传
category: 技术
comments: true
---


```java
UploadController：
@Controller
@RequestMapping("/testSSH")
public class UploadController {
 @RequestMapping(value = "/upload.do")
 public String upload(
   @RequestParam(value = "file", required = false) MultipartFile file,
   HttpServletRequest request, ModelMap model) {
  System.out.println("上传文件");
  String path = request.getSession().getServletContext()
    .getRealPath("upload");
  String fileName = file.getOriginalFilename();
  System.out.println("fileName:"+fileName);
  System.out.println("path:"+path);
  File targetFile = new File(path, fileName);
  String filet = file.getContentType();
  if (!targetFile.exists()) {
   targetFile.mkdirs();
  }
  try {
   file.transferTo(targetFile);
  } catch (Exception e) {
   e.printStackTrace();
  }
  model.addAttribute("fileUrl", request.getContextPath() + "/upload/"
    + fileName);
  return "result";
 }
```

```java
<%@ page language="java" contentType="text/html; charset=UTF-8"
 pageEncoding="UTF-8"%>
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
<title>上传文件</title>
</head>
<body>
<form action="testSSH/upload.do" method="post" enctype="multipart/form-data">  
<input type="file" name="file" /> <input type="submit" value="Submit" /></form>  
</body>
</html>
```

```java
<%@ page pageEncoding="utf-8"%>  
<!DOCTYPE html>  
<html>  
<head>  
<meta charset="utf-8">  
<title>上传结果</title>  
</head>  
<body>  
<img alt="" src="${fileUrl}" />  
</body>  
</html>
```
