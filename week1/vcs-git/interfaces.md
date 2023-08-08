# Git 的接口

为了避免重复造轮, 我们不会一五一十的解释下面的这些命令行指令. 我们推荐你阅读 [Pro Git](https://git-scm.com/book/zh/v2/Git-%E5%9F%BA%E7%A1%80-%E8%8E%B7%E5%8F%96-Git-%E4%BB%93%E5%BA%93) 文档来学习这些 Git 的基础操作. 另外文档工具例如 tldr, 或者 `git help` 会很有用.

此外, 我们推荐你去了解 IDE 里集成的 Git 使用, 而不是照着命令行敲(通常来说这是低效的). 当然, 作为学习工作原理, 我们强烈推荐你去了解CLI.

## 基础操作

{% hint style="info" %}
如果你还在思考 Commit 是个什么东西, 你可以回到上一个章节再去回顾下知识点.
{% endhint %}

获取一个 Git 仓库:

* `git init`: 将尚未进行版本控制的本地目录转换为 Git 仓库
* `git clone`: 从其它服务器**克隆**一个已存在的 Git 仓库

一些基础操作:

* `git status`: 查看当前状态
* `git add`: 将文件添加到暂存区
  * 从工作区(worktree)添加到暂存区(index/staged)
* `git commit -m "<message>"`: 创建一次提交
  * 暂存区添加到版本库. 这时候会更新 HEAD.
  * 我好犹豫怎么写提交信息啊! 看看这个[文章](https://cbea.ms/git-commit/). 写好提交信息很重要.

{% hint style="info" %}
这是一个经典笑话: 如何生成一个随机字符串

让新手退出 VIM

```bash
git add .
git commit
^C^C^D^D^X^X^X^Z^Z^Zexit.exitexit()^C^C^C^C^C^C^C^C^C^C^C^C^C^C^C^C^C^C^C^C^C^C
```
{% endhint %}

* `git log:` 显示历史信息
* `git log --all --graph --decorate`: 可视化
* `git diff <filename>`: 显示文件与暂存区的差异
* `git diff <revision> <filename>`: 显示某个文件两个版本之间的差异
* `git checkout <revision>`: 更新HEAD和目前的分支

{% hint style="info" %}
`git checkout` 自 Git 2.23 起不建议使用(deprecated). 改用 `git switch` 来切换分支, 以及分离 HEAD.
{% endhint %}

## 分支操作

分支操作是 Git 的灵魂之一. 我们在原理篇介绍了分支其实就是一个指向 commit 的指针. 按照这个思路你会更好理解这个概念.

我们强烈推荐你学习 [Learn Git Branching](https://learngitbranching.js.org/) 这样一个交互式网站去学习分支操作!

* `git branch`: 显示分支
* `git branch <name>`: 创建分支
* `git checkout -b <name>`: 创建分支并切换到该分支
* `git merge <revision>`: 合并到当前分支
* `git mergetool`: 使用工具来处理合并冲突
* `git rebase`: 将一系列补丁变基(rebase)为新的基线
  * [merge or rebase?](https://zhuanlan.zhihu.com/p/57872388) 按需取用

## 远端操作

* `git remote`: 列出远端
* `git remote add <name> <url>`: 添加一个远端
* `git push <remote> <local branch>:<remote branch>`: 将对象传送至远端并更新远端引用
* `git branch --set-upstream-to=<remote>/<remote branch>`: 创建本地和远端分支的关联关系
* `git fetch`: 从远端获取对象/索引
* `git pull`: 相当于 `git fetch; git merge`
* `git clone`: 从远端下载仓库

## 撤销 <a href="#che-xiao" id="che-xiao"></a>

* `git commit --amend`: 编辑提交的内容或信息
* `git reset HEAD <file>`: 恢复暂存的文件
* `git checkout -- <file>`: 丢弃修改
* `git restore`: git 2.32 版本后取代 `git reset` 进行许多撤销操作

## 进阶操作

* `git config`: Git 是一个[高度可定制的](https://git-scm.com/docs/git-config)工具
  * `git pre-commit hook`: Git 能在特定的重要动作发生时触发自定义脚本(shell)
* `git clone --depth=1`: 浅克隆 (shallow clone), 不包括完整的版本历史信息
* `git add -p`: 交互式暂存
* `git rebase -i`: 交互式变基
* `git blame`: 查看最后修改某行的人
* `git stash`: 暂时移除工作目录下的修改内容
* `git bisect`: 通过二分查找搜索历史记录
* `.gitignore`: [指定](https://git-scm.com/docs/gitignore)故意不追踪的文件
* [提交签名](https://docs.github.com/zh/authentication/managing-commit-signature-verification/about-commit-signature-verification): 给你的 Git 提交签名, 这样你的提交就会有一个 Verified 字样. 曾经有人用 linus 的名义发起 [commit](https://github.com/torvalds/linux/tree/8bcab0346d4fcf21b97046eb44db8cf37ddd6da0) 整蛊.

## 合格的 Git 仓库

原则: **要让傻瓜都会按照你的指示正常跑起你的代码.**

包含以下内容:

* 合适的项目名称
* 项目简介
* README.md
* .gitignore
* 项目源码
* 项目依赖库的安装方式
* 项目的使用方法

## 演示: IDE 里的使用

这一部分其实最为重要: 就算你不懂原理, 但你也可以依葫芦画瓢地去使用 Git.

我们给出相关 IDE 的文档: 如果有你没见到的, 记得 STFW.

* [Using Git source control in VS Code](https://code.visualstudio.com/docs/sourcecontrol/overview)
* [Pycharm Documentation](https://www.jetbrains.com/help/pycharm/set-up-a-git-repository.html)
* [Git | IntelliJ IDEA Documentation](https://www.jetbrains.com/help/idea/using-git-integration.html)
