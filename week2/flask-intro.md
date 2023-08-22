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

你可能会好奇创建应用实例是干什么的. 你可以做下面的一个小实验:

```python
>>> from flask import Flask, current_app, request
>>> app = Flask('hello')
>>> app
>>> current_app
>>> with app.app_context():
    print(repr(current_app))
>>> with app.test_request_context():
    print(repr(request))
```

看看 Flask 的核心代码:

```python
def wsgi_app(self, environ: dict, start_response: t.Callable) -> t.Any:
        ctx = self.request_context(environ)
        error: t.Optional[BaseException] = None
        try:
            try:
                ctx.push()
                response = self.full_dispatch_request()
            except Exception as e:
                error = e
                response = self.handle_exception(e)
            except:  # noqa: B001
                error = sys.exc_info()[1]
                raise
            return response(environ, start_response)
...
```

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

`index` 这个名字你应该不陌生. 除了下标的含义, 它还代表类似于主页的含义. 它处理入站**请求**(request), 然后执行 `index` 函数. 它的返回值称为**响应**(response), 可以是包含 HTML 元素的简单字符串.

URL是可变的, `app.route` 它可以 parse 一些特定的句法(也叫做 [converter](https://flask.palletsprojects.com/en/2.3.x/api/#url-route-registrations)), 例如

```python
@app.route('/user/<name>')
def user(name):
    return f'my name is {name}'
```

Flask 会把这个动态部分作为函数参数传入函数. 现在, 访问 `/user/besthope`, 就会输出我们的预期结果.

这个动态部分支持强调类型(默认是 `string`). 例如, `<int:id>` 就声明了它只匹配动态片段 `id` 为整数的情况(注意这和 type hint 的语法不太一样).

我们还可以获取视图函数对应的 URL, 用 `url_for` 即可:

```python
>>> from flask import url_for
>>> url_for('index')
```

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

我们推荐你使用 `config` 文件或者利用 `dotenv` 去配置这些选项. 不过到目前, 你只需要了解这些部分即可.
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

## 示例: Glimpse to RESTful APIs

### REST 架构

在本次的示例中, 我们会演示怎么去编写符合 [REST](http://ruanyifeng.com/blog/2014/05/restful\_api.html)(**RE**presentational **S**tate **T**ransfer) 原则的接口.

REST _对信息的核心抽象_就是**资源**(Resources), 通常以 [URI](https://en.wikipedia.org/wiki/Uniform\_Resource\_Identifier)(统一资源定位符) 的形式表现. 这里的资源可以是文本, 图片, 或者_任何能被命名的信息_. 换句话说, 如果某一信息能被超链接导航到, 那它就符合资源的定义. 要访问某一"资源", 访问它的 URI 即可. URI 就是这个"资源"独一无二的**标识符**!

{% hint style="info" %}
"资源"是对信息的_抽象_: 它代表的是**一组**相关的实体, 而非一个单独的实体. 具体的实体会发生变化, 但"资源"表示实体的结构和行为是不变的.
{% endhint %}

例如 Github 对用户信息封装了一个接口

```
https://api.github.com/users/besthope-official
```

"资源"具体表现的形式, 就称作它的"**表现层**"(Representation).

例如, 文本可以用 txt 格式表现, 也可以用 JSON, xml 格式, 甚至二进制格式表现; 图片可以用 jpg 格式表现, 也可以用 png 格式表现.

上面的 URI 访问的结果就是一个 JSON 表示:

```
{
  "login": "Besthope-Official",
  "id": 29171517,
  "node_id": "MDQ6VXNlcjI5MTcxNTE3",
  ...
}
```

想要获取同一资源另外的表现层, 例如上面的XML表示, 你需要为这个请求附带额外信息, 这个额外信息就叫做标头 (**header**). 在这个例子中, 你需要设置下 [Content-Type](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Content-Type) 这个header.

访问一个网站, 就代表了**客户端和服务器的一个互动过程**. 在这个过程中, 必然涉及数据和状态的改变.

互联网通信协议[HTTP 协议](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Overview#http\_%E6%98%AF%E6%97%A0%E7%8A%B6%E6%80%81%EF%BC%8C%E6%9C%89%E4%BC%9A%E8%AF%9D%E7%9A%84), 是一个_无状态协议_. 所有的状态都保存在服务器端, 如果客户端想要操作服务器, **必须通过某种途径**, 使得服务器端发生"**状态转移**"(State Transfer). 而这种状态转移是建立在表现层之上的. 所以, 你现在就能明白 REST 到底指的是个啥.

<figure><img src="../.gitbook/assets/web-server.svg" alt=""><figcaption><p>请求与响应</p></figcaption></figure>

至于客户端用到的手段, 只能是[HTTP 的请求方法](https://developer.mozilla.org/zh-CN/docs/web/http/methods)(request methods). 下面这五种方法对应的 **CRUD(增删改查)** 操作:

| 请求方法   | 效果                 |
| ------ | ------------------ |
| GET    | 读取（Read）           |
| POST   | 新建（Create）         |
| PUT    | 更新（Update）         |
| PATCH  | 更新（Update），通常是部分更新 |
| DELETE | 删除（Delete）         |

根据 HTTP 协议, 请求方法由客户端提出, 由服务器决定是否执行或拒绝. 例如, 你发起了一个 `GET` 请求, 想获取 `/clients` 下的资源. 但这个文件获取需要有权限, 服务器就可以有如下的执行方式:

* `404 Not found`: 这个状态码表示文件不存在. 当然是服务器装的.
* `403 Forbidden`: 这个文件拒绝访问.
* `405 Method Not Allowed`: 然后用 `Allow` 头告诉你可以用 `HEAD` 方法获取文件的元信息.

这样, 你就不难理解: **资源都是名词**. 例如, 下面的这个 URI 就是错误的

```
http://api.example.com/posts/delete/233/
```

要实现删除操作, 你需要用到上面介绍的请求方法, 用_动词+名词_的方式去实现这个操作, 例如

```
DELETE https://api.github.com/gists/gists/1
```

返回的结果也不再是资源, 而是我们上面提到的返回码和原因短语. 通常来说, 我们会在后端文档中指明各个返回码的含义, 例如上面这个[例子](https://docs.github.com/en/rest/gists/gists?apiVersion=2022-11-28#delete-a-gist--status-codes) 204 代表 No Content:

```
Status: 204
```

讲了这么多有的没的, 那采用 RESTful 架构到底有什么用. 为什么, 那就是大家都用这一套. 正所谓某个商品销量高, 评价又好, 必然有它的原因. 它好理解, 跨平台, 支持缓存, 可拓展......

不过说到底, 它也_只是_**一种架构. 一种准则.** 你完全可以不按照这套方式去设计接口(例如去学习RPC等其它架构方式), 完全的放飞自我, 只不过这样, 你的 URL 设计会变得一团糟:

* URI 又臭又长: ~~`/authors/12/categories/2`~~
  * 用字符串查询去优化: `/authors/12?categories=2`
* URI 的命名: ~~`order/create-order`~~
  * 不要用单数名词去命名资源集合, 也不要出现动宾结构的出现.
* .....

### Coding Time

Flask 是编写 RESTful APIs 的一个很好的选择, 不仅因为是_它好写_(Python 的优势), 同时, Flask 本身对 REST 的请求和调度支持很好. 从我个人的角度来说, Flask 小而微, 由社区 extensions 驱动, 和 vscode 类似, 非常符合我的审美需求:>

我们用一个简单的增删改查的例子来体现这一点: 下载我们的[示例代码](https://github.com/Besthope-Official/Singularity-backend/raw/main/demo/demo\_crud.zip), 它包含了一个简单的框架和存储的数据.

{% hint style="info" %}
在后期的教学以及作业布置, 我们会经常采用类似的方式.

如果你是 WSL, 你可以通过 Windows Explorer 直接将文件复制到你想要的目录下(试试 `explorer.exe .`)

不过, 为了熟悉 Unix 工作流, 我们推荐你使用 `wget` 将文件下载到当前目录下

`wget https://github.com/Besthope-Official/Singularity-backend/raw/main/demo/demo_crud.zip`

然后利用 `unzip` 指令进行解压:

```
unzip demo_curd.zip
```
{% endhint %}

我们的数据保存在 `db.py`, 它是一个由字典构成的列表, 每一个字典有 4 个字段, id, name, group, category, 作为键存在.

我们用 `get_author()` 作为示例:&#x20;

* 它返回"数据库"里指定 group 和指定 category 的作者信息.
* 请求 URL 是: `http://127.0.0.1:5000/authors/<group_id>`, 请求方式为 GET.
* 它需要一个请求 Query 参数 categories, 例如 `/authors/1?categories=1`

获取这个请求参数, 我们可以利用 [`request`](https://flask.palletsprojects.com/en/2.3.x/api/#flask.request), 它是一个全局的 Request object, 你只用导入它就可以获取请求(根据上下文). 同时, 它还是线程安全的!

剩下一个问题是, 返回的请求是一段 JSON, 你可能会想, 直接返回一个 `json.dumps` 不就可以了(就和我们上面返回HTML标签一样). 其实你这是隐式创建了一个 Response.

但假如我们想额外返回例如错误码之类的信息, 你可能还要在 JSON 里添加一个错误码的字段.

我们不想让 JSON 的格式变得太复杂, 如果它能作为一个类似 header 的返回, 那会更方便. 很显然我们是没有 `flask.response` 的, 但显式地创建一个 response 也不是什么难事:

```python
resp = Response(js, status=200, mimetype='application/json')
return resp
```

其实我们还有更方便的 `jsonify` 多做了一层封装:

```python
@app.route('/authors/<int:group_id>', methods=['GET'])
def get_author(group_id):
    '''
    Return the authors that are in the same group and category.
    '''
    category = request.args.get('categories', None)
    
    result_authors = [author for author in authors if author['group']
                      == group_id and author['category'] == int(category)]
    
    if not result_authors:
        js = jsonify({'message' : 'No authors to be found'})
        js.status_code = 404
    else:
        js = jsonify(result_authors)
        js.status_code = 200
        
    return js
```

用 curl 去获取请求(`-i`表示显示headers):

```bash
curl -i http://127.0.0.1:5000/authors/1?categories=1
```

## 学习资料

* _Flask Web开发: 基于Python的Web应用开发实战._ 很好的 Flask 入门图书.
* [Hello Flask](https://tutorial.helloflask.com/hello/): 一个 Flask 入门的教程. 因为是中文所以比较好上手.
* [Awesome Flask](https://github.com/humiaozuzu/awesome-flask#resources): 一个收录了许多优秀 Flask 插件和学习资源的开源项目.
* [Flask-restful](https://github.com/flask-restful/flask-restful): 快速上手搭建 RESTful 风格的接口插件.
* [Implementing a RESTful Web API with Python & Flask](http://blog.luisrei.com/articles/flaskrest.html): 很不错的一篇文章.
* [In Flask we trust](https://speakerdeck.com/playpauseandstop/in-flask-we-trust): 如果你想要更仔细了解 Flask 的底层机制, 推荐你阅读这个 slides.
