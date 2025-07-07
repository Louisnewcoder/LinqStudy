# Linq Study Note
LINQ to XML 是 System.xml.Linq 命名空间下提供的基于Linq处理xml文件的技术栈。

## 通过代码创建xml文档
L t M 提供了构造函数式创建方式。 通过声明 `XDocument` 类，将作为数据的xml节点成员作为参数传入构造函数中，即可在内存中创建一个xml文档。如下例所示

```c#
            XDocument xDoc = new XDocument(

                new XDeclaration("1.0", "UTF-8", "yes"),
                new XElement("customers",
                     new XElement("customer",
                        new XAttribute("ID", "A"),
                        new XAttribute("City", "New York"),
                        new XAttribute("Region", "North America"),
                        new XElement("order",
                                new XAttribute("Item", "Widget"),
                                new XAttribute("Price", 100))),
                       new XElement("customer",
                        new XAttribute("ID", "B"),
                        new XAttribute("City", "Mumbai"),
                        new XAttribute("Region", "Asia"),
                        new XElement("order",
                                new XAttribute("Item", "Oven"),
                                new XAttribute("Price", 501)))

                    )
                );
```

`XDocument` 提供了4中构造函数。它还有不接受参数的构造函数，接收另一个 `XDocument` 的构造函数，接收一个 `XDeclaration` 的构造和 `XElement` 的构造函数，以及上例所示的一个可以之传入 `XElement`的构造函数。

***PS：一般情况下并不需要 `XDeclaration` 除非有特殊指定需要。 这里是自己写代码练习一下***

## 关于`XElement`
从上面的例子可以看出，具体的数据信息是由XElement所承载的。最外层的 `XElement`可以看作是 `root node`， 一次层层内嵌的`XElement`则是数据成员。

***PS：XML文档必须有一个根元素，所以大多数情况下 `XDocument`的参数都已 `XElement` 开始(除XDeclaration) ***


### 关于xml的Attribute
XML Attribute 式XML文档的一个叶节点，可以从例子中看出，作为附属节点的`属性`也是以构造函数的方式创建的。

***PS：技术所有的LtM 内的对象都是以 `X` 开头***
***PS：另外，大多数的构造函数都是以类似 `键值对` 的形式输入的***

## 关于使用L t M 加载和保存XML文档
### 加载XML文档
加载的时候要使用`XDocument`类的静态方法`.Load( <file Path>)`
```C#
    // Load方法接收一个用于输入加载地址的 字符串参数
    // 该方法返回一个 XDocument 类型的变量
  XDocument xDoc = XDocument.Load(Path.Combine(path, fileName)); 
     
```

### 保存XML文档
保存的时候需要使用 `XDocument`实例对象的实例方法 `.Save( <file Path>)`
```C#
    // Save方法同样接收一个用于确认保存路径的 字符串参数
     xDoc.Save(Path.Combine(path, fileName));    
```
