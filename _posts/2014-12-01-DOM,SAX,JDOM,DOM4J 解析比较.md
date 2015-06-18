---
layout: post
title: DOM,SAX,JDOM,DOM4J 解析比较
category: 技术
comments: true
---

公有部分：

books.xml

```java
<?xml version="1.0" encoding="UTF-8"?>
<bookstore>
    <book id="1">
        <name>西游记</name>
        <author>吴承恩</author>
        <year>19XX</year>
    </book>
    <book id="2">
        <name>冰与火之歌</name>
        <year>2014</year>
        <author>乔治马丁</author>
        <price>11</price>
    </book>
</bookstore>
```

Book类：

```java
public class Book {
 private String id;
 private String name;
 private String author;
 private String year;
 private String price;
  
 public String getId() {
  return id;
 }
 public void setId(String id) {
  this.id = id;
 }
 public String getName() {
  return name;
 }
 public void setName(String name) {
  this.name = name;
 }
 public String getAuthor() {
  return author;
 }
 public void setAuthor(String author) {
  this.author = author;
 }
 public String getYear() {
  return year;
 }
 public void setYear(String year) {
  this.year = year;
 }
 public String getPrice() {
  return price;
 }
 public void setPrice(String price) {
  this.price = price;
 }
  
}
```

## DOM解析：

```java
public class DOMTest {
 public static void main(String[] args) {
  DocumentBuilderFactory dbf = DocumentBuilderFactory.newInstance();
  try {
   DocumentBuilder db = dbf.newDocumentBuilder();
   Document document = db.parse("books.xml");
   NodeList bookList = document.getElementsByTagName("book");
   System.out.println("一共有" + bookList.getLength() + "本书");
   for (int i = 0; i < bookList.getLength(); i++) {
    System.out.println("=================下面开始遍历第" + (i + 1)
      + "本书的内容=================");
    // 解析属性值
    /*for (int k = 0; k < bookList.getLength(); k++) {
     Node book = bookList.item(k);
     NamedNodeMap attrs = book.getAttributes();
     for (int j = 0; j < attrs.getLength(); j++) {
      Node attr = attrs.item(j);
      System.out.print("属性名：" + attr.getNodeName());
      System.out.println("--属性值" + attr.getNodeValue());
     }
    }*/
    // 解析属性值
    Element book = (Element) bookList.item(i);
    String attrValue = book.getAttribute("id");
    System.out.println("id属性的属性值为" + attrValue);
    NodeList childNodes = book.getChildNodes();
    for (int k = 0; k < childNodes.getLength(); k++) {
     if (childNodes.item(k).getNodeType() == Node.ELEMENT_NODE) {
      System.out.print("节点名："
        + childNodes.item(k).getNodeName());
      /**
       * 请注意，这里还使用了一个getFirstChild()方法来获得message下面的第一个子Node对象。虽然在message标签下面除了文本外并没有其它子标签或者属性，
       * 但是我们坚持在这里使用getFirseChild()方法，这主要和W3C对DOM的定义有关。W3C把标签内的文本部分也定义成一个Node，所以先要得到代表文本的那个Node，
       * 我们才能够使用getNodeValue()来获取文本的内容。 
       */
       
      System.out.println("--节点值是："
        + childNodes.item(k).getFirstChild()
          .getNodeValue());
      /* System.out.println("--节点值是：" +
       childNodes.item(k).getTextContent());*/
     }
    }
    System.out.println("======================结束遍历第" + (i + 1)
      + "本书的内容=================");
   }
  } catch (ParserConfigurationException e) {
   e.printStackTrace();
  } catch (SAXException e) {
   e.printStackTrace();
  } catch (IOException e) {
   e.printStackTrace();
  }
 }
```

结果：

```java
一共有2本书
=================下面开始遍历第1本书的内容=================
id属性的属性值为1
节点名：name--节点值是：西游记
节点名：author--节点值是：吴承恩
节点名：year--节点值是：19XX
======================结束遍历第1本书的内容=================
=================下面开始遍历第2本书的内容=================
id属性的属性值为2
节点名：name--节点值是：冰与火之歌
节点名：year--节点值是：2014
节点名：author--节点值是：乔治马丁
节点名：price--节点值是：11
======================结束遍历第2本书的内容=================
```

## SAX解析：

```java
	public class SAXParserHandler extends DefaultHandler {
 String value = null;
 Book book;
 private ArrayList<Book> bookList = new ArrayList<Book>();
  
 public ArrayList<Book> getBookList() {
  return bookList;
 }
  
 // 遍历xml文件的开始标签
 @Override
 public void startElement(String uri, String localName, String qName,
   Attributes attributes) throws SAXException {
  // TODO Auto-generated method stub
  super.startElement(uri, localName, qName, attributes);
  // 解析book元素
  if (qName.equals("book")) {
   // 已知book属性的名称
   /*
    * String value = attributes.getValue("id");
    * System.out.println("book属性值：" + value);
    */
   book = new Book();
   int num = attributes.getLength();
   for (int i = 0; i < num; i++) {
     
    System.out.println("属性名：" + attributes.getQName(i));
    System.out.println("属性值：" + attributes.getValue(i));
     if(attributes.getQName(i).equals("id")){
      book.setId(attributes.getValue(i));
     }
   }
  }else if (!qName.equals("bookstore")) {
   System.out.print("节点名是：" + qName + "---");
  }
 }
 // 遍历xml文件的结束标签
 @Override
 public void endElement(String uri, String localName, String qName)
   throws SAXException {
  // TODO Auto-generated method stub
  super.endElement(uri, localName, qName);
  //判断结束节点
  if(qName.equals("book")){
   bookList.add(book);
   System.out.println("结束遍历");
  }else if(qName.equals("name")){
   book.setName(value);
  }else if(qName.equals("author")){
   book.setAuthor(value);
  }else if(qName.equals("year")){
   book.setYear(value);
  }else if(qName.equals("price")){
   book.setPrice(value);
  }
 }
 // 标示解析开始
 @Override
 public void startDocument() throws SAXException {
  // TODO Auto-generated method stub
  super.startDocument();
   
  System.out.println("解析开始");
 }
 // 标示解析结束
 @Override
 public void endDocument() throws SAXException {
  // TODO Auto-generated method stub
  super.endDocument();
  System.out.println("解析结束");
 }
 @Override
 public void characters(char[] ch, int start, int length)
   throws SAXException {
  // TODO Auto-generated method stub
  super.characters(ch, start, length);
  value = new String(ch,start,length);
    if(!value.trim().equals("")){
     System.out.println(value.trim());
    }
 }

public class SAXTest {
 public static void main(String[] args) throws ParserConfigurationException, SAXException {
  SAXParserFactory saxParserFactory = SAXParserFactory.newInstance();
  SAXParser saxParser = saxParserFactory.newSAXParser();
  SAXParserHandler handler = new SAXParserHandler();
  try {
   saxParser.parse("books.xml", handler);
   System.out.println(handler.getBookList().size() + "本书");
   for(int i=0;i<handler.getBookList().size();i++){
    System.out.println("第"+i+"本书");
    System.out.println(handler.getBookList().get(i).getId());
    System.out.println(handler.getBookList().get(i).getAuthor());
    System.out.println(handler.getBookList().get(i).getName());
    System.out.println(handler.getBookList().get(i).getPrice());
    System.out.println(handler.getBookList().get(i).getYear());
   }
  } catch (IOException e) {
   // TODO Auto-generated catch block
   e.printStackTrace();
  }
 }
```
结果：

```java
解析开始
属性名：id
属性值：1
节点名是：name---西游记
节点名是：author---吴承恩
节点名是：year---19XX
结束遍历
属性名：id
属性值：2
节点名是：name---冰与火之歌
节点名是：year---2014
节点名是：author---乔治马丁
节点名是：price---11
结束遍历
解析结束
2本书
第0本书
1
吴承恩
西游记
null
19XX
第1本书
2
乔治马丁
冰与火之歌
11
2014
```

## JDOM解析：

```java
public class JDOMTest {
 public static void main(String[] args) {
  SAXBuilder saxBuilder = new SAXBuilder();
  InputStream inputStream;
  try {
   ArrayList<Book> arrayList = new ArrayList<Book>();
   inputStream = new FileInputStream("books.xml");
   Document document = saxBuilder.build(inputStream);
   // 得到根节点
   Element element = document.getRootElement();
   List<Element> list = element.getChildren();
   for (Element book : list) {
    Book jdom_book = new Book();
    System.out.println("第" + list.indexOf(book) + "本书");
    List<Attribute> attributes = book.getAttributes();
    for (Attribute attribute : attributes) {
     System.out.print("属性名：" + attribute.getName() + " ");
     System.out.println("属性值：" + attribute.getValue() + " ");
     if (attribute.getName().equals("id")) {
      jdom_book.setId(attribute.getName());
     }
    }
    List<Element> boolChilds = book.getChildren();
    for (Element element2 : boolChilds) {
     System.out.print("节点名：" + element2.getName() + " ");
     System.out.println("节点值：" + element2.getValue() + " ");
     if (element2.getName().equals("name")) {
      jdom_book.setName(element2.getName());
     }
     if (element2.getName().equals("author")) {
      jdom_book.setAuthor(element2.getName());
     }
     if (element2.getName().equals("year")) {
      jdom_book.setYear(element2.getName());
     }
     if (element2.getName().equals("price")) {
      jdom_book.setPrice(element2.getName());
     }
    }
    arrayList.add(jdom_book);
   }
    
   System.out.println(arrayList.size());
  } catch (Exception e) {
   // TODO: handle exception
   e.printStackTrace();
  }
 }
```

结果：

```java
第0本书
属性名：id 属性值：1 
节点名：name 节点值：西游记 
节点名：author 节点值：吴承恩 
节点名：year 节点值：19XX 
第1本书
属性名：id 属性值：2 
节点名：name 节点值：冰与火之歌 
节点名：year 节点值：2014 
节点名：author 节点值：乔治马丁 
节点名：price 节点值：11 
2
```
DOM4J解析：

```java
public class DOM4JTest {
 public static void main(String[] args) {
  SAXReader reader = new SAXReader();
  try {
   Document document = reader.read("books.xml");
   Element element_root = document.getRootElement();
   Iterator iterator = element_root.elementIterator();
   while (iterator.hasNext()) {
    Element book = (Element) iterator.next();
    List<Attribute> list = book.attributes();
    for (Attribute attribute : list) {
     System.out.print("节点名：" + attribute.getName() + " ");
     System.out.println("节点值：" + attribute.getValue() + " ");
    }
    Iterator itt = book.elementIterator();
    while (itt.hasNext()) {
     Element element = (Element) itt.next();
     System.out.print("节点名：" + element.getName() + " ");
     System.out.println("节点值：" + element.getStringValue());
    }
   }
  } catch (Exception e) {
   // TODO: handle exception
   e.printStackTrace();
  }
 }
```

结果：

```java
节点名：id 节点值：1 
节点名：name 节点值：西游记
节点名：author 节点值：吴承恩
节点名：year 节点值：19XX
节点名：id 节点值：2 
节点名：name 节点值：冰与火之歌
节点名：year 节点值：2014
节点名：author 节点值：乔治马丁
节点名：price 节点值：11
```

## 总结：

DOM解析：

形成树结构，直观好理解，代码更易编写<br>
解析过程中树结构保留在内存中，方便修改<br>
当xml文件较大的时候，对内存消耗比较大，容易影响解析性能并造成内存溢出<br>

SAX解析：

一行一行地解析 触发事件。。  调用不同的方法<br>
对内存消耗比较小<br>
适用于只需要处理xml中数据时候<br>
不易编码<br>
很难同时访问同一个xml中多处不同的数据<br>

JDOM解析：
 
仅使用具体类而不是用接口<br>
API大量使用Collections类<br>

DOM4J:
现在比较流行的一种解析xml文件的方式<br>
jdom的一种智能分支，他合并了许多超出基本xml表示的功能<br>
dom4j使用接口和抽象类方法，是一种优秀的JavaxmlAPI<br>
具有性能优异、灵活性号、功能强大和极易用和使用的特点<br>
是一种开源代码的软件<br>
