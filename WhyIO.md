# 为何程序需要 IO 操作
这篇文章旨在挑战如今人们对于软件架构的基本观念，所以才故意起个搞个大新闻的标题。
虽然你可能不认同接下来的观点，但仍希望此文可改变读者对于编程的思考方式。

这里想要用自己的话复述一下 Conal Elliot 一直重申的观点：现代编程实际上是 Rube-Goldberg machine，如果我们愿意改变组织代码的方式，就可以被大大简化。

很多程序员直觉上或多或少已经察觉了这一点。他们会告诉你编程的生态环境是如此的错误百出支离破碎与过度复杂。一直以来，我感同身受，但回想起来，这种不满又如此肤浅，囿于认知，对于背后更深层根本的问题视而不见。

利益相关：此文使用了我可爱的 configuration 语言 Dhall，主要考量到其为文中很多论点的 proof-of-concept。当然，此文也并不是为了安利 Dhall 而是为了向你展示一种新的思考软件的方式。

# Input 与 Output

回顾一下标题：

> 为何程序需要 IO 操作

多数人会回答：

- 程序需要与外部真实世界进行沟通
- 程序不能只是烤机吧

现在我从另一方面复述标题中的问题：

- 若是只有编译器可以进行 IO 操作，何如？

你可能会问：又有什么两样呢？显然你是指一门语言的编译器提供了一些库函数，之后编译好的程序通过调用这些库函数进行IO，无论是文件 handle 或是 stdin/stdout。

不，我完全不是这个意思。我指的是编译器可以进行 IO 操作，但编译好的语言就不能进行 IO 操作了，只能进行 pure 的函数计算。

不得不说，这个想法乍看很愚蠢。如果按你说的，你要怎么与程序进行通信呢？

# Import

大多数语言都可以 import 代码，主要通过 package 的形式。import 进来的 value 和函数，对编译器而言，都是编译期的静态的输入；与之相对的编译好的程序是运行时的动态的输入。

假设我告诉你，有一门语言仅能通过 import 来获取输入。

「住口！无稽之谈！」你激动的把刚喝的咖啡都喷了出来。「不会有人想用这样一种语言的。」你甚至觉得槽点太多以至于不知从哪里开始反驳。

或许，你会吐槽获取或发布一个新的 value 所需的繁琐操作。你或许开始背诵你最喜欢的语言的包管理(package management)流程：

- 新建一个module，把你要的 value 放进去
- 按照模板，新建一个 project
- 向其中添加 project 描述文件
- 通过 version control 检查你的 project
- 把你的 package 发布到一个 package repository

或许，你会吐槽通过改变 import 配置程序麻烦的要死。你最爱的编程语言通常会要求你：

- 从version control 获取相关程序
- 修改项目描述文件，使之依赖你新发布的 package
- 修改源代码以 import 你的 value
- 编译
- 运行

凭什么让麻瓜用户做这么一大堆事情而不是简单地通过 IO 操作读写你的 value？

这就是我刚才提到的 Rube-Goldberg machine。我们已经习惯于在源代码中依赖另一源代码的复杂操作。

# import 路径

分发代码并不必须那样的繁琐。譬如 Dhall 的 import 系统就可以直接通过路径 import 你的value。

举个例子，我们把`True`写到了文件 `example.dhall`：
```
$ echo 'True' > example.dhall
```
另一个 Dhall 程序就可以在任何需要布尔值的地方引用这个值：
```
$ dhall <<< './example.dhall || False'
Bool

True
```

引用文件路径等同于直接引用文件内容：
```
$ dhall <<< 'True || False'
Bool

True
```

Dhall 不支持任何显式的读取操作，因为 Dhall 已经通过 import 读取到了所需的 value。

同样的，Dhall 也不支持显式的写操作。只需要用文本编辑器把 Dhall 的表达式保存下来就可以了。

「若是我需要自动化生成多个文件怎么办呢？」

你并不需要这样做，因为单个文件已经足够去存储你所需的信息。Dhall 是一门可编程的配置语言，支持 list 与 record，所以可包含任意多个 value。
生成多个文件只是处于对人类阅读方便的考量，然而 Dhall 代码并不会因写到单一文件或多个文件而受影响。

多数人需要自动化读写只是因为他们在使用不可编程的配置文件格式或是数据储存格式。

# 可编程配置

Dhall 不是图灵完备的。
……
……

# import URL
……

# 结论
如果我们可以改变那些使我们陷入麻烦的固有观念，软件可以变得不那么麻烦。

这些观点深受 Conal Elliot 的文章影响

- 函数式编程可以逃脱冯诺依曼的桎梏吗？

这篇文章不是讲 Dhall 的，而是致力于阐释无副作用函数的编程的未来。我相信 IO 操作终将变成高级语言的底层实现，正如 allocate stack register 或管理内存。



译自 Gabriel Gonzalez, Why do our programs need to read input and write output?,Oct 2017
http://www.haskellforall.com/2017/10/why-do-our-programs-need-to-read-input.html
