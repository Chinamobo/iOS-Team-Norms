Chinamobo Objective-C 编码规范
======================

<a name='TOC'/></a>目录
----
* [命名](#naming)
  * [缩写](#abbreviation)
* [其他](#others)

<a name='naming'/></a>命名
-----
### <a name='abbreviation'/></a>缩写
可以使用广泛使用的缩写，如`URL`、`JSON`。但像将`download`简写为`dl`这种是不可以的。

:o: OK
```
ID, URL, JSON
```

:x: 糟糕
```
id, Url, json
```

### <a name='match-case'></a>大小写
* 类名采用大驼峰（`FooBar`）
* 类成员、方法小驼峰（`fooBar`）
* 局部变量大小写首选小驼峰，小写下划线的形式（`foo_bar`）也可接受，大驼峰或大写
* C函数的命名用大驼峰

### <a name='hungarian-prefix'></a>变量匈牙利前缀
禁止使用标定为特定数据类型的匈牙利前缀。

前缀 | 含义
-----|-----
MSG  | 通知常量
ix   | 序号，起始为0
in   | 序号（自然数范围），起始为1
if   | 类型为浮点的“序号”
x    | 坐标
y    | 坐标
w    | 宽度
h    | 高度
vc   | 视图控制器
v    | 视图
tmp  | 临时变量
k    | 宏常量

:o: OK
```
MSGFirstColumnWillShow, wCell
```

:x: 糟糕
```
bool_switchState, floatBoxHeight
```

### 方法名
* 以 `alloc`、`copy`、`init`、`mutableCopy`、`new` 开头的方法要注意，它们会改变ARC的行为。[^1]
* 以 `get`、`set` 开头的方法有特殊的意义，不要随意定义。
  1. set 是属性默认的设置方法，如果函数不是为了设置类成员，则不要用 `set` 开头，可用 `setup` 替代。
  2. get 和属性方法无关，但在 Cocoa 中，其标准行为是通过传入的指针返回结果，而不是通过 return 来返回的（如果有返回通常返回的是操作结果）。欲获取变量，直接以变量名为名，如：`userInfomation`，而不是 `getUserInfomation`。

例：

```Objective-C
// OK
- (NSString *)name;

// 糟糕
- (NSString *)getName;

// OK，但极少使用
- (void)getName:(NSString **)buffer range:(NSRange)inRange;
 

// 对 controller 做一般设置，OK
- (void)setupController;

// 列出具体设置的内容，更好
- (void)setupControllerObservers;

// 糟糕，set 专用于设置属性
- (void)setController;
```




### <a name='others'/>其他
i，j专用于循环标号


代码格式化
-----
### 空格/换行
多个参数逗号后留一个空格（这也符合正常的西文语法）。

### 花括号
方法的花括号可以另起一行或在一行，但在方法内部需要写在一行。

    - (void)methodName:(NSString *)string {
     ↑空格                          ↑空格，花括在一行或另起一行
       if () {
      空格↑  ↑空格，花括号不要另起一行
       }
       else {
    要换行↑  ↑空格，花括号不要另起一行
       }    
    }

原因：Xcode默认的花括号位置是这样的：方法另起一行，用在方法内部的各种补全都是在一行的。

另起一行的写法在折叠后非常难看，但还要兼顾Xcode默认的处理方式，所以不做强制要求。

### 折行
与多数其他规范不同，我们不建议手动折行。

通常不要手动折行，Xcode自动折行的效果还是不错的，而且手动折行在窗口宽度比较窄时可能非常难看。不过，列宽参考线还是推荐打开的（在Xcode首选项->Text Editing中可以找到，默认80），因为一行过长的话还是不利于阅读的。

代码组织
----
* 函数长度（行数）不应超过2/3屏幕，禁止超过70行。
: 例外：对于顺序执行的初始化函数，如果其中的过程没有提取为独立方法的必要，则不必限制长度。
* 单个文件方法数不应超过30个
* 不要按类别排序（如把IBAction放在一块），应按任务把相关的组合在一起
* 禁止出现超过两层循环的代码，用函数或block替代。

类
----
类的成员尽量声明为属性，这样可以带来最大的灵活性。

### 属性的内存管理
一般说来，IBOutlet属性用weak，block（block也是对象）用copy，NSString通常也是copy，标量用assign，delegate、datesource对象用weak。

除非你明确要直接操作成员，尽量用点操作访问属性，而不要直接访问对象的成员（init方法除外）。

为什么：

我们使用属性的一个重要原因是编译器会自动根据我们设置内存关键字去管理内存，而使用成员不会有这些操作。

假设有Person类，在其对象使会打印一条日志。self有一个aPerson的属性，试问：

```
self.aPerson = [[Person alloc] init]; self.aPerson = [[Person alloc] init];
```

和

```
_aPerson = [[Person alloc] init]; _aPerson = [[Person alloc] init];
```

两段代码分别会打印多少次释放日志？你可以分别试试不同的关键字，多数是一样的，但不总是一样的，两者的行为是不一致的！

对于对象，strong、weak、assign通常有一致的打印，但其行为真的一致么？

面试时可能让你写过一个setName的方法，如果属性是retain的，可以这样写：

```
 - (void)setName:(NSString *)aName {
    if (_name != aName) {
       if (_name != nil) {
          [_name release];
       }
       _name = aName;
       if (aName != nil) {
          [aName retain];
       }
    }
 }
```

如果是assign，可以简单的这样写：

```
 - (void)setName:(NSString *)aName {
    _name = aName;
 }
```

两者能一样么？（这个例子只是一个示例，正常情况，name属性应该是copy的）

另一方面，自动合成的属性做的不仅仅是内存管理那么简单，直接访问成员可能不会触发key value observe（跟监听的option有关），而且触发次数也常常不一样。

## 注释
方法内部禁止使用块注释。 

“注释应该表达那些代码没有表达以及无法表达的东西。如果一段注释被用于解释一些本应该由这段代码自己表达的东西，我们就应该将这段注释看成一个改变代码结构或编码惯例直至代码可以自我表达的信号。我们重命名那些糟糕的方法和类名，而不是去修补。我们选择将长函数中的一些代码段抽取出来形成一些小函数，这些小函数的名字可以表述原代码段的意图，而不是对这些代码段进行注释。尽可能的通过代码进行表达。你通过代码所能表达的和你想要表达的所有事情之间的差额将为注释提供了一个合理的候选使用场合。对那些代码无法表达的东西进行注释，而不要仅简单地注释那些代码没有表达的东西。”[http://bigwhite.blogbus.com/logs/125602412.html]

### 异常
* 作为被调用模块的维护者，当被调用不当时（参数有问题、不和时宜），如何处理需要考虑（抛出异常还是返回错误状态）
* 不要依赖try catch，它不是代替你做检查、填补遗漏的工具

### 其他
#### 使用Unicode
能用UTF-8就用UTF-8（无BOM的），让GBK这类编码见鬼去吧。不要写依赖ANSI编码格式的代码。

参考
------
* http://developer.apple.com/library/ios/#documentation/Cocoa/Conceptual/CodingGuidelines/CodingGuidelines.html
* 你们是如何为 View Controller 的变量命名的呢？http://www.v2ex.com/t/25732

[^1]: [再谈ARC - 苹果核](http://pingguohe.net/2012/06/22/talk_arc_again/)
