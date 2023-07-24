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
