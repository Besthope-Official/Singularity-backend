---
description: 让代码写代码
---

# 泛型

大家在写代码的时候总是会遇到迫不得已的复制与粘贴。

{% hint style="danger" %}
这种复制粘贴是相当不推荐的行为。

大家在实现方法的时候, 都是把已有的代码复制好几份, 然后进行一些微小的改动(例如把`<<`改成`>>`)。

当你发现这些代码有bug的时候, 噩梦才刚刚开始.。也许花了好几天你又调出一个bug的时候, 才会想起这个bug你好像之前在哪里调过。你也知道代码里面还有类似的bug, 但你已经分辨不出哪些代码是什么时候从哪个地方复制过来的了。

这种糟糕的编程习惯叫Copy-Paste, 经过上面的分析, 相信你也已经领略到它的可怕了。事实上, [周源源教授](https://cseweb.ucsd.edu/\~yyzhou/)的团队在2004年就设计了一款工具CP-Miner, 来自动检测操作系统代码中由于Copy-Paste造成的bug。这个工具还让周源源教授收获了一篇[系统方向顶级会议OSDI的论文](http://pages.cs.wisc.edu/\~shanlu/paper/OSDI04-CPMiner.pdf), 这也是她当时所在学校UIUC史上的第一篇系统方向的顶级会议论文。

后来周源源教授发现, 相比于操作系统, 应用程序的源代码中Copy-Paste的现象更加普遍. 于是她们团队把CP-Miner的技术应用到应用程序的源代码中, 并创办了PatternInsight公司. 很多IT公司纷纷购买PatternInsight的产品, 并要求提供相应的定制服务, 甚至PatternInsight公司最后还被VMWare收购了.
{% endhint %}

这很大程度上是因为代码的逻辑有重复。这个时候我们会使用到函数——一种把相同逻辑封装一下的手法。这大大降低了我们的开发难度。但是有没有一种情况，就算函数也解决不了Copy-Paste了呢？

正如我们之前的引入中所说。当相同的逻辑应用在不同的类型上时连函数也无能为力了。由于函数自带的严格得类型检查，我们不得不写出大量重复的代码。

有没有一种办法解决这个问题呢？从直观的角度来看这些代码长相十分相近，我们可以用一个代码生成器生成这一系列的代码。十分的幸运的是，现代计算机语言已经内置了这个代码生成器，并且有一些语言如C++与java还额外提供了类型检查帮助程序员。

而这个想法的起源很早甚至可以追溯到宏的诞生。在纯粹的c语言工程中可以使用宏实现类似泛型的效果。而C++则使用函数模板，这是一种真正的泛型机制，也因此诞生了`.hpp`文件。而在java中使用了一种被称为类型擦除的技术实现泛型。

## 简单的泛型示例

先来看看泛型的好处吧

```java
private static <T extends Number> double add(T a, T b) {
    System.out.println(a + "+" + b + "=" + (a.doubleValue() + b.doubleValue()));
    return a.doubleValue() + b.doubleValue();
}
```

这一段代码可以等效于

```java
private static int add(int a, int b) {
    System.out.println(a + "+" + b + "=" + (a + b));
    return a + b;
}

private static float add(float a, float b) {
    System.out.println(a + "+" + b + "=" + (a + b));
    return a + b;
}

private static double add(double a, double b) {
    System.out.println(a + "+" + b + "=" + (a + b));
    return a + b;
}
...(还有很多很多函数)
```

那么java究竟是如何实现这一神奇的效果的呢？

## 类型擦除

{% hint style="info" %}
Java泛型这个特性是从JDK 1.5才开始加入的，因此为了兼容之前的版本，Java泛型的实现采取了“**伪泛型**”的策略，即Java在语法上支持泛型，但是在编译阶段会进行所谓的“**类型擦除**”（Type Erasure），将所有的泛型表示（尖括号中的内容）都替换为具体的类型（其对应的原生态类型），就像完全没有泛型一样。理解类型擦除对于用好泛型是很有帮助的，尤其是一些看起来“疑难杂症”的问题，弄明白了类型擦除也就迎刃而解了。
{% endhint %}

### **泛型的类型擦除原则**是：

* 消除类型参数声明，即删除`<>`及其包围的部分。
* 根据类型参数的上下界推断并替换所有的类型参数为原生态类型：如果类型参数是无限制通配符或没有上下界限定则替换为Object，如果存在上下界限定则根据子类替换原则取类型参数的最左边限定类型（即父类）。
* 为了保证类型安全，必要时插入强制类型转换代码。
* 自动产生“桥接方法”以保证擦除类型后的代码仍然具有泛型的“多态性”。

### 那么是如何进行擦除的呢？

* 擦除类定义中的类型参数 - 无限制类型擦除

<figure><img src="../../.gitbook/assets/image (8).png" alt=""><figcaption></figcaption></figure>

当类定义中的类型参数没有任何限制时，在类型擦除中直接被替换为Object，即形如`<T>`和`<?>`的类型参数都被替换为Object。

* 擦除类定义中的类型参数 - 有限制类型擦除

当类定义中的类型参数存在限制（上下界）时，在类型擦除中替换为类型参数的上界或者下界，比如形如`<T extends Number>`和`<? extends Number>`的类型参数被替换为`Number`，`<? super Number>`被替换为Object。

<figure><img src="../../.gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>

* 擦除方法定义中的类型参数

擦除方法定义中的类型参数原则和擦除类定义中的类型参数是一样的，这里仅以擦除方法定义中的有限制类型参数为例。

<figure><img src="../../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

## java泛型的上下限 <a href="#fan-xing-de-shang-xia-xian" id="fan-xing-de-shang-xia-xian"></a>

* **先看下如下的代码，很明显是会报错的** （具体错误原因请参考后文）。

```java
class A{}
class B extends A {}

// 如下两个方法不会报错
public static void funA(A a) {
    // ...          
}
public static void funB(B b) {
    funA(b);
    // ...             
}

// 如下funD方法会报错
public static void funC(List<A> listA) {
    // ...          
}
public static void funD(List<B> listB) {
    funC(listB); // Unresolved compilation problem: The method doPrint(List<A>) in the type test is not applicable for the arguments (List<B>)
    // ...             
}
```

那么如何解决呢？

为了解决泛型中隐含的转换问题，Java泛型加入了类型参数的上下边界机制。`<? extends A>`表示该类型参数可以是A(上边界)或者A的子类类型。编译时擦除到类型A，即用A类型代替类型参数。这种方法可以解决开始遇到的问题，编译器知道类型参数的范围，如果传入的实例类型B是在这个范围内的话允许转换，这时只要一次类型转换就可以了，运行时会把对象当做A的实例看待。

```java
public static void funC(List<? extends A> listA) {
    // ...          
}
public static void funD(List<B> listB) {
    funC(listB); // OK
    // ...             
}
```

* **泛型上下限的引入**

在使用泛型的时候，我们可以为传入的泛型类型实参进行上下边界的限制，如：类型实参只准传入某种类型的父类或某种类型的子类。

上限

```java
class Info<T extends Number>{    // 此处泛型只能是数字类型
    private T var ;        // 定义泛型变量
    public void setVar(T var){
        this.var = var ;
    }
    public T getVar(){
        return this.var ;
    }
    public String toString(){    // 直接打印
        return this.var.toString() ;
    }
}
public class demo1{
    public static void main(String args[]){
        Info<Integer> i1 = new Info<Integer>() ;        // 声明Integer的泛型对象
    }
}
```

下限

```java
class Info<T>{
    private T var ;        // 定义泛型变量
    public void setVar(T var){
        this.var = var ;
    }
    public T getVar(){
        return this.var ;
    }
    public String toString(){    // 直接打印
        return this.var.toString() ;
    }
}
public class GenericsDemo21{
    public static void main(String args[]){
        Info<String> i1 = new Info<String>() ;        // 声明String的泛型对象
        Info<Object> i2 = new Info<Object>() ;        // 声明Object的泛型对象
        i1.setVar("hello") ;
        i2.setVar(new Object()) ;
        fun(i1) ;
        fun(i2) ;
    }
    public static void fun(Info<? super String> temp){    // 只能接收String或Object类型的泛型，String类的父类只有Object类
        System.out.print(temp + ", ") ;
    }
}
```

**小结**

```java
<?> 无限制通配符
<? extends E> extends 关键字声明了类型的上界，表示参数化的类型可能是所指定的类型，或者是此类型的子类
<? super E> super 关键字声明了类型的下界，表示参数化的类型可能是指定的类型，或者是此类型的父类

// 使用原则《Effictive Java》
// 为了获得最大限度的灵活性，要在表示 生产者或者消费者 的输入参数上使用通配符，使用的规则就是：生产者有上限、消费者有下限
1. 如果参数化类型表示一个 T 的生产者，使用 < ? extends T>;
2. 如果它表示一个 T 的消费者，就使用 < ? super T>；
3. 如果既是生产又是消费，那使用通配符就没什么意义了，因为你需要的是精确的参数类型。
```

* 再看一个实际例子，**加深印象**

```java
private  <E extends Comparable<? super E>> E max(List<? extends E> e1) {
    if (e1 == null){
        return null;
    }
    //迭代器返回的元素属于 E 的某个子类型
    Iterator<? extends E> iterator = e1.iterator();
    E result = iterator.next();
    while (iterator.hasNext()){
        E next = iterator.next();
        if (next.compareTo(result) > 0){
            result = next;
        }
    }
    return result;
}
```

上述代码中的类型参数 E 的范围是`<E extends Comparable<? super E>>`，我们可以分步查看：

* 要进行比较，所以 E 需要是可比较的类，因此需要 `extends Comparable<…>`（注意这里不要和继承的 `extends` 搞混了，不一样）
* `Comparable< ? super E>` 要对 E 进行比较，即 E 的消费者，所以需要用 super
* 而参数 `List< ? extends E>` 表示要操作的数据是 E 的子类的列表，指定上限，这样容器才够大
* **多个限制**

使用&符号

```java
public class Client {
    //工资低于2500元的上斑族并且站立的乘客车票打8折
    public static <T extends Staff & Passenger> void discount(T t){
        if(t.getSalary()<2500 && t.isStanding()){
            System.out.println("恭喜你！您的车票打八折！");
        }
    }
    public static void main(String[] args) {
        discount(new Me());
    }
}
```
