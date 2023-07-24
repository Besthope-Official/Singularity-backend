---
description: 自己的事情自己做
---

# 面向对象

面向对象构造了一个权责分明的理想世界。他使用封装，继承，多态的技巧使得我们能够完善的抽象这个世界。

{% hint style="success" %}
这是一个可选任务：使用c语言实现封装继承多态！具体的细节请自己查找。完成之后相信你会对这三个特性有更深的了解。如果自己实现不了也可以看其他人的源码，看源码是提升最快的方法！
{% endhint %}

## 封装

封装并不难，不过就是把类的属性聚合起来，把这个类能做的事抽象成方法然后决定暴露出什么样的接口。但是一个好的封装却很难。

### 什么是好的封装

* 定义变量不会污染外部：封装的首要目的是保护我们的变量不会被外部所污染，也不会污染外部的变量，在大型项目中很难保证变量名不和别人的变量名起冲突，所以说这点很重要。
* 能够作为一个模块调用：除了上面那点之外，良好的封装才能保证我们的代码作为独立的模块与外部沟通，把程序变成模块架构中的基础就是良好的封装成模块。
* 遵循开闭原则：对修改进行关闭，对扩展进行开放，我们的代码要允许使用者进行扩展而不允许使用者进行修改，开闭原则建立在封装的基础上，只有在良好的封装的情况下才能保证代码没法被进行直接修改。

\
总结起来一共六个字：**不可见、留接口**

### **创建对象时的几个模式**

#### **工厂模式**：

既然叫做工厂，其实就是创造一个专门用来创建对象的工厂，我们通过这个工厂来获取对象，从而代替我们使用new操作符或者是代替我们通过手写来生成对象。

**目的**：方便我们大量创建对象。

**应用场景**：工厂肯定是要大批量的产出某样东西，所以说当你需要创建的对象需要大批量生成时，应该首先想到工厂模式。比如我们项目中有很多的弹窗，我们应该把我们弹窗组件封装成工厂模式提供给别人使用，当别人需要调用弹窗的时候只需要调用工厂就能拿到弹窗对象了。

一个试用动物类的工厂如下所示：

```java
//三个动物类
class Cat extends Animal{
    ...
}

class Dog extends Animal{
    ...
}


class Bird extends Animal{
    ...
}

Animal animalFactory(TYPE type, ...){
    Animal res;
    switch(type){
        case CAT:
            res = new Cat(...);
            break;
        case DOG:
            res = new Dog(...);
            break;
        case BIRD:
            res = new Bird(...);
            break;
    }
    return res;
}
```

#### **建造者模式**：

工厂模式用来大量产出类似对象，这些对象可能只是内容不同，其它部分大致相同，而建造者模式截然相反，建造者我们首先想到造房子，造房子肯定是精细化建造，不可能大批量产出房子，所以说建造者模式其实是一种精细化构建的思想。

**目的**：指导我们通过组合构建一个复杂的全局对象

**应用场景**：当需要创建单个、庞大的可能并不需要大量产出的组合对象时，就可以考虑使用建造者模式，它与工厂模式分别对应于编程中产出对象的两个方面。例如：写一个复杂的轮播图，轮播图一般一个页面只有一个，而轮播图有各种各样的动画效果以及各种复杂的交互，此时采用建造者模式封装代码是非常好的。

以上是两个帮助我们创建对象的设计模式，在创建完对象之后往往需要一些特殊性的需求，例如：需要保障对象在全局有且仅有一个。这个时候就会有单例模式。

#### **单例模式**：

为什么要确保全局有且只有一个对象呢？因为在很多情况下，我们为了避免出现多个对个对象之间发生干扰，此时我们往往需要保证全局只有一个对象。

**目的**：确保全局有且仅有一个对象

**应用场景**：为了避免重复新建，避免多个对象存在互相干扰。例如：当在项目中重复的去new同一个类，会导致多个对象之间存在干扰，这个时候可以考虑使用单例模式。单例模式的做法并不是很固定，更重要的是要记住它全局只有一个对象的思想。

## 继承

继承实现了 **IS-A** 关系，例如 Cat 和 Animal 就是一种 IS-A 关系，因此 Cat 可以继承自 Animal，从而获得 Animal 非 private 的属性和方法。

继承应该遵循里氏替换原则，子类对象必须能够替换掉所有父类对象。

Cat 可以当做 Animal 来使用，也就是说可以使用 Animal 引用 Cat 对象。父类引用指向子类对象称为 **向上转型** 。

```java
Animal animal = new Cat()
```

正如之前factory的使用一样。

类除了可以继承类以外也同样可以实现接口，只不过是用implements完成。

一个显著的不同是类只允许单继承，而接口允许多继承！

{% hint style="warning" %}
想一想这是为什么！为什么类只能单继承而借口可以多继承？如果像C++一样允许了类的多继承会发生什么问题？又会带来什么好处？
{% endhint %}

## 多态

多态分为编译时多态和运行时多态:

* 编译时多态主要指方法的重载
* 运行时多态指程序中定义的对象引用所指向的具体类型在运行期间才确定

运行时多态有三个条件:

* 继承
* 覆盖(重写)
* 向上转型

下面的代码中，乐器类(Instrument)有两个子类: Wind 和 Percussion，它们都覆盖了父类的 play() 方法，并且在 main() 方法中使用父类 Instrument 来引用 Wind 和 Percussion 对象。在 Instrument 引用调用 play() 方法时，会执行实际引用对象所在类的 play() 方法，而不是 Instrument 类的方法。

```java
public class Instrument {
    public void play() {
        System.out.println("Instrument is playing...");
    }
}

public class Wind extends Instrument {
    public void play() {
        System.out.println("Wind is playing...");
    }
}

public class Percussion extends Instrument {
    public void play() {
        System.out.println("Percussion is playing...");
    }
}

public class Music {
    public static void main(String[] args) {
        List<Instrument> instruments = new ArrayList<>();
        instruments.add(new Wind());
        instruments.add(new Percussion());
        for(Instrument instrument : instruments) {
            instrument.play();
        }
    }
}
```

