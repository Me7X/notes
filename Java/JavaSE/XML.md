# XML解析

---

# **概述**

### **XML**

* 扩展名：.xml
* 作用：存储、配置
* 语法
    * 文档声明
        * 格式： ```<?xml version="1.0" encoding="UTF-8"?>```
* xml乱码

### **元素**

##### **命名规范**

* 区分大小写
* 不能以数字或"_"开头
* 不能以xml开头
* 不能包含空格
* 名称中间不能包含冒号

### **CDATA区**

*内容随意*
```
<![CDATA[内容]]>
```

### **处理指令**

处理指令用来指挥解析引擎如何解析XML文档内容
处理指令必须以"<?"作为开头，以"?>"作为结尾

### **XML约束**

在XML中可以编写一个文档来约束一个XML文档的书写规范，这称之为XML约束

##### **DTD**

DTD与XML文档关联三种方式
  1. 使用内部DTD
  2. 使用外部DTD
  3. 使用公共DTD
  
* 内部DTD
    格式：``` <!DOCTYPE 书架 [...具体规范内容...]>```
* 外部DTD
    格式：```<!DOCTYPE 文档根结点 SYSTEM "DTD文件的URL">```
* 公共DTD
    格式：```<!DOCTYPE 文档根结点 PUBLIC "DTD名称" "DTD文件的URL">```
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE books SYSTEM "books.dtd">
<books>
    <book>
    </book>
</books>
```
```xml-dtd
//books.dtd
<?xml version="1.0" encoding="UTF-8"?>
<!ELEMENT books (book+)>
<!ELEMENT book (#PCDATA)>
```

1. 元素定义

    ```<!ELEMENT 元素名称 元素内容声明>```
    
    **包含符号和数据类型两部分**
2. 属性定义
3. 实体定义
    
##### **Scheme**


### **分类**

- dom
    Document Object Model，是W3C组织推荐的解析XML的一种方式

- sax
    Simple API for XML，不是官方标准，但它是XML社区事实上的标准，几乎所有的XML解析器都支持它

----

# **JAXP-DOM**

**JAXP(Java API for XML Processing)**

### **概述**

* JAXP是Sun提供的一套XML解析API
* JAXP很好的支持DOM和SAX解析方式
* JAXP开发包是J2SE的一部分，包括以下包或子包
    - javax.xml
    - org.w3c.dom
    - org.xml.sax
* 在javax.xml.parsers包中，定义了几个工厂类，我们可以调用这些工厂类，得到对xml文档进行解析的DOM或SAX的解析器对象

### **DOM解析实例**

**Books.xml**

**1. 获取document对象**
```Java
//获得document对象
private static void demo() throws ParserConfigurationException, SAXException, IOException {
    //通过工厂，获得工厂实例
    DocumentBuilderFactory factory = DocumentBuilderFactory.newInstance();
    //通过工厂实例，获得解析器
    DocumentBuilder builder = factory.newDocumentBuilder();
    //通过解析器，解析xml文档
    Document document = builder.parse("books.xml");
    //打印文档编码
    System.out.println(document.getXmlEncoding());
}
```
**2. 获得所有book元素**
```Java
//获得所有的book元素
private static void demo2（） throws Exception {
    //获得document对象
    //获得工厂实例
    DocumentBuilderFactory factory = DocumentBuilderFactory.newInstance();
    //获得解析器
    DocumentBuilder builder = factory.newDocumentBuilder();
    //获得document
    Document document = builder.parse("books.xml");
    //获得所有book元素
    NodeList nodeList = document.getElementsByTagName("book");
    //输出book元素的个数
    System.out.println(nodeList.getLength());
```
**3. 遍历所有的book元素**
```
//需要获得所有
NodeList nodeList = document.getElementsByTagName("book");
//遍历book元素
for(int i = 0; i < nodeList.getLength(); i++) {
    //获得每一个book元素
    Node node = nodeList.item(i);
    System.out.printl(node.getNodeName());
    Element book = (Element)node;
    String id = book.getAttribute("id");
    System.out.println("ID-->" + id);
```
**4. 遍历所有的book子元素**
```
//遍历所有的子元素
NodeList childNodes = book.getChildNodes();
for(int m = 0; m < childNodes.getLength(); m++) {
    Node item = childNodes.item(m);
    //节点的类型
    if(Node.ELEMENT_NODE == item.getNodeType()) {
        //获得每个孩子
        Element child = (Element)item;
        //获得孩子的文本内容
        System.out.ln("***" + child.getTextContent());
        //第二种方式：获得文本，即当前孩子的孩子，
        NodeList texts = child.getChildNodes();
        //获得第一个元素
        Node textNode = texts.item(0);
        //输出内容
        System.out.println(child.getNodeName() + ":" + textNode.getNodeValue());
        //第三种方式
        Node firstChild = child.getFirstChild();
        System.out.println("--->" + child.getNodeName() + ":" + firstChild.getNodeValue());
    }
｝
```

### **使用DOM方法解析XML**

* 解析器工厂类DocumentBuilderFactory

> DocumentBuilderFactory dbf = DocumentBuilderFactory.newInstance();

* 解析器类DocumentBuilder

> DocumentBuilder db = dbf.newDocumentBuilder();

* 解析器生成Document对象

>Document doc = db.parse("message.xml");

* 通过Document对象查询节点

> document.getElementById   //返回Node对象
document.getElementByTagName //返回NodeList对象


### **节点列表类NodeList**

* 节点列表类NodeList就是代表了一个包含一个或者多个Node的列表
* 可以简单的把它看成一个Node的数组
* 常用方法
    * getLength(): 返回列表的长度
    * item(int):  返回指定位置的Node对象

### **节点对象Node**

* Node对象提供了一系列常量来代表结点的类型
* 当开发人员获得某个Node类型后，就可以把Node节点转换成相应的节点对象
* 节点的操作
    * getAttribute(String): 返回标签中给定属性的值
    * getAttributeNode(name): 返回指定名称的属性节点
    * getNodeName(): 返回节点的名称
    * getNodeType(): 返回节点的类型
    * getNodeValue(): 返回节点的值

# **JAXP-SAX**

### **SAX解析原理**

* SAX是事件驱动的XML处理方法
* 逐行扫描文档，一边扫描一边解析
* 仅需实现部分接口时扩展org.xml.sax.helpers.Defaulthandler类
* DefaultHandler类默认的空实现

### **需要用到的方法**

* startDocument
* startElement: 元素开始
* characters: 文本
* endElement: 元素结束
* endDocument: 文档结束

### **使用SAX方式解析XML**

* 使用SAXParserFactory创建SAX解析工厂

> SAXParserFactory spf = SAXParserFactory.newInstance();

* 通过SAX解析工厂得到解析器对象

> SAXParser sp = spf.newSAXParser();

* 通过解析器对象得到一个XML的读取器

> XMLReader xmlReader = sp.getXMLReader();

* 设置读取器的事件处理器

> xmlReader.setContentHandler(new XMLContentHandler());

* 解析xml文件

> xmlReader.parse("book.xml");


# **DOM4J**


