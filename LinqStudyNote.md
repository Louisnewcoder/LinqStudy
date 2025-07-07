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

## 关于`XElement`
从上面的例子可以看出，具体的数据信息是由XElement所承载的。最外层的 `XElement`可以看作是 `root node`， 一次层层内嵌的`XElement`则是数据成员。

### 关于xml的Attribute
可以从例子中看出，作为附属节点的`属性`也是以构造函数的方式创建的。

***PS：技术所有的LtM 内的对象都是以 `X` 开头***
***PS：另外，大多数的构造函数都是以类似 `键值对` 的形式输入的***


