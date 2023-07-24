# Python 里技

你可能不知道的 Python 特性!

我们主要围绕 Python 的 OOP 内容展开, 主要原因是在教学过程中被省略掉了这一重要的部分. 虽然说后端开发并不需要你掌握什么特别 fancy 的东西, 但是面向对象的概念却在后端开发中处处可见.

剩下的内容我们会讨论一些 Python 的语言特性, 例如一些元编程的内容. 我们不会深入到 CPython 的具体实现, 只会蜻蜓点水般地简单介绍.

{% hint style="info" %}
深入任何一门语言, 都需要你大量地进行编码, 实验, 去熟练这些特性. 本文如果能起到抛砖引玉的作用, 那就是万幸. 若有时间, 我们很推荐你去自学完成给出的补充资料!
{% endhint %}

## 回顾: Python 基础(可选)

## 示例 1: Nahida to Python

Nahida 很喜欢[编程](https://space.bilibili.com/546137543)(作为智慧之神那是必须的). 她从[官网](https://www.python.org/downloads/) Python 的新版本, 并已经添加到了系统的环境配置里. 为了检查这一点, 她在终端里键入了

```bash
$ python3 --version
Python 3.11.4
```

Nahida 之前从来没有接触过 Python! 为了体验与其它语言的不同, 她写了一个经典的 Hello world 程序 (`test.py`):

```python
hello, world = 'hello', 'world'        # multiple assignment
print(f'{hello}', '%s!' % (world))     # f-string, % operator (.format() is omitted here)
```

```bash
$ python3 -i test.py
hello world!
>>> world = 'python'
hello python
```

在**交互(interactive)**环境下, Nahida 又将打印的内容更改为了 `hello python!`(这是一个非常好用的调试方法)

Nahida 又听说, Python 的数学运算非常强大, 据说自带高精度, 还有一些好用的内置方法, 而且还有 `numpy`, `scipy` 等库的加持(你甚至可以拿它们去做高代作业, 解决计算几何的一些问题 :)

```python
>>> (2 ** 114513 - 1) % 998244353
766171354
>>> x = 1172.5
>>> x.as_integer_ratio()
(2345, 2)
>>> import numpy as np
>>> a = np.array([[1,0],[0,1]])
>>> b = np.array([[4,1],[2,2]])
>>> np.multiply(a, b)
```

字符串处理也是不甘示弱: **切片操作(slice)**可以轻易获取一个字符串的子串; `in` 关键字可以轻松判断字符串的包含关系; 还有强大的正则表达式支持(`re` 库)......

```python
>>> text = 'The Temple of Wisdom'
>>> text[4:]
Temple of Wisdom
>>> 'Wisdom' in text
True
>>> import re
>>> message = 'hello from Nahida@akademiya.edu.sumeru'
>>> emails = re.findall(r'\b[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Z|a-z]{2,}\b', message)
>>> emails
['Nahida@akademiya.edu.sumeru']
```

{% hint style="info" %}
我们推荐你去了解下正则表达式, 因为某些场景的字符串匹配用它真的很方便, 同时很多编程语言和 Unix 工具都支持正则表达式, 例如 `sed`. 这里有一个[互动教程](https://regexone.com/).

不要让 regex 成为你"书到用时方恨少"的下一个遗憾. (血的教训)
{% endhint %}

以及内置的许多好用的数据结构: **列表(list), 元组(tuple), 字典(dict)** 以及**集合(set)**, 还支持一个优雅的特性: **推导式(Comprehension)**, 能够最大程度上让你的代码保证一定可读性的同时高效 ([真的不用 For Loop?](https://www.bilibili.com/video/BV1At4y1b7L1/))

```python
>>> list(range(10))
[0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
>>> [i ** 3 for i in range(4)]
[0, 1, 8, 27]
>>> {str(i) : i for i in range(10) if i % 2 != 0}
{'1': 1, '3': 3, '5': 5, '7': 7, '9': 9}
>>> sum(i ** 3 for i in range(4))
36
```

{% hint style="info" %}
**字典尤其重要**. 其实字典就可以构成一个简单的数据库(对于一个真实的数据库, 表的数据关系更为复杂而已), 在我们编写接口的时候, 也必然需要处理这种映射关系. 我们返回数据的 JSON 格式通常就是这三个数据结构的嵌套.
{% endhint %}

Python 的 for loop 语句相当于其它语言的 foreach, 我们还有一些方便的迭代函数, 如 zip, enumerate. 我们推荐你花时间去了解**迭代器(iterator)**的概念.

&#x20;文件 I/O 的格式也很简洁: 例如, 打开一个文件, 读取每一行, 并且记录行数, Python 可以这么写

```python
with open('foo.txt', 'r') as f:
    for lineno, line in enumerate(f, start=1): # line number tracking on a file
        # do something
# and you don't have to manually close the file here
```

还有错误处理的相关语句: try, except 和 finally.

Python 里的函数定义很简单, 你都不需要人为去指定参数类型(当然你也可以做标记, Type Hints). Python 的另外一个好处是灵活的函数参数. 例如, 一个_可变参数_以一个元组(或字典)的形式传入函数

```python
def touch_fish(*args, **kwargs):
    pass
touch_fish(1, 2, 3, arg1=4, arg2=5)
```

以上这些内容, 听说就是财大 Python 课程的全部. Nahida 听后, 立马把复习的讲义看了一遍, 然后将它们转换为了罐装知识放在这里.

<figure><img src="../.gitbook/assets/1681103534339.jpg" alt="" width="375"><figcaption><p>有不会的东西, 记得 STFW</p></figcaption></figure>

## Python OOP Basics

OOP(Object-Oriented Programming), 面向对象编程.

在之前的 Python 开发中, 基本上不需要用到面向对象的概念, 但是在实际的开发项目中, 类(class)这一概念非常重要.

Python 本身就是一个面向对象的语言. **所有的基础数据类型都是"对象"**.

```python
a = 'hello world'    # string object
a.upper()            # a method applied to the string

items = [1, 2, 3]    # list object
items.append(4)      # a method applied to the list

isinstance(5, int)
```

举个例子, 你要开发一款游戏, 现在设计怪物好了, 有史莱姆和哥布林, 现在需要你写代码, 你怎么写?

```python
slime_hp, slime_atk, slime_xp = 10, 2, 10
goblin_hp, goblin_atk, goblin_xp = 15, 3, 20
```

写完你发现这样太麻烦了. 每种怪物都有血量, 攻击, 和经验值的属性(attributes), 如果能封装一个怪物的模型?

于是就有了类 (class), 它类似于 C 语言里的结构体(struct). 但类不光能够存储异质的数据, 还能在类中声明函数:

```python
class Monster:
    def __init__(self, name, hp, atk, xp):
        self.name = name
        self.hp = hp
        self.atk = atk
        self.xp = xp
        
    def damage(self, pts):
        self.hp -= pts
```

这样之后创建一个怪物就简单多了. 例如

```python
slime = Monster('slime', 10, 2, 10)    # data
getattr(slime, 'name')                 # attritube access function
slime.damage(5)                        # behavior
```

这里 slime 就是 Monster 类的一个**实例**(Instance). 你可以用 `isinstance` 来判断这种关系.

`__init__` 称为构造函数, 当实例创建以后就会自动调用这个函数. 在大部分情况下, 它用来存放这个实例的数据, 也就是用来初始化的.

{% hint style="info" %}
实例在类内部按照惯例称为 `self`. 当然这个名称并不重要(改名为 `abc` 也没问题), 它总是作为第一个参数传入类内部的函数. 这个概念类似于 C++ 里的 `this` 指针.

另外, 类并不会定义一个作用域(scope), 如果你想要在类中的方法调用类的另一个方法, 这其实需要你对一个实例操作, 那么你**必须**显性引用它!

<pre class="language-python"><code class="lang-python"><strong>class Player:
</strong>    ...
    def move(self, dx, dy):
        ...
    def left(self, amt):
        self.move(-amt, 0)    # self is a must
</code></pre>
{% endhint %}

如果我要创建多个史莱姆, 那么可以再将史莱姆作为一个类封装起来!

类就是一系列函数的集合(当然这不意味着内部无法定义变量), 这些函数能够对所谓的"实例"进行操作.

{% hint style="info" %}
单独的类什么也做不到(就和单独的函数一样), 它只是作为一个定义存在.

但是, 通过一些方式, 你可以操作类自身, 例如**静态方法**(static method).

另外一种方式是**类方法**(class method). 它通常用作类的初始化, 它的第一个函数参数就是类本身(这对解决继承的问题很方便)

<pre class="language-python"><code class="lang-python"><strong>class Example:
</strong>    @classmethod
    def what(cls):
        print('what', cls)
</code></pre>
{% endhint %}

{% hint style="info" %}
**Python 和其它 OOP 语言的差别**

如果你熟悉其它 OOP 语言(例如 JAVA), 属性会有 private, public 关键字修饰, 对于 Python 来说, Python 仅有一套命名规则来限定.

```python
self._name = name     # we consider it as "private"
```

Python 里也有 property, 作为 accessor method 的另一种方法:

```python
@property
def shares(self):
    return self._shares
```
{% endhint %}

类的定义还有其它的几种形式:

* 利用 `__slots__` 去定义一个纯数据结构. 可以优化性能, 节省内存开销, 并且能够更快获取属性(内部是值引用实现的)

```python
class Monster:
    __slot__ = ('name', 'hp', 'atk', 'xp')
    def __init__(self, name, hp, atk, xp):
    # ...
```

此外还有 `dataclass`(方便存放 data object) 和 `typing.NamedTuple`(赋予不可变性) 的两种方式. 感兴趣的同学可以自行探索! 让我们继续回到主题.

OOP 另一个重要的思想是**继承(Inheritance)**. 假设我想给史莱姆设计一个特殊的机制, 例如弹跳, 这时候我要额外设计一个 slime 类, 但它同时需要怪物的这些属性和机制, 当然我们不需要复制粘贴过来, 继承机制就可以帮助我们实现这一点

```python
class Slime(Monster):
    def __init__(self, name, hp, atk, xp):
        super().__init__(name, hp, atk, xp)
    def bounce():
        atk += atk
```

这时候 Slime 类就称为是 Monster 类的一个_导出类_(derived class)或者称_子类_(subclass). 同理有_基类, 父类_和_超类_(superclass). 通过继承创建的对象是这个父类的一个特殊版本.

{% hint style="info" %}
有继承就有对父类的初始化. 需要调用 `super()` 方法. 同时, 父类的属性, 在子类中是不可见的, 但可以通过 name mangling 机制去获取.
{% endhint %}

简单来说, 继承能干什么: **拓展已有的代码**

继承还能够重载(override)函数, 支持多继承等.

代码复用的思想: 接口(Interface), 抽象基类(Abstract Base Class), 句柄类(handler class), 模板类......内容太多, 这里塞不下. 我们推荐你找一本OOP的书去深入学习.

## 装饰器(Decorators)

接下来我们会涉及一些 Python 元编程(Metaprogramming)的内容. 但是Don't panic! 这并没有你想象中那么吓人(至少不如C++), 而且在实际中, 我们并不会编写这一部分的代码.

啥是元编程? 简单来说, 就是**对代码编程**. 例如宏就是一个典型的元编程例子(当然Python里没有宏): 用一个指定的字符串替代一个代码

```c
#define PI 3.14159 // this code was written in C/C++
```

装饰器是一个函数, 只不过这个函数会创建一个 wrapper 把另一个函数围起来. wrapper 就是一个新函数, 和原函数功能基本一样, 只不过稍微多了些功能.

* wrapper 需要一个函数作为参数传入(是的, Python 的函数也是一个对象, 换作 C 语言那就是函数指针), 然后返回一个函数.
* 返回的这个函数返回我们传入的参数. 只不过在这个函数里, 它有些其他的行为.

下面就是一个装饰器的例子: 它能够在每次调用函数时打印信息

```python
def add(a, b):
    return a + b
    
# wrapper function
def logged(func):

    def wrapper(*args, **kwargs):
        print('Calling', func.__name__)
        return func(*args, **kwargs)
        
    return wrapper
```

然后你就可以这么去调用

```bash
>>> logged_add = logged(add)
>>> logged_add(3, 4)
```

所以装饰器其实就是一个语法糖

```python
def add(a, b):
    return a + b
add = logged(add)
```

```python
@logged
def add(x, y):
    return x + y
```

两段代码其实是一样的!

再贴一个常用的装饰器: 它可以显示程序运行的毫秒数

```python
def metric(fn):
    """running time for each main function"""
    
    @functools.wraps(fn)
    def wrapper(*args, **kw):
        print('start executing %s' % (fn.__name__))
        start_time = time.time()
        result = fn(*args, **kw)
        end_time = time.time()
        t = 1000 * (end_time - start_time)
        print('%s executed in %s ms' % (fn.__name__, t))
        return result
    return wrapper
```

为什么要使用装饰器:

* 调试找BUG. (例如上面打印 log)
* 避免代码重复. (不用你反复写调试的打印语句)
* 某一功能的开关. (例如上面讲到的 `classmethod`)

## 魔术方法(Magic methods)

> There is no magic in computer science.

魔术方法的另一种说法是 Special methods, 好吧, 这样就变得朴实无华了.

魔法方法允许你在类中, 将 `__<name>__` 这类的自定义函数(也叫做 Dunder methods)绑定到类的特殊方法中.

你觉得你没用过, 其实你天天都在用! 例如, 初始化一个类的 `__init__` 方法, 就是一个魔术方法.

这么说来, 怎么感觉和内置变量(builtins)很类似? 例如, 你应该知道 Python 的主程序入口是

```python
if __name__ == '__name__':
    # code
```

这里 \_\_name\_\_ 就是一个内置的变量, 它表示当前模块的名字([『if \_\_name\_\_ == "\_\_main\_\_"』到底啥意思❓](https://www.bilibili.com/video/BV1te4y1M7yx/)). 此外, 我们还有 `__doc__`, `__file__`, 等等.

那魔术方法其实也可以认为是 Python 内置的一些方法, 其实你只是在对它进行**函数重载**. 例如, 你自定义了一个二维向量类, 然后想实现一个向量加法, 那么你可以这么做

```python
class Vector2D:
    def __init__(self, x, y):
        self.x = x
        self.y = y

    def __str__(self):
        return f"({self.x}, {self.y})"

    def __add__(self, other):
        if isinstance(other, Vector2D):
            return Vector2D(self.x + other.x, self.y + other.y)
        else:
            raise TypeError("Unsupported operand type for +")
```

这里 `__add__` 实际上就是作为加法运算符的重载. `__str__` 是将对象转换为字符串时自动调用, 有点类似于 `ToString`.

利用魔术方法, 你可以实现很多东西:

* 实现对类属性的封装! 利用 `__getattr__`.
* 不用 `def` 也能实现一个函数! 利用 `__callable__`.
* 实现属于自己的一个容器, 例如实现一个双链表之类的! 你需要实现迭代器需要的方法. 例如 `__iter__`.

如果你感兴趣, 可以自行搜索这些话题.

## 值得学习的模块

* `re` 库: 正则表达式
* `os` 库: 操作系统接口
  * 我们推荐大家用 `os` 库作为 shell 脚本的一种替代.(更可读, 更好写)

`collections` 库: 能够帮助我们简化许多数据处理的操作.

一些操作例子:&#x20;

* `defaultdict` 对缺省的字典键初始化. 这个应该你在课堂上学过.
* `Counter` 计数特化的字典
* `deque` 双向队列
* `ChainMap` 多重查询

`Pathlib` 库: 面向对象的文件系统路径

* 跨平台开发很有用. 不需要你用 `os` 库去判断操作系统型号然后选用到底是 / 还是 \\.

其它待补充

## 补充学习

* [python-mastery](https://github.com/dabeaz-course/python-mastery): 一个不错的 Python 进阶教程仓库. 亮点在于教程和练习写的很详细.
* [cs-61a](https://cs61a.org/): UCB 非常优质的公开课程, 富含精心准备的教案和代码练习. 这门课用 Python 讲 SICP 这本书, 不仅能让你上手 Python 编程, 还带你真正进入 CS 的世界.
* [清华大学2023科协暑培 Python 基础](https://www.bilibili.com/video/BV1kF411Q71M/): b 站上刷到的, 讲得很细致, 同时也给出了[在线文档](https://docs.net9.org/courses/), 感觉和我们讲的东西很贴近, 大家也可以去观摩学习一下.
* Clean Code: A Handbook of Agile Software Craftsmanship: 有关 OOP 很好的一本参考书.
* 一本名字跟他很接近的书: [Clean Python](https://zhaochenyang20.github.io/pdf/clean%20python.pdf)
  * 我想找些和 Python 相关的书籍! [这个网站](https://pythonbooks.org/)推荐了适合各个阶段学习的书籍.
* [怎样理解 Python 里的装饰器?](https://www.zhihu.com/question/26930016)&#x20;
* [装饰器的超详细教学](https://www.bilibili.com/video/BV1Gu411Q7JV/): 同时推荐这个UP主, 资深 Python 用户.

