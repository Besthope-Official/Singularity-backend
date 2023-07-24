---
description: ^_^
---

# 作业

## Git

1. 试着理解并总结 Git 的底层原理. 我们推荐你去阅读 [Pro Git](https://git-scm.com/book/zh/v2/Git-%E5%86%85%E9%83%A8%E5%8E%9F%E7%90%86-%E5%BA%95%E5%B1%82%E5%91%BD%E4%BB%A4%E4%B8%8E%E4%B8%8A%E5%B1%82%E5%91%BD%E4%BB%A4) 关于这一部分的讲解.
2. 创建一个属于自己的 Git 仓库! 然后在你的机子上试着执行我们文档里给出的指令. 看看会发生什么! 这也是熟悉命令行工具的一环.
3. 熟悉 Git 在 IDE 里的使用方式. 完成各大 IDE 文档给出的教程内容. 在接下来的课程中, 我们都会改用 IDE 进行 Git 操作.
4. 完成 [Learn Git Branching](https://learngitbranching.js.org/) 的全部练习. 分支操作很有用!
5. 这里是本次[训练营文档的仓库](https://github.com/Besthope-Official/Singularity-backend). 我们在这个仓库里藏了一个小彩蛋, 看看你能通过什么方式找到它! (其实方法很简单) 如果找到了, 你可以 fork 一下然后开一个 Pull Request 向我们提交(如果你发现文档里有错误, 可以用相同的方式提交).
6. 看看是哪位部长创建了 `zuo-ye.md` :)
7. 创建一个 `git log --all --graph --decorate --oneline` 的别名 `git graph`. 你可以在 `~/.gitconfig` 去配置(不要去修改 `~/.bash_profile`).
8. 使用 Git 的一个常见错误是提交不应该由 Git 管理的大文件, 例如视频文件, 或者把敏感信息(例如数据库的密码)提交给了 Git. 先前我们提到 Git 会把一切对象存储到仓库内, 撤回也只是新建一个抹除文件后的快照, 这些内容仍然存储在历史记录内. 试着把某一个文件彻底地从历史记录中删去. (参考[这篇文章](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/removing-sensitive-data-from-a-repository))

## Python

1. 完成 CS-61A 的一个 [OOP Lab](https://cs61a.org/lab/lab08/). 按照文档指示一直做到最后, 同时完成自动化评测. 注意: 你需要添加一个 `--local` 的标记(因为我们不是UCB的学生).
2. 尝试去阅读并完成 [Python-mastery](https://github.com/dabeaz-course/python-mastery) 的练习.
3. 之前我们提到了, Git 里的一切都是对象. 你会发现, 我们可以用 OOP 的思想去设计一个 Git 的模型:

* 设计一个基类 Object, 然后它的子类 Blob, Tree, Commit.
* 你只需要设计 Template. 换句话说不需要你实现, 但你需要把接口设计出来.
* 学习下 `argparse` 或者 `click` 这个库. 试着能够在终端里像执行 git 一样执行你的应用程序. (创建一个 `mygit` 文件, 调用 `mygit_lib.py` 然后将它添加到你的 PATH)你只需要实现一个打印功能, 例如下面的演示

```bash
$ mygit init
This is mygit init
```

* 如果你愿意进一步探索, 可以去实现一些简单的 Git 功能. 参考我们对 Git 底层原理的讲解, 以及 [wyag](https://wyag.thb.lt/) 的项目.

实践环节: 上传到你的 Git 仓库! 就算你还是 WIP, 照样可以上传. 如果你愿意, 可以分享出来. 说不定你还可以拉上几个小伙伴一起团队开发一个 Git 呢!

## JAVA

1. 深入理解java的面向对象部分，并自己实现一个基于继承的工厂模式。要求能够生产至少3个类别。
2. 更进一步的理解泛型，自己动手实现一个基于泛型的计算器。这个计算器要求包含加减乘除功能，使用命令行交互。

{% hint style="success" %}
进阶版本：

1. 想一想该如何提供一个更友好的交互界面，如像python的交互模式一样区分输入与输出。
2. 你可能只实现了两个数的加减乘除，该如何分析更长的表达式呢？

[一个提示](https://nju-projectn.github.io/ics-pa-gitbook/ics2022/1.5.html)：这是一个南京大学提供的硬核pa实验，里面给出了基本的表达式分析功能，可以作为参考。

3. 该如何验证你的表达式的正确性？

[单元测试](https://zh.wikipedia.org/zh-cn/%E5%8D%95%E5%85%83%E6%B5%8B%E8%AF%95)：所有未经测试的代码都是不正确的，想办法自己实现一个单元测试来检查你的实现。
{% endhint %}

3. 反射部分需要大家实现一个反编译器，不过只是基础版本大家不要担心。你需要实现反汇编一个类的import部分、属性部分、方法部分并将其格式化（即标准的java缩进），最后在输出到一个`.java`文件中即可。

该如何反编译呢？你只需要先写出一个真正的类（比如在第一项作业中实现的factory），然后使用java的反射机制，在运行时将整个类的信息读取出来，具体的做法请详细阅读我们提供的[反射](java-jin-jie/fan-she.md)一节。再使用一些字符串拼接的技巧，拼出完整的类再输出出来即可。

4. 注解部分基础要求为仔细阅读注解的原理与接口。详细描述注解如何发挥作用并提交即可。

{% hint style="success" %}
进阶部分：上面的第三部分中实现了一个弱小的反编译器将其从class格式转换为人类能阅读的形式。事实上类还可以用别的方式存储比如[json格式](https://www.json.org/json-en.html)。请使用注解提供一个快速将json格式文件转换为java类的工具。如果你不知道该怎么做请阅读[fastjson](https://www.runoob.com/w3cnote/fastjson-intro.html)的源码。

PS：这个东西难度确实有点大^\_^
{% endhint %}
