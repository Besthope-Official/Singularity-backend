# 介绍 Git

{% hint style="info" %}
在阅读本篇之前, 确保你已经安装好了 Git 并且配置了`.gitconfig` (邮箱及用户等).
{% endhint %}

动机: 怎么多人协作?

* 不是像几个人做英语的小组作业那样灾难
  * 一个文件被传几遍. 改了什么内容?
  * 这段文案是谁改的...? 为啥要改?
  * ...

<figure><img src="../../.gitbook/assets/什么文件管理.png" alt=""><figcaption><p>没有版本控制的灾难</p></figcaption></figure>

有了版本管理系统(VCS)就能解决大部分上面的问题.

* 别人都干了些啥?
* 同步项目
* 解决并行开发引起的冲突

我不和别人协作!

* 项目快照
* 记录改动目的
* 多分支并行开发

如果愿意, 你也可以用 Git 打造专属自己的一套[个人笔记系统](https://orangex4.cool/post/lesson-zero-for-cs-students/)!

<figure><img src="../../.gitbook/assets/git_zh.png" alt=""><figcaption><p><a href="https://xkcd.com/1597/">XKCD 漫画</a>, 自嵌</p></figcaption></figure>

* Git 这么强大, 那要怎么实现 Git?
* 这是我该问的问题吗?
  * 为Git祛魅: [Write Yourself a Git!](https://wyag.thb.lt/) 563 行 Python 代码实现了 Git 的底层机制.

大部分的 Git 教程通常都是自上而下的: 从命令行接口开始, `git add/commit/push/fetch/pull`...

看起来你学到了很多, 但事实上, 你还是一个接口侠(_这也是我们后端最不愿意看到的_). 或者, 一旦学深入, 就不得不涉及到一些底层的原理. Git 被人诟病 "leaky abstraction", 就是因为它的接口太过丑陋 (就连Linus 本人也吐槽这一点).

虽然说现代 IDEs (例如Jetbrain, vscode) 已经提供了 Git 图形化的界面, 但是这不妨碍很多人依然不会.

{% hint style="warning" %}
什么是 leaky abstraction 泄露抽象?&#x20;

简单来说, 抽象层次的不完全封装, 导致底层细节和实现细节对上层抽象产生意外影响或泄漏出来, 这就是泄露抽象.

举一个例子, `matching_user(id:int)`是你写的一个匹配指定用户的函数, 你只需要传入用户 `id` 即可完成匹配, 但如果你写成 `matching_user(pattern:SQL_query)`, 这个接口就是泄露抽象的.

作为一名合格的后端人员, 你应当避免泄露抽象的出现.
{% endhint %}

本次的介绍会从 Git 的底层设计和模型开始讲起.

* Git 的后端模型很简洁.
* 理解一定的原理, 至少能让你在复制命令的时候不会感到一脸茫然.

### 存储结构

* 文件内容: Blob 对象 (**B**inary **L**arge **OB**ject)
* 对象: 对象按照 [SHA1-Hash](https://en.wikipedia.org/wiki/SHA-1#Data\_integrity) 寻址
* **Git 的一切都是对象**

不同于传统的文件存储系统: key-value

* 文件的内容是 value
* 文件或目录有一个唯一的名称/路径作为 key

你不是修改了文件, 而是**在另一个位置新建了文件**

* 文件的内容是 value
* 根据内容计算一个 hash key, 这个哈希值相当于路径
* 这就是 Git 的后端模型: 内容寻址文件系统(IPFS) (_听起来很吓人_)

{% hint style="info" %}
什么是哈希函数? `strlen()` 就是一个哈希函数, 你可以知道字符串的长度, 但永远不知道原字符串是长什么样的. 你可以想象哈希函数就是_数据的身份证_, 它唯一, 并且不可逆. 利用这些特点, SHA-1哈希值就是一个对象的唯一标识符.
{% endhint %}

## 示例1: `git hash-object/cat-file`

这个示例中我们会演示两个"底层" (plumbing) 的指令: `git hash-object` 和 `git cat-file`. 即便这两个命令在我们日常使用中_几乎_不会见到, 但是对于我们了解 Git 的底层机制来说是很有用的.

在这个示例中, 一个文本被 hash 化, 同时作为一个新的数据对象被存储进 Git 数据库中(`.git/objects/`). 然后我们又用 `cat-file` 取回了原数据.

```bash
$ git init git_demo
Initialized empty Git repository in /home/besthope/git_demo/.git/
$ cd git_demo
$ echo 'hello git' | git hash-object -w --stdin
8d0e41234f24b6da002d962a26c2495ea16a425f
$ git cat-file -p 8d0e41234f24b6da002d962a26c2495ea16a425f
hello git
```

你可能会好奇 `hash-object` 是怎么做到的. 实际上这并不黑科技, 我们可以用 Python 简单地去实现:

```bash
>>> import hashlib
>>> data = 'hello git'
>>> content = f'blob {len(data)}\x00{data}'
>>> content
'blob 9\x00hello git'
>>> hashlib.sha1(content.encode()).hexdigest()
'f09e9c379f5fe8f4ce718641c356df87906d87a6'
>>>
$ echo -n "hello git" | git hash-object --stdin
f09e9c379f5fe8f4ce718641c356df87906d87a6
```

然后是数据的存储. 这条文本消息会用 zlib 进行压缩, 并写入指定的路径. 用 Python 代码表示就是:

```python
# .git/objects/f0/9e9c379f5fe8f4ce718641c356df87906d87a6
path = repo_file(obj.repo, "objects",
                 sha[0:2], sha[2:])

with open(path, 'wb') as f:
    f.write(zlib.compress(result))
```

让我们再用实际的一个例子来演示:

```bash
$ echo 'version 1' > a.txt
$ git hash-object -w a.txt
83baae61804e65cc73a7201a7252750c76066a30
$ echo 'version 2' > a.txt
$ git hash-object -w a.txt
1f7a7a472abf3dd9643fd615f6da379c4acb3e3a
```

```bash
$ find .git/objects -type f
.git/objects/1f/7a7a472abf3dd9643fd615f6da379c4acb3e3a
.git/objects/83/baae61804e65cc73a7201a7252750c76066a30
```

```bash
$ git cat-file -p 83baae61804e65cc73a7201a7252750c76066a30 > a.txt
$ cat a.txt
version 1
```

这就已经是一个简单的版本控制系统了! 并且我们看到了, 在Git版本控制系统中, 文件都以对象存储. 但是这种方法有一个问题: 文件名, 以及文件所在的路径的信息并没有被保存. 为了解决这个问题, 于是引入了**树对象** (tree object):

* 历史记录的基本元素: **树(快照)**
  * 一棵树可以包含其它树, 以及文件. (树是_递归定义_的!)
  * 树将文件名和 hash 值进行了关联. 正如下图所示:

<figure><img src="../../.gitbook/assets/data-model-2.png" alt=""><figcaption><p>树模型, 让Blob对象和文件名称关联, 同时它还可以包含其它树, 图来自 Pro Git</p></figcaption></figure>

接下来介绍的**提交**(Commit), 可能是Git中最重要的一个概念. 但有了上面的基础, 它很好理解: 实际上就是一棵树, 附带一些其它信息, 例如提交者, 提交时间等.

## 示例 2: Object Walkthrough

{% hint style="success" %}
不用对看不懂示例中的指令感到担忧, 你很快就会上手并且_变着花地_去使用这些 Git 指令.
{% endhint %}

我们用一个现有的 Git 仓库来演示这些对象的概念. 看看 [Linus 的 first commit](https://github.com/git/git/commit/e83c5163316f89bfbde7d9ab23ca2e25604af290): Git 的源码在 Github (代码托管网站) 有存档, 你可以将其克隆下来:

<pre class="language-bash"><code class="lang-bash"><strong>$ git clone https://github.com/git/git.git
</strong><strong>...
</strong><strong>$ git log --reverse
</strong>commit e83c5163316f89bfbde7d9ab23ca2e25604af290
Author: Linus Torvalds &#x3C;torvalds@linux-foundation.org>
Date:   Thu Apr 7 15:13:13 2005 -0700

    Initial revision of "git", the information manager from hell

</code></pre>

如果你去查看这条 commit 记录(运用我们上面提到的 `cat-file` 命令), 它的结果

```bash
$ git cat-file -p e83c5163316f89bfbde7d9ab23ca2e25604af290
tree 2b5bfdf7798569e0b59b16eb9602d5fa572d6038
author Linus Torvalds <torvalds@ppc970.osdl.org> 1112911993 -0700
committer Linus Torvalds <torvalds@ppc970.osdl.org> 1112911993 -0700

Initial revision of "git", the information manager from hell
```

```bash
$ git cat-file -p 2b5bfdf7798569e0b59b16eb9602d5fa572d6038
100644 blob a6bba79ba1f46a1bbf7773449c3bd2bb9bf48e8b    Makefile
100644 blob 27577f76849c09d3405397244eb3d8ae1d11b0f3    README
100644 blob 98a32a9ad39883c6d05a000a68511d4b1ee2b3c7    cache.h
100644 blob 74a0a234dd346fff51c773aa57d82fc4b83a8557    cat-file.c
...
```

```bash
$ git cat-file -p 8bc9a0c769ac1df7820f2dbf8f7b7d64835e3c68
tree 4f856748145b27bc669a98cd4d66574dfd01b083
parent e83c5163316f89bfbde7d9ab23ca2e25604af290
author Linus Torvalds <torvalds@ppc970.osdl.org> 1112912170 -0700
committer Linus Torvalds <torvalds@ppc970.osdl.org> 1112912170 -0700

Add copyright notices.

The tool interface sucks (especially "committing" information, which is just
me doing everything by hand from the command line), but I think this is in
theory actually a viable way of describing the world. So copyright it.
```

你会发现这里的 `parent e83c5163316f89bfbde7d9ab23ca2e25604af290` 其实就是第一条提交的 hash. 这表明, 树本身会包含一些指向其他内容的指针. 也就是说, Git 对象引用其它对象时, 并不会真正储存对象, 只保存了他们的哈希值作为引用.

* 一个大型项目(例如一个kernel), 各个版本都要进行备份, 这难道不会导致内存被占满吗?
* 提交历史就是这么智能. 它会比较各个 commit 之间的不同, 然后仅储存这些**不同的对象**, 其余的引用历史版本即可.

这个示例也显示一点: Git 的提交(`git commit`)其实就是:

* 将被改写的文件保存为数据对象(`object-hash`)
* 更新_暂存区_(`update index`) (你可以暂且不管)
* 记录树对象(`write-tree`)
* 最后创建一个指明了顶层树对象和父提交的提交对象(`commit-tree`)

commit 是不可变的(immutable).

一个 commit ID 不仅和文件内容相关, 还和整个提交历史和仓库相关.

> Commits have "memory", but no premonition.

packfiles

* 历史记录: 快照组成的有向无环图
* 引用: 指向提交的指针
  * 当前位置的索引: HEAD
* 标签: 对象的一个别名
  * 通常来说是某次 commit 后设定的一个版本号
* 仓库: 对象和引用
* 暂存区

[MIT CS教育缺失的一课: 版本控制(Git)](https://missing-semester-cn.github.io/2020/version-control/)

[Git crash course](https://www.youtube.com/watch?v=RGOj5yH7evk\&list=PLICH95IlQ1dRtYhrkmiqubOB6TGoZKrS5\&index=38)

用 ChatGPT 辅助你完成代码!

### 作业
