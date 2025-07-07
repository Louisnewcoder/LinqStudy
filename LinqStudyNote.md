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

***PS：因为 `XDocument`和`XElement` 都继承自XContainer类，而且大多数XDocument的方法 `XElement`都实现了，所以创建一个xml文档并写入内容可以直接用`XElement`类封装数据，然后直接调用`Save()`方法也可以保存 ***


### 关于xml的Attribute
XML Attribute 式XML文档的一个叶节点，可以从例子中看出，作为附属节点的`属性`也是以构造函数的方式创建的。

***PS：技术所有的LtM 内的对象都是以 `X` 开头***
***PS：另外，大多数的构造函数都是以类似 `键值对` 的形式输入的***

## 关于使用L t M 加载和保存XML文档
`XDocument` 和 `XElement` 都继承自 `XContainer`类（它实现了一个可以包含其他xml节点的xml节点）。这两个类都实现了`Save()`和`Load()`方法，且调用方式一致。 另外`XElement`还支持`.Parse()`方法直接从字符串中读取XML数据。

下面仅使用其中一种进行展示：

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

## 如何插入数据
使用 `Add()`系列方法可以向xml文本内添加数据。
`Add()` - 添加到子节点的最后一个位置
`AddFirst()` - 添加到子节点的第一个位置
`AddAfterSelf()` - 添加到当前节点的后面
`AddBeforeSelf()` - 添加到当前节点的前面

下面调用`Add()`方法作为示例：

```C#
    // 首先创建一个内容
     XElement newCustomer = new XElement("customer",
         new XAttribute("ID", "F"),
         new XAttribute("City", "Chang sha"),
         new XAttribute("Region", "Hu nan"),
         new XElement("order",
         new XAttribute("Item", "Book"),
         new XAttribute("Price", 73.5)));
    // 延续之前的例子， 添加到之前例子中xml文档root node子节点中的最后一个位置
     xElm.Add(newCustomer);
```

## 如何更新数据 -含其属性
1 更新数据的核心操作步骤思想就是利用 `Descendants()`方法，获取子节点；
2 然后基于LINQ的链式语法设置查询条件获取目标；
3 对数据的指定内容进行操作

***需要注意的是：***
    在查找过程中要明确查询条件是 `XElement` 还是 `XAttribute`

示例：
```C#

        // 这个示例展示了通过XAttribute查找对象
        // 然后修改对象身上 Price属性的值
            XElement? customer = newXML.Descendants("customer")
               .FirstOrDefault(c => (string)c.Attribute("ID") == "F");

            if (customer != null) {

                XAttribute? priceNode = customer.Element("order").Attribute("Price");

                if (priceNode != null) {

                    priceNode.Value = "521234";  
                }            
            }
```
## 如何删除数据或其属性
都是在目标类型上调用 `Remove()` 方法

删除数据
```C#
    var bookToRemove = xDoc.Descendants("Book")
                       .FirstOrDefault(b => (string)b.Element("Title") == "Dune");
        bookToRemove?.Remove();
```


删除属性
```C#
    var price = xDoc.Descendants("Price").FirstOrDefault();
    price?.Attribute("Currency")?.Remove();
```

