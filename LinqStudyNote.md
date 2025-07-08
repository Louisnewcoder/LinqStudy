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
<<<<<<< HEAD
`XDocument` 和 `XElement` 都继承自 `XContainer`类（它实现了一个可以包含其他xml节点的xml节点）。这两个类都实现了`Save()`和`Load()`方法，且调用方式一致。 另外`XElement`还支持`.Parse()`方法直接从字符串中读取XML数据。

下面仅使用其中一种进行展示：


### 加载XML文档
加载的时候要使用`XDocument`类的静态方法`.Load( <file Path>)`
```C#
    // Load方法接收一个用于输入加载地址的 字符串参数
    // 该方法返回一个 XDocument 类型的变量
  XDocument xDoc = XDocument.Load(Path.Combine(path, fileName)); 
     
```
<<<<<<< HEAD

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

### 保存XML文档
保存的时候需要使用 `XDocument`实例对象的实例方法 `.Save( <file Path>)`
```C#
    // Save方法同样接收一个用于确认保存路径的 字符串参数
     xDoc.Save(Path.Combine(path, fileName));    
```

## LINQ 查询语法
***这是一种类似 SQL 语句的语法***
先看示例：
```C#
string[] names = { "James", "Louis", "Jessica", "Laurrence", "Lucy",
    "Howard", "Tequila", "Daisy", "Funky", "Song Li" };

var quereyResult = from n in names where n.StartsWith("J") select n;

foreach (var n in quereyResult)
{
    Console.WriteLine(n);
}
```
在上述例子中展示了如何使用LINQ在一个类型集合中找到符合条件的目标。其中:

1. ` from n in names` 类似 `foreach` 循环的条件是声明语句，在这里代表：
***声明一个一个循环变量 n ，用于代表names中的每一个元素***；
2. `where` 是条件是一个条件语句，用于筛选出符合条件的元素。这里的条件是所有开头为`J`的string元素；
3. `select` 是确认选择语句，将所有符合条件的元素收集起来；
4. 如果将鼠标放在 `where` 关键字上，可以看到这个条件和删选的结果返回一个 `IEnumerable<T>`的类型变量；

### 关于 var 关键字的使用实践
`var` 关键字是被广泛推荐作为LINQ结果的变量是声明,没什么特别的就是方便，因为很多时候你不知道你查询的泛型类型是什么，但是它一定是`IEnumerable<T>`的可迭代集合。这样就可以让编辑器自己推断。

### 关于 from 与 “数据源”
`from` 关键字用于指定要查询的数据源，这个数据源必须是数组或者可枚举(迭代的)集合。***这要求数据源必须支持 `IEnumerable<T>` 接口***

### 关于 where 子句
`where` 字句是可选的，只不过大多数的时候都会要求一些筛选条件。where子句被称为LINQ中的限制运算符，它限制了查询结果。

### 关于 select 子句
`select` 子句是必须的*因为必须指定结果中有哪些元素*

### 关于“延迟执行查询的概念”
虽然在一开始写了LINQ的查询语句并且将查询结果存入了一个名为`queryResult`的变量，但实际上代码运行到这里并没有产生查询结果。这里实际上是存储了一个 ***查询计划*** 。
直到`foreach` 遍历`queryResult`变量的时候，才真正执行LINQ查询，并返回结果交给`foreach`遍历。 

***PS： LINQ查询语法 是由C#编译器进行将其转换为 LINQ方法语法并执行的。 在使用LINQ时，官方推荐有限使用查询语法，当查询语法不合适时再使用方法语法。***

## LINQ 方法语法
LINQ的方法语法就是为一切可支持LINQ查询的集合对象实现的与传统C#方法一样的扩展方法。
在集合后使用 `.` 方法调用符就可以调用。
但是如果没有引用 `System.Linq` 命名空间就无法调用或者查询这些方法的intelliSense.

```C#
  foreach(var n in   names.Where(n => n.StartsWith("L")))
     {
         Console.WriteLine(n);
     }

     // names.Where(n => n.StartsWith("L") 这一行语句就是使用方法语法获得与查询语法相同的效果
```