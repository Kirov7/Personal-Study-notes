## XML

### XML是什么

```tex
World Wild Web Consortium (W3C) 中的标准定义
The Extensible Markup Language (XML) is a simple text-based format for representing structured information: documents, data, configuration, books, transactions, invoices, and much more. It was derived from an older standard format called SGML (ISO 8879), in order to be more suitable for Web use.

可扩展标记语言 （XML） 是一种基于文本的简单格式，用于表示结构化信息：文档、数据、配置、书籍、交易记录、发票等。它派生自一种称为 SGML （ISO 8879） 的旧标准格式，以便更适合 Web 使用。
```

### XML的作用

XML 是各种应用程序之间进行数据传输的最常用的工具。

XML 应用于 Web 开发的许多方面，常用于简化数据的存储和共享。

#### XML不会做任何事情

XML 被设计用来结构化、存储以及传输信息。但本身不会做任何事情。

下面实例是 张三 写给 李四 的 便签，存储为 XML：

```xml
<note>
	<to>李四</to>
	<from>张三</from>
	<heading>便签</heading>
	<body>这周末一起干饭！</body>
</note>
```

上面的这条便签具有自我描述性。它包含了发送者和接受者的信息，同时拥有标题以及消息主体。

但是，这个 XML 文档仍然没有做任何事情。它仅仅是包装在 XML 标签中的纯粹的信息。我们需要编写软件或者程序，才能传送、接收和显示出这个文档。



### XML语法

1. xml是一种文本文档，其后缀名为xml
2. xml文档内容的第一行必须是对整个文档类型的声明
3. xml文档内容中有且仅有一个根标签
4. xml文档内容中的标签必须严格闭合
5. xml文档内容中的标签属性值必须使用单引号或双引号引起来
6. xml文档内容中的标签名区分大小写

<font color=blue>示例</font>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<students>
    <student name="枫阿雨" age="19" sex="男"></student>
    <STUDENT>
        <NAME>枫阿雨</NAME>
        <AGE>19</AGE>
        <SEX>男</SEX>
    </STUDENT>
</students>
```



> 注1：当在xml文档中出现了像(<)这类的特殊符号时，使用标签CDATA来完成，CDATA标签中的内容会按原样展示

<font color=blue>语法</font>

```xml
<![CDATA[
	<!--内容-->
]]>
```

> 注2：空标签可以使用自闭合，仍符合语法规范

<font color=blue> 语法</font>

 ```xml
 <BOOK name="CSAPP"/>
 ```

 

XML文档可以自定义标签，为了更规范的使用XML文档，可以使用XML约束来限定XML文档中的标签使用。

XML约束可以通过DTD文档和Schema文档来实现，其中DTD文档比较简单，后缀为dtd，而Schema技术则比较复杂，后缀名为xsd。



***



## DTD约束



#### DTD约束元素

<font color=blue>元素类型</font>

**EMPTY (空元素)** ，元素不包含任何数据，即标签之间没有任何其他内容，但是可以有属性，可以使用自闭合，如：

```xml
<student name="枫阿雨" sex="男" age="19" />
```

**#PCDATA(字符串)**，PCDATA是指被解析器解释的文本也就是字符串内容，不能包含其他类型的元素，如：

```xml
<name>枫阿雨</name>
<sex>男</sex>
<age>19</age> 
```

**ANY(任何内容都可以)**

<font color="blue">DTD约束元素出现顺序及次数</font>

| 情景       | 语法                     | 描述                                            |
| ---------- | ------------------------ | ----------------------------------------------- |
| 顺序出现   | `<!ELEMENT name (a, b)>` | 子元素a、b必须同时出现，且a必须在b之前出现      |
| 选择出现   | `<!ELEMENT name (a|b)>`  | 子元素a、b只能有一个出现，要么是a，要么是b      |
| 只出现一次 | `<!ELEMENT name (a)>`    | 子元素a只能且必须出现一次                       |
| 一次或多次 | `<!ELEMENT name (a+)>`   | 子元素a要么出现一次，要么出现多次               |
| 零次或多次 | `<!ELEMENT name (a*)>`   | 子元素a可以出现任意次（包括不出现，即出现零次） |
| 零次或一次 | `<!ELEMENT name (a?)>`   | 子元素a可以出现一次或不出                       |

<font color="blue">元素格式</font>

> 注：此内容写在.dtd文件中，在.xml文件中导入后方可引入此文件中的规范

```dtd
<!ELEMENT students() students (student*)>
<!ELEMENT student (name, sex, age) ANY>
<!ELEMENT name (#PCDATA)>
<!ELEMENT sex (#PCDATA)>
<!ELEMENT age (#PCDATA)>
```

```xml-dtd
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE students SYSTEM "student.dtd">
<students>
	<student>
        <naem>枫阿雨</naem>
        <sex>男</sex>
        <age>19</age>
    </student>
</students>
```



#### DTD约束属性

<font color="blue">属性值类型</font>

**CDATA**，属性值为普通文本字符串。

**Enumerated**，属性值的类型是一组取值的列表，XML文件中设置的属性值只能是这个列表中的某一个值。

**ID**，表示属性值必须唯一，且不能以数字开头。

<font color="blue">属性值设置</font>

**#REQUIRED**，必须设置该属性。

 **#IMPLIED**，该属性可以设置也可以不设置。

 **#FIXED**，该属性的值为固定的。

**使用默认值** (不写)。

<font color="blue">属性格式</font>

```dtd
<!ATTLIST 元素名 属性名 属性值类型 设置说明>
```

<font color="blue">示例</font>

```xml-dtd
<!ELEMENT students (student*)>
<!ELEMENT student (name,sex,age)>

<!ATTLIST student number ID #REQUIRED>
<!ATTLIST student name CDATA>
<!ATTLIST student sex(男|女|其他) #IMPLIED>
<!ATTLIST student age CDATA>
```

```xml-dtd
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE students SYSTEM "student.dtd">
<students>
	<student number="a111" name="枫阿雨" sex="男" age="19"/>
	<student number="a112" name="雨阿枫" sex="男" age="19"/>
	<student number="a113" name="阿枫雨" sex="男" age="19"/>
</students>
```


***



## DTD解析⚠

##### 1. 解析方式

<font color="blue">DOM解析</font>

DOM解析将XML文档一次性加载进内存，在内存中形成一颗dom树，优点是操作方便，可以对文档进行CRUD的所有操作，缺点就是占内存

<font color="blue">SAX解析</font>

SAX解析式将XML文档逐行读取，是基于事件驱动的，优点是不占内存，缺点是只能读取，不能增删改

对于XML操作，我们通常都是读取操作，增删改的情况较少，因此这里主要讲解SAX解析,SAX解析最优实现是Dom4j解析器。

##### 2. `Dom4j`解析XML

<font color="blue">示例</font>

```java
package com.kirov.xml;

import org.dom4j.Attribute;
import org.dom4j.Document;
import org.dom4j.DocumentException;
import org.dom4j.Element;
import org.dom4j.io.SAXReader;

import java.io.InputStream;
import java.util.Iterator;

public class XmlParser {

    public static void main(String[] args) {
        //构建一个SAX读取器
        SAXReader reader = new SAXReader();
        //通过类的字节码对象获取一个给定资源并将该资源读取到流的通道中
        InputStream is = XmlParser.class.getResourceAsStream("student.xml");
        try {
            //SAX读取器从通道中读取一个文档对象
            Document document = reader.read(is);
            //获取文档的根元素，因为XML文档只会有一个根元素
            Element root = document.getRootElement();
            //获取根元素的标签名
            String tagName = root.getQualifiedName();
            System.out.println("XML文档根标签：" + tagName);
            //获取根元素的下一级子元素
//            List<Element> elements = root.elements();
//            for(Element element: elements){
//                //获取元素的标签名
//                String tag = element.getQualifiedName();
//                System.out.println(tag);
//                //获取元素的所有属性
//                List<Attribute> attributes = element.attributes();
//                for(Attribute attr: attributes){
//                    //获取属性名
//                    String name = attr.getName();
//                    //获取属性值
//                    String value = attr.getValue();
//                    System.out.print("属性：" + name + "=>" + value + "\t");
//                }
//                System.out.println();
//            }
            Iterator<Element> iterator = root.elementIterator("student");
            while (iterator.hasNext()){
                Element element = iterator.next();
                //获取元素的标签名
                String tag = element.getQualifiedName();
                System.out.println(tag);
//                //获取元素的所有属性
//                List<Attribute> attributes = element.attributes();
//                for(Attribute attr: attributes){
//                    //获取属性名
//                    String name = attr.getName();
//                    //获取属性值
//                    String value = attr.getValue();
//                    System.out.print("属性：" + name + "=>" + value + "\t");
//                }
//                System.out.println();
                String name = element.attributeValue("name");
                String sex = element.attributeValue("sex");
                String age = element.attributeValue("age");
                System.out.println(name + "\t" + sex + "\t" + age);
            }
        } catch (DocumentException e) {
            e.printStackTrace();
        }
    }
}
```

