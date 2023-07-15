---
description: 我们不是SVN
---

# Git

{% hint style="info" %}
本章节推荐所有同学完成，这一章节是基础的安装工作，没有原理讲解。
{% endhint %}

相信有不少同学已经久闻Git的大名。是时候动手实践了！

## Git 安装

我们将在两个系统（本机系统，linux）上都安装Git。因此这一章分为三个独立的小节：mac上的git安装、Windows上的git安装、linux上的git安装。

### mac 安装

在 Mac 上安装 Git 有多种方式。 最简单的方法是安装 Xcode Command Line Tools。 Mavericks （10.9） 或更高版本的系统中，在 Terminal 里尝试首次运行 'git' 命令即可。

```console
$ git --version
```

如果没有安装过命令行开发者工具，将会提示你安装。

如果你想安装更新的版本，可以使用二进制安装程序。 官方维护的 macOS Git 安装程序可以在 Git 官方网站下载，网址为 [https://git-scm.com/download/mac](https://git-scm.com/download/mac)。

你也可以将它作为 GitHub for macOS 的一部分来安装。 它们的图形化 Git 工具有一个安装命令行工具的选项。 你可以从 GitHub for macOS 网站下载该工具，网址为 [https://mac.github.com](https://mac.github.com/)。

### Windows 安装

在 Windows 上安装 Git 也有几种安装方法。 官方版本可以在 Git 官方网站下载。 打开 [https://git-scm.com/download/win](https://git-scm.com/download/win)，下载会自动开始。 要注意这是一个名为 Git for Windows 的项目（也叫做 msysGit），和 Git 是分别独立的项目；更多信息请访问 [http://msysgit.github.io/](http://msysgit.github.io/)。

要进行自动安装，你可以使用 [Git Chocolatey 包](https://chocolatey.org/packages/git)。 注意 Chocolatey 包是由社区维护的。

另一个简单的方法是安装 GitHub Desktop。 该安装程序包含图形化和命令行版本的 Git。 它也能支持 Powershell，提供了稳定的凭证缓存和健全的换行设置。 稍后我们会对这方面有更多了解，现在只要一句话就够了，这些都是你所需要的。 你可以在 GitHub for Windows 网站下载，网址为 [GitHub Desktop 网站](https://desktop.github.com/)。

### linux 安装

如果你想在 Linux 上用二进制安装程序来安装基本的 Git 工具，可以使用发行版包含的基础软件包管理工具来安装。 以 Fedora 为例，如果你在使用它（或与之紧密相关的基于 RPM 的发行版，如 RHEL 或 CentOS），你可以使用 `dnf`：

```console
$ sudo dnf install git-all
```

如果你在基于 Debian 的发行版上，如 Ubuntu，请使用 `apt`：

```console
$ sudo apt install git-all
```

要了解更多选择，Git 官方网站上有在各种 Unix 发行版的系统上安装步骤，网址为 [https://git-scm.com/download/linux](https://git-scm.com/download/linux)。

{% hint style="warning" %}
你可能会发现网速异常的慢，甚至有的时候会莫名其妙的 `network erro` 。是时候配置自己的镜像站了！具体的细节请STFW ^-^ 。
{% endhint %}

### 检验是否安装完成

在你的bash 中输入&#x20;

<pre class="language-bash"><code class="lang-bash"><strong>$ <a data-footnote-ref href="#user-content-fn-1">git -v</a>
</strong></code></pre>

当出现

```bash
> git version xx.xx.xx
```

之类的输出时代表git安装正常。

{% hint style="danger" %}
有可能你明明安装正确了却依然报错。这是为什么呢？很大概率是环境变量配置错误导致的。具体细节请STFW。
{% endhint %}

## Git 配置

在安装好git之后是否就神功大成了呢？答案是否定的，我们还需要配置好自己的邮箱与用户名。Git 使用用户名将提交与身份关联。&#x20;

可使用 `git config` 命令更改与 Git 提交关联的名称。 您设置的新名称将在从命令行推送到 GitHub 的任何未来提交中显示。 如果您想要将真实姓名保密，则可以使用任意文本作为您的 Git 用户名。

使用 `git config` 更改与 Git 提交关联的名称仅影响未来的提交，而不会更改用于过去提交的名称。

### 为计算机上的每个存储库设置 Git 用户名 <a href="#setting-your-git-username-for-every-repository-on-your-computer" id="setting-your-git-username-for-every-repository-on-your-computer"></a>

1. 打开一个 Bash。
2.  设置 Git 用户名：

    ```shell
    git config --global user.name "Mona Lisa"
    ```
3.  确认您正确设置了 Git 用户名：

    ```shell
    $ git config --global user.name
    > Mona Lisa
    ```

### 为一个仓库设置 Git 用户名 <a href="#setting-your-git-username-for-a-single-repository" id="setting-your-git-username-for-a-single-repository"></a>

1. 打开一个 Bash。
2. 将当前工作目录更改为您想要在其中配置与 Git 提交关联的名称的本地仓库。
3.  设置 Git 用户名：

    ```shell
    git config user.name "Mona Lisa"
    ```
4.  确认您正确设置了 Git 用户名：

    ```shell
    $ git config user.name
    > Mona Lisa
    ```

[^1]: 
