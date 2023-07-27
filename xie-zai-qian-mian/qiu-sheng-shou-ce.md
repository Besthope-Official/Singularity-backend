# 求生手册

## Survival Tips

{% hint style="info" %}
**指导准则**: _Don't panic._

我想, 在大家的游戏生涯里肯定遇到过许多_挫折_的时刻, 例如在团队竞技里大逆风, 在硬核的游戏里疯狂受苦, 等等. 同时, 在你的学习生涯里, 也必然会遇上艰深的内容, 遇上难解的试题......

这些时刻会让人感到沮丧, 你很可能会决定放弃, 开摆. 想想你是怎么挺过这些至暗时刻的! 我们本次的训练营希望传递一个信息: 那就是**不要怕**. **坚持下去!**
{% endhint %}

### 仔细阅读本文档

可能细心的你已经发现了, 文档里存在大量的外部链接. 请**不要认为**它们**无关紧要**! 通常来说它们是我们文章的参考资料, 以及一些_验证过好用的_工具和优秀的学习资源.

{% hint style="info" %}
其实你也_完全可以抛弃_本文档转去自学这些资料. (_yes!_)我们更多地是为大家提供一条比较合适的学习路线, **并不是什么权威**, 市面上有更多比我们适合的教材和课程.
{% endhint %}

仔细阅读, 你可以挖掘很多潜在的信息. 有些问题的答案你可以在文档里找到. 同时, 保不准你能发现一些 Typo 或者错误. 这对于我们双方都是有益的!

### STFW

如果你不知道这四个字符的含义, 那么, 你会怎么做?

同样的, 在文档里, 你也很可能会遇见自己不认识或者不了解的概念. 在大部分情况下, 我们会像上面一样不多做解释.

动手检索是一项_极为重要_的能力, 它可以节省你大量的时间与人交流. 如今大部分的问题你都可以在 [StackOverflow ](https://stackoverflow.com/)类似的技术网站上找到解决方案.

在这个过程中, 你可能需要了解一些搜索引擎的技巧, 例如 `-csdn`, `site:zhihu.com` 等. 一个合适的搜索引擎 Edge(海外版), Google 也会帮助巨大.

{% hint style="warning" %}
当然你需要一些方法进行科学上网. 虽然这不是必须的, 但它同样可以帮助你节省大量的时间.
{% endhint %}

其实还有RTFM的说法, 我们希望大家能够变为**不需要教程也能解决问题**的猛男, 这时候你唯一的参考资料可能就是这些天书般长的文档. 同时, 后端天生就需要编写文档, 学会阅读文档也是大有裨益的一件事.

2023年了, 你还可以 AskGPT! 如果你看不懂示例代码, 可以问问 GPT 会怎么理解. (但在使用的时候, 记得小心求证)

<figure><img src="../.gitbook/assets/chat.jpeg" alt=""><figcaption><p>THE UNDERTAKER!</p></figcaption></figure>

补充阅读: [提问的智慧](https://lug.ustc.edu.cn/wiki/doc/smart-questions/) 在提问之前, 至少表现得你为了解决问题付出了相当的努力.

当然了, 不要因此而对提问感到羞愧或恐惧. 小伙伴们都是很 nice 的. 就算我们不会, 我们还可以找外援, 在社交媒体上发问, 等等.

### 配置好你的编程环境

接下来我们会给出一个完备的编程环境清单(checklist). 你可以按照自己的情况添加补充.

### 安装好必备的工具

* 安装好 JDK(其实IDEA安好了是自带的)/Python 3.6 以上的版本.
* 对于JAVA 方向的同学, 安装好 Maven. 这是一个很方便的依赖管理工具. 参考[这篇文章](https://zhuanlan.zhihu.com/p/615382243).
  * 对于 Python 方向的同学, 你需要安装 pip, 但 Python 自带 :)
* 安装好 MySQL 8.0 以上版本. 它是本次训练营将用到的数据库管理系统.
  * 虽然说主流版本是 5.7, 但我们并不需要考虑兼容相关的问题.
  * 如果你是 WSL, 安装方式很简单, 只需要你[按照文档](https://learn.microsoft.com/zh-cn/windows/wsl/tutorials/wsl-database) `apt install` 即可 (这也是为什么我们推荐你使用 Linux).
  * 如果你是 Windows, 参考[这篇文章](https://blog.csdn.net/m0\_52559040/article/details/121843945). 唯一要注意的一点是, 你需要将 mysql 加入到你的系统环境变量中.

{% hint style="warning" %}
2023年了, 你不需要手动的去修改 `my.ini`等相关配置文件! 你要避开这些历史遗留的教程.
{% endhint %}

* Git: 重要的版本控制系统. 至少在任何CS开发相关的工作中你都会用上它.
  * 在你的开发系统上(例如 WSL 上)[安装](https://git-scm.com/book/zh/v2/%E8%B5%B7%E6%AD%A5-%E5%AE%89%E8%A3%85-Git)好一个 Git, 然后完成 Git 的初次[配置](https://git-scm.com/book/zh/v2/%E8%B5%B7%E6%AD%A5-%E5%88%9D%E6%AC%A1%E8%BF%90%E8%A1%8C-Git-%E5%89%8D%E7%9A%84%E9%85%8D%E7%BD%AE). 下一周我们会对 git 进行一个较为详尽的介绍 (buttom up)!
* 安装好 ssh server: 在后面我们可能会讲到连接服务器的相关操作.
* [安装](https://zhuanlan.zhihu.com/p/605649220)好 [Postman](https://www.postman.com/): 一个很强大的测试工具
  * 虽然说 curl 或者浏览器模拟请求也是可以的, 但 Postman 确实值得我们学习.
* 安装好 [Docker](https://zhuanlan.zhihu.com/p/82269806): 它是一个很强大的容器化工具, 轻量, 且方便构建.

### 选择一个好的 IDE

> 工欲善其事，必先利其器.

一个好的 IDE 能够节省你大量的时间与精力. 虽然不是硬性要求, 但我们推荐你使用专门的开发工具.

我们举个例子, Python 开发你可以使用 [IDLE](https://docs.python.org/3/library/idle.html)(官方自带的代码编辑器), 经过配置以后, 也可以实现代码补全, 快捷键, Code Snippets 等功能.

或者你也可以用 vim (去配置 vimrc, 你还有 neovim 可以用), nano 这样的文本编辑器(_我想你可能甚至没听过_), 这也是一种选择, 但显然配置也很复杂(你甚至可以[把 vim 配置成 vscode](https://juejin.cn/post/7064009400075157535#heading-35) 的模样), 并且操作也显然不是一个 beginner 能够立马接受的.

<figure><img src="../.gitbook/assets/vim-meme (1).png" alt=""><figcaption><p>Vim 很强大, 但学起来也是真的累 (</p></figcaption></figure>

我们更推荐你使用 [Anaconda](https://www.anaconda.com/)(或者 [miniconda](https://docs.conda.io/en/latest/miniconda.html)), [Visual Studio](https://visualstudio.microsoft.com/zh-hans/vs/features/python/), [Visual Studio Code](https://code.visualstudio.com/)(严格上说它不算是IDE, 但配置好插件后等同于一个IDE), 或者 [PyCharm](https://www.jetbrains.com/pycharm/download/) 作为你的 Python 开发环境. 使用这些 IDE 的好处就在于简单, 方便, 同时足够强大.

{% hint style="warning" %}
Jupyter Notebooks 很强大, 但它不适合作为我们完整项目的开发环境, 因为我们需要多文件引用, 多模块编程. 它更适合AI方向的同学进行数据分析以及可视化展示.
{% endhint %}

值得一提的是, Pyharm 可以通过学生认证免费获得 Jetbrain 全家桶专业版: [学生专业版申请指南](https://blog.jetbrains.com/zh-hans/blog/2022/08/24/2022-jetbrains-student-program/)

对于Java的开发环境, 我们推荐 Jetbrain 的 [IntelliJ IDEA](https://www.jetbrains.com/idea/), 按照上面的方法你也可以白嫖到专业版.

你也可以按照下面的方法申请 [Github 学生包](https://zhuanlan.zhihu.com/p/578964972), 然后用这个 Github 账号登录就可以获取学生专业版了.

### 一个Linux系统会有帮助

虽然不是必要的, 但一个 Linux 的编程环境能够让你理解更多 CS 相关的内容(例如命令行, 服务器等). 你有下面的这些选择:

* 在物理机上直接安装 Linux
* 整一个 Linux 虚拟机 (VMWare, Virtual Box)
* 安装 Windows Subsystem for Linux (wsl) ([推荐](https://zhuanlan.zhihu.com/p/337104547)的教程)
  * 微软推出的 [WSL 文档](https://learn.microsoft.com/zh-cn/windows/wsl/)
* 在网络上整一个 Linux 虚拟主机, 例如 [Lightly](https://lightly.teamcode.com/?f=jb51)

本次训练营的许多内容都将在WSL Ubuntu 22.04.6 LTS上展示.

{% hint style="info" %}
Q: 为什么文档里的这段代码在我的电脑上报错啊?

```powershell
PC C:\> export FLASK_APP=hello.py
export : The term 'export' is not recognized as the name of a cmdlet, function, script file, or operable program. Check the spelling of the name, or if a p
ath was included, verify that the path is correct and try again.
At line:1 char:1
...
```

A: 我们是 Unix 系统下的工作流, 你可能需要一个 WSL 或者买一个 Mac. (这并不是因为我们很残忍, 但这确实更有利于大家去学习)
{% endhint %}





<figure><img src="../.gitbook/assets/R-C.jpg" alt=""><figcaption><p>Yes, it's me</p></figcaption></figure>

### 熟悉CLI相关工具

在你安装完了 Linux 之后, 你就可以开始着手试试这些命令行工具了!

可以提前了解下 [git](broken-reference).

一个合适的终端: [zsh](https://zhuanlan.zhihu.com/p/441676276)

上面提到的 vim. 试试在 bash 里键入 vimtutor

[The art of command line](https://github.com/jlevy/the-art-of-command-line/blob/master/README-zh.md)

[Modern Unix](https://github.com/ibraheemdev/modern-unix)

查询文档的工具: tldr 或者 man
