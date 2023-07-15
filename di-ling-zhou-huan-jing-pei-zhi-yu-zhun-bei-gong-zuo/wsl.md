---
description: Windows Subsystem for Linux
---

# WSL

{% hint style="info" %}
WSL的安装与配置推荐后端人员如果使用Windows一定要完成。其他同学可视自己的情况完成。
{% endhint %}

## 什么是WSL

wikipada 对 WSL 做了如下定义：

_<mark style="background-color:orange;">“适用于Linux的Windows子系统（英语：Windows Subsystem for Linux，简称WSL）是一个为在</mark>_[_<mark style="background-color:orange;">Windows 10</mark>_](https://zh.wikipedia.org/wiki/Windows\_10)_<mark style="background-color:orange;">和</mark>_[_<mark style="background-color:orange;">Windows Server 2019</mark>_](https://zh.wikipedia.org/wiki/Windows\_Server\_2019)_<mark style="background-color:orange;">以上能够原生运行</mark>_[_<mark style="background-color:orange;">Linux二进制可执行文件</mark>_](https://zh.wikipedia.org/wiki/Linux)_<mark style="background-color:orange;">（</mark>_[_<mark style="background-color:orange;">ELF</mark>_](https://zh.wikipedia.org/wiki/%E5%8F%AF%E5%9F%B7%E8%A1%8C%E8%88%87%E5%8F%AF%E9%8F%88%E6%8E%A5%E6%A0%BC%E5%BC%8F)_<mark style="background-color:orange;">格式）的</mark>_[_<mark style="background-color:orange;">兼容层</mark>_](https://zh.wikipedia.org/wiki/%E5%85%BC%E5%AE%B9%E5%B1%82)_<mark style="background-color:orange;">。”</mark>_

通俗的说 WSL 是一款由微软公司开发的 Linux 虚拟机。他与其他虚拟机的不同之处在于它是由微软公司开发并维护的。正因如此，它拥有相比其他虚拟机更加契合Windows的特点。也因此获得了更强的性能与全套的微软开发工具支持。

## WSL1 与 WSL2

WSL 分为 WSL1 与WSL2。WSL1 与 WSL2之间的关系类似 python2 与 python3 。虽然在名字上差别不大，但是底层实现上有着巨大不同。WSL1选择给Windows系统开天窗，增量式的实现Linux的系统调用。但这带来了巨大的工作量（由于Linux与Windows本质上的不同）与更多的稳定性和安全性问题。因此 WSL1 目前处于半放弃状态，但仍可以使用。 而WSL2则使用Windows提供的系统调用实现Linux的系统调用。这极大地减轻了工作量并且没有牺牲太多性能。

### 比较功能 <a href="#comparing-features" id="comparing-features"></a>

| 功能                                | WSL 1 | WSL 2 |
| --------------------------------- | ----- | ----- |
| Windows 和 Linux 之间的集成             | ✅     | ✅     |
| 启动时间短                             | ✅     | ✅     |
| 与传统虚拟机相比，占用的资源量少                  | ✅     | ✅     |
| 可以与当前版本的 VMware 和 VirtualBox 一起运行 | ✅     | ✅     |
| 托管 VM                             | ❌     | ✅     |
| 完整的 Linux 内核                      | ❌     | ✅     |
| 完全的系统调用兼容性                        | ❌     | ✅     |
| 跨 OS 文件系统的性能                      | ✅     | ❌     |

### 例外情况（使用 WSL 1 而不是 WSL 2） <a href="#exceptions-for-using-wsl-1-rather-than-wsl-2" id="exceptions-for-using-wsl-1-rather-than-wsl-2"></a>

{% hint style="warning" %}
你可能看不懂这一小节的内容，但你可以先跳过。之后的项目不会使用WSL1。
{% endhint %}

我们建议使用 WSL 2，因为它提供更快的性能和100% 的系统调用兼容性。 但是，在某些特定情况下，你可能会更倾向于使用 WSL 1。 在以下情况下，请考虑使用 WSL 1：

* 你的项目文件必须存储在 Windows 文件系统中。 WSL 1 可以更快地访问从 Windows 装载的文件。
  * 如果你将使用 WSL Linux 分发版来访问 Windows 文件系统上的项目文件，并且这些文件无法存储在 Linux 文件系统上，那么，通过使用 WSL 1，你将跨 OS 文件系统实现更快的性能。
* 一个项目要求对相同的文件使用 Windows 和 Linux 工具进行交叉编译。
  * 在 WSL 1 中，跨 Windows 和 Linux 操作系统的文件性能比 WSL 2 中更快，因此如果要使用 Windows 应用程序来访问 Linux 文件，则目前通过 WSL 1 可实现更快的性能。
* 你的项目需要访问串行端口或 USB 设备。 但是，现在可通过 USBIPD-WIN 项目为 WSL 2 提供 USB 设备支持。 有关设置步骤，请参阅[连接 USB 设备](https://learn.microsoft.com/zh-cn/windows/wsl/connect-usb)。
* WSL 2 不支持访问串行端口。 有关详细信息，请参阅[常见问题解答](https://learn.microsoft.com/zh-cn/windows/wsl/faq#can-i-access-the-gpu-in-wsl-2--are-there-plans-to-increase-hardware-support-)或 [WSL GitHub 存储库中有关串行支持的问题](https://github.com/microsoft/WSL/issues/4322)。
* 有严格的内存要求
  * WSL 2 的内存使用量会随使用而缩放。 当进程释放内存时，这会自动返回到 Windows。 但从现在开始，在关闭 WSL 实例前，WSL 2 还不会将内存中缓存的页面释放回 Windows。 如果你有长时间运行的 WSL 会话或访问非常大量的文件，此缓存可能会耗尽 Windows 内存。 我们通过 [WSL GitHub 存储库问题 4166](https://github.com/microsoft/WSL/issues/4166) 跟踪工作以改善此体验。
* 对于使用 VirtualBox 的用户，你可能需要考虑你正在运行的版本以及它是否与 WSL 2 兼容。 （有关完整讨论，请参阅 [WSL GitHub 存储库问题 798](https://github.com/MicrosoftDocs/WSL/issues/798)。VirtualBox v6.1.16 似乎适用于 WSL 2，但其他版本可能遇到问题。）
* 如果依赖 Linux 发行版在与主机相同的网络中拥有 IP 地址，则可能需要设置一种替代方法来运行 WSL 2。 WSL 2 作为 hyper-v 虚拟机运行。 这是对 WSL 1 中使用的桥接网络适配器的更改，这意味着 WSL 2 使用网络地址转换 (NAT) 服务作为其虚拟网络，而不是将其桥接到主机网络接口卡 (NIC)，从而生成唯一的将在重启时更改的 IP 地址。 要详细了解将 WSL 2 服务的 TCP 端口转发到主机 OS 的问题和缓解措施，请参阅 [WSL GitHub 存储库问题 4150，NIC 桥接模式（TCP 缓解措施）](https://github.com/microsoft/WSL/issues/4150)。

## WSL 的安装

### 先决条件 <a href="#prerequisites" id="prerequisites"></a>

必须运行 Windows 10 版本 2004 及更高版本（内部版本 19041 及更高版本）或 Windows 11 才能使用以下命令。 如果使用的是更早的版本，请参阅[手动安装页](https://learn.microsoft.com/zh-cn/windows/wsl/install-manual)。

### 安装 WSL 命令 <a href="#install-wsl-command" id="install-wsl-command"></a>

现在，可以使用单个命令安装运行 WSL 所需的一切内容。 在管理员模式下打开 PowerShell 或 Windows 命令提示符，方法是右键单击并选择“以管理员身份运行”，输入 wsl --install 命令，然后重启计算机。

```powershell
wsl --install
```

此命令将启用运行 WSL 并安装 Linux 的 Ubuntu 发行版所需的功能。 （[可以更改此默认发行版](https://learn.microsoft.com/zh-cn/windows/wsl/basic-commands#install)）。

如果你运行的是旧版，或只是不想使用 install 命令并希望获得分步指引，请参阅[旧版 WSL 手动安装步骤](https://learn.microsoft.com/zh-cn/windows/wsl/install-manual)。

{% hint style="warning" %}
首次启动新安装的 Linux 发行版时，将打开一个控制台窗口，要求你等待将文件解压缩并存储到计算机上。这一过程可能会消耗一段时间，但未来的所有启动时间应不到一秒，所以请内心等待。
{% endhint %}

{% hint style="warning" %}
上述命令仅在完全未安装 WSL 时才有效，如果运行 `wsl --install` 并查看 WSL 帮助文本，请尝试运行 `wsl --list --online` 以查看可用发行版列表并运行 `wsl --install -d <DistroName>` 以安装发行版。 若要卸载 WSL，请参阅[卸载旧版 WSL](https://learn.microsoft.com/zh-cn/windows/wsl/troubleshooting#uninstall-legacy-version-of-wsl) 或[注销或卸载 Linux 发行版](https://learn.microsoft.com/zh-cn/windows/wsl/basic-commands#unregister-or-uninstall-a-linux-distribution)。
{% endhint %}

## 安装 Linux 发行版

{% hint style="info" %}
本次项目默认安装ubuntu，如想要安装其他发行版请自行寻找教程。
{% endhint %}

按下键盘上的Windows键并输入 Microsoft store 并按下回车。打开微软商店后输入 ubuntu搜索。接下来你会发现各个不同的ubuntu版本，它们有两种：LTS版本与非LTS版本。

### LTS 与非 LTS&#x20;

#### 什么是 Ubuntu LTS？ <a href="#what-is-ubuntu-lts" id="what-is-ubuntu-lts"></a>

Ubuntu 每两年发布一次其操作系统的长期支持 (LTS) 版本。你可以选择升级到下一个可用的 LTS 版本。具体来说，LTS 版本在每个偶数年的 4 月发布。

LTS 版本更安全、更稳定，因此更可靠。此外，所有 LTS 版本都可以从 Canonical 团队获得长达五年的软件更新和标准支持。

经常获得新的软件版本和更新是个好消息，对您的系统有好处，但它也让 Ubuntu 用户在使用哪个 Ubuntu 版本或版本方面进退两难。

对于服务器、软件开发 PC 和其他关键系统，强烈建议您使用 LTS 版本。

#### 选择哪个版本？

本次项目选择最新的LTS版本：Ubuntu 22.04.6 LTS。点击安装按钮安装此版本即可。

### 第一次的Linux

在安装完成以后会出现一个登录引导。根据引导输入你的新用户名与新密码即可。

{% hint style="danger" %}
注意一定要记住并保管好你的用户名与密码！
{% endhint %}

至此WSL的安装就完成了。不要害怕，新的世界已经打开大门。
