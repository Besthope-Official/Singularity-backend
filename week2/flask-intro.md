# Flask 入门

## 安装 Flask

### 创建虚拟环境

创建好你的应用目录, 然后我们就可以安装 Flask 了.

安装 Flask 的最简便的方法是使用虚拟环境, 虚拟环境可以理解为 Python 解释器的一个 copy, 这个环境里安装其他包不会对_全局 Python 解释器_产生影响.

{% hint style="info" %}
你可能会好奇我们为什么要大费周章创建一个虚拟环境. 主要原因是: **环境的兼容问题**.
{% endhint %}

在 Unix 系统下, 我们需要先装下 `virtualenv` 或者 `python3-venv` 包:

```bash
$ sudo apt-get install python3-venv
```

创建虚拟环境:

```
$ python3 -m venv <name>
```

通常 `<name>` 我们取 `.venv` 或者 `venv`. 当这个命令执行完毕后, 我们就会有一个 `.venv` 的子目录, 里面包含一个全新的 Python 解释器.

然后激活这个虚拟环境

```bash
$ source venv/bin/activate
```

然后你应该能在命令提示符前看到多出来的环境名了.

### 一键安装

安装 Flask 非常简单, 我们利用 `pip` 即可:

```bash
(.venv) $ pip install flask
```

检验一下:

```
(.venv) $ python3
>>> import flask
>>>
```

## Flask Quick Start

在根目录下新建一个 `app.py`, 然后就可以开始我们的编码之旅了!

### 应用实例

任何 Flask 应用都需要创建一个应用实例.

Web 服务器使用一种名为 Web 服务器网关接口(WSGI, Web server gateway interface, 读作 wiz-ghee) 的协议, 把接受自客户端的所有请求都转交给这个对象处理. 应用实例是 Flask 类的对象, 一般我们用下面的方式构建

```python
from flask import Flask
app = Flask(__name__)
```

这个类的构造函数需要传入一个_应用主模块的名称_.

我们之后会讲到通过蓝图构建应用实例. 不过对于小型项目来说, 这样做就够了.

### 路由和视图函数

* 路由(route): 处理 URL 和函数之间关系的程序
  * 简单来说, 就是让应用实例知道访问某个 URL 要运行哪些代码
* 我们可以用类装饰器 `app.route()` 来声明路由.
  * 下面的例子: 访问根目录执行 `index` 函数.
  * ~~当然也可以用 `app.add_url_rule()`, 但是麻烦很多.~~

<pre class="language-python"><code class="lang-python">@app.route('/')
def index():
<strong>    return '&#x3C;h1>Hello World!&#x3C;/h1>'
</strong></code></pre>

`index` 这个名字你应该不陌生. 除了下标的含义, 它还代表类似于主页的含义. 它处理_入站请求_, 然后执行 `index` 函数. 它的返回值称为**响应**(response), 可以是包含 HTML 元素的简单字符串.

URL是可变的, `app.route` 它可以 parse 一些特定的句法, 例如

```python
@app.route('/user/<name>')
def user(name):
    return f'my name is {name}'
```

Flask 会把这个动态部分作为函数参数传入函数. 现在, 访问 `/user/besthope`, 就会输出我们的预期结果.

这个动态部分支持强调类型. 例如, `<int:id>` 就声明了它只匹配动态片段 `id` 为整数的情况(注意这和type hint 的语法不太一样).

### 命令行选项

运行我们的 Flask 程序, 你会发现我们用的是一个命令行指令 `flask run`. 这个其实就是 Flask 做的一个命令行指令, 其实等价于你在脚本里写一个

```python
if __name__ == '__main__':
    app.run()
```

我们不需要像其它语言一样写一个 `main` 函数! 用命令行去 run 一下.

这个命令会在 FLASK\_APP 环境变量指定的 Python 文件里寻找应用实例. 默认这个文件是 `app.py`. 如果你没有按照我们上面的指示, 就会遇到相关的错误. 如果你想修改这个默认值, 可以使用下面的语句

```bash
(.venv) $ export FLASK_APP=hello.py
```

其它的一些命令行选项, 例如调试模式, 你可以

```python
(.venv) $ export FLASK_DEBUG=1
```

{% hint style="danger" %}
**千万不要在生产服务器中启用调试模式!**

客户端通过调试器能请求执行远程代码，因此可能导致生产服务器遭到攻击。作为一种简单的保护措施，启动调试模式时可以要求输入PIN 码，执行 `flask run` 命令时会打印在控制台中。

我们推荐你使用 `config` 文件去配置这些选项. 不过到目前, 你只需要了解这些部分即可.
{% endhint %}

你可以试试 `flask help`, 里面有更多关于 flask 命令的相关选项.

例如 `--host` 选项, 它可以让 Web 服务器监听公共网络接口上的连接, 同一网络中的其它计算机发送的连接也可以接受到:

```python
(venv) $ flask run --host 0.0.0.0
* Serving Flask app "hello"
* Running on http://0.0.0.0:5000/ (Press CTRL+C to quit)
```

### 模板

视图函数的作用很明确, 即生成请求的响应, 但很多情况下, 请求会改变应用的状态, 而这种变化仍然发生在视图函数中.

举个例子, 你要实现一个注册账户的功能, 那么首先需要让用户填写用户名和密码, 然后点击提交按钮. 服务器接受到包含用户输入数据的请求, 然后 Flask 把请求分派给处理注册请求的视图函数. 这个视图函数需要访问数据库, 添加新用户, 然后生成响应回送浏览器, 指明操作成功还是失败. 这两个过程分别称为**业务逻辑**和**表现逻辑**.

把业务逻辑和表现逻辑混在一起会导致代码难以理解和维护. 你的 Python 代码可能要充斥大量的 HTML 代码. 这并不是我们希望的事情. 于是就有了**模板:**

* 它用来存放这些表现逻辑.

但实际上, 这些表现逻辑并不需要我们后端实现. 在前后端分离的概念里, 前端就负责这些用户的输入逻辑.

学会用 Jinja2 去渲染模板, 我们就可以去编写一些前后端不分离的 Web 应用. 简单介绍这部分:

* 新建一个 `templates` 文件夹. 在模板文件夹内, 新建一个 `index.html`.
* 内容: `<h1>hello world</h1>`

然后在视图函数里, 我们返回一个 `render_template()`.

```python
@app.route('/')
def index():
    return render_template('index.html')
```

## 学习资料

* Flask Web开发: 基于Python的Web应用开发实战. 很好的 Flask 入门图书.
* [Hello Flask](https://tutorial.helloflask.com/hello/): 一个 Flask 入门的教程.
