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

#### 哪些操作会导致强制执行
1. 转换方法（返回集合或具体类型）
这些方法会立即执行查询并返回一个新的集合或数据结构：

方法	            返回类型	                                说明
.ToList()	        List<T>	                               将查询结果转换为 List<T>
.ToArray()	        T[]	                                   将查询结果转换为数组
.ToDictionary()	    Dictionary<TKey, TValue>	           将查询结果转换为字典
.ToLookup()	        ILookup<TKey, TElement>                创建分组查找结构
.ToHashSet()	    HashSet<T>	                           将查询结果转换为 HashSet<T>
.AsEnumerable()	I   Enumerable<T>	                       强制转换为 IEnumerable<T>（不立即执行，但某些情况下可能影响后续查询）

2. 聚合计算（返回单个值）
这些方法会立即计算并返回一个标量值：

方法	                        返回类型	        说明
.Count()	                     int	        返回元素数量
.LongCount()	                 long	        返回大整数计数值
.Any()	                         bool	        检查是否有元素满足条件
.All()	                         bool	        检查是否所有元素满足条件
.First() / .FirstOrDefault()	 T	            返回第一个元素（或默认值）
.Last() / .LastOrDefault()	     T	            返回最后一个元素（或默认值）
.Single() / .SingleOrDefault()	 T	            返回唯一元素（或默认值，否则抛异常）
.Min() / .Max()	                 T 或数值	     返回最小/最大值
.Sum()	                         数值	        求和
.Average()	                     数值	        求平均值
.Aggregate()	                 T	            自定义聚合计算

3. 元素获取（强制遍历）
这些方法会立即执行查询以获取特定元素：

方法	                                返回类型	        说明
.ElementAt() / .ElementAtOrDefault()	   T	        获取指定索引位置的元素
.Contains()	                               bool	        检查是否包含某个元素

4. 序列比较
方法	            返回类型	    说明
.SequenceEqual()	 bool	    比较两个序列是否完全相同

5. 强制求值（非标准 LINQ，但会影响执行）
方法	                                 说明
.GetEnumerator() + 遍历	            直接遍历会触发查询执行
foreach 循环	                    隐式调用 GetEnumerator()
.ToString()（某些 LINQ 提供程序）	 可能强制求值（如 Entity Framework 的查询日志）


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
这里要理解的是，`.Where()` 会对 `names` 中的每个元素执行作为参数传入的lambda表达式方法；
同时C#编译器会推断数据源（在这里也就是names）的类型，并且告诉`Where()`把什么类型作为元素的输入类型；

## LINQ 对查询结果进行排序
只需要一行 `orderby  <condition>` 子句就可以实现排序。下面是基于查询语法进行演示
```C#
    List<Person> people = new List<Person>() {
    new Person("Jessica",29),
    new Person("Louis",5),
    new Person("Vicky",17),
    new Person("Lucy",41),
    new Person("Lily",22),
    new Person("Simon",37),
    new Person("Steward",50),
    new Person("Penny",71),
    new Person("Howard",16),
    new Person("Laurent",9),
    new Person("Lewis",33),
    new Person("Babara",10),
    new Person("Jake",51),
    new Person("Jack",18),
    new Person("Glory",25),
    new Person("Smith",44)
    };

    var queryResult = from p in people
                      where p.Name.StartsWith("L")
                      orderby p.Age descending
                      select p;

    foreach (var person in queryResult)
    {
        Console.WriteLine("I find {0}, who is {1} yrd ", person.Name, person.Age);

    }
```

默认情况 `orderby` 子句是升序排列的，如果希望降序则加入 `descending` 关键字
上述代码的方法语法查询实现:
```C#
 var queryResult = people.Where(n => n.Name.StartsWith("J")).OrderBy(n => n.Age);
```

### 调用LINQ的聚合运算方法/运算符
LINQ像SQL一样提供了一些用于聚合运算的方法。者可以方便开发者在获取筛选后的数据后不必再做额外的开发操作去处理筛选结果。

***需要注意的是： 根据删选结果的不同可能 `查询结果`的集合内并不存在数据，这时调用`聚合运算符`会出现异常导致程序异常***

使用方法很简单，直接在查询结果上调用对应的聚合运算方法即可。另外，下列例子中如果把筛选条件改成`n<500`就很可能出现查询结果为空的情况，因为并没产生出这样的数据。然后在使用聚合运算符时没有提前验证或者try..catch保护就会导致程序异常。

```C#
            var queryResult = from n in GetLotsOfNumbers(1_000_000) where n%2==0 select n;

            foreach (var item in queryResult)
            {
                Console.WriteLine(item);
            }

            Console.WriteLine("The count of result is: " + queryResult.Count());
            Console.WriteLine("The Average of result is: " + queryResult.Average());
            Console.WriteLine("The MaxNumber of result is: " + queryResult.Max());
            Console.WriteLine("The MinNumber of result is: " + queryResult.Min());
            Console.WriteLine("The count of result is: " + queryResult.Sum(n=>(long)n));

        static int[] GetLotsOfNumbers(int size)
        {
            int[] numbers = new int[size];

            Random r = new Random(1024);
            for (int i = 0; i < numbers.Length; i++)
            {
                numbers[i] = r.Next();

            }
            return numbers;
        }
```

另外在实际操作的时候还要注意`数据溢出`的问题。比如上例中，获取的都是`int`类型，但是可能最后`Sum()`运算符给出的结果非常大超出int类型的存储范围，所以选择了`Sum()`的可传递lambda参数的重载，将数值转换为`long`类型。

***PS: 每个聚合运算符都有可自定义运算条件的重载方法。 同时LINQ也提供了完全可自定义的聚合运算符***

### 单值选择查询/去重查询
这种查询是用于查询数据中的唯一值。也就是说结果是不重复的值或值的集合。比如一个学校的学生来自五湖四海，现在想知道一共来自那些地方，而不是有多少个人来自同一个地方，那么就可以用这种单值查询。
使用方式也很简单，只需要在查询语句的最后调用`.Distinct()`方法。

```C#
   List<Person> people = new List<Person>() {
   new Person("Jeccica",19,"Asia"),
   new Person("Bright",57,"America"),
   new Person("Lucy",77,"Africa"),
   new Person("Trupt",7,"Europe"),
   new Person("Terry",13,"South America"),
   new Person("Lily",20,"Asia"),
   new Person("Micheal",16,"Asia"),
   new Person("Steward",49,"Asia"),
   new Person("Jame",25,"Europe"),
   new Person("Kiki",5,"Europe"),
   new Person("Labubu",33,"Europe"),
   new Person("Omiga",14,"Europe"),
   new Person("Bruck",25,"South America"),
   new Person("Baby",3,"South America"),
   new Person("Smith",50,"South America"),
   new Person("Sebastian",30,"South America")
   };

    // 将查询语句用()包裹起来，然后在()外调用 .Distinct()
   var queryResult = (from p in people
                     select p.Region).Distinct();
```
或者使用方法语法查询
```C#
  var queryResult = people.Select(p=>p.Region).Distinct();
```

### 多级排序
当需要将数据根据条件进行排序整理的时候通过 `orderby` 子句进行处理。之前的例子是只用了一个条件，并通过添加降序关键字来控制升降序。下面展示如何通过多个条件以及升降序关键字进行控制。
```C#
    List<Person> people = new List<Person>() {
       new Person("Jeccica",19,"Asia"),
       new Person("Bright",57,"America"),
       new Person("Lucy",77,"Africa"),
       new Person("Trupt",7,"Europe"),
       new Person("Terry",13,"South America"),
       new Person("Lily",20,"Asia"),
       new Person("Micheal",16,"Asia"),
       new Person("Steward",49,"Asia"),
       new Person("Jame",25,"Europe"),
       new Person("Kiki",5,"Europe"),
       new Person("Labubu",33,"Europe"),
       new Person("Omiga",14,"Europe"),
       new Person("Bruck",25,"South America"),
       new Person("Baby",3,"South America"),
       new Person("Smith",50,"South America"),
       new Person("Sebastian",30,"South America")
    };

    var queryResult = from p in people
                      orderby p.Region, p.Age descending, p.Name
                      select p;
  foreach (var person in queryResult)
    {
         Console.WriteLine($"{person.Name} is from {person.Region} and {person.Age} year{(person.Age>1?"s":"")} old ");
    }
```
如上面这段代码所展示，只需要将自己需要的排序顺序一次键入，并使用 `,` 分隔即可。这个例子得到的输出是先按*Region 升序*排列，然后按*Age 降序*，最后再按*名字 升序* 排列。

***使用语法方法的时候，每一个排序等级按顺序使用 `ThenBy()` or `ThenByDescending()`进行Chain Method***
```C#
var queryResult = people.OrderBy(p => p.Region).ThenByDescending(p => p.Age).ThenBy(p => p.Name);
```