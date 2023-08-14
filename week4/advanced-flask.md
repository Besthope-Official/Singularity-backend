# Flask 进阶

本周我们会介绍一种大型 Flask 项目的文件架构. 尽管 Flask 并不强制要求项目的具体组织方式(换句话说任凭你放飞自我, 写在单文件里也是没问题的), 但是采用一种具体的组织方式总能使你的编码更清晰.

```bash
$ tree -P '*.py' --prune -I 'flask-sqlacodegen|venv'
.
├── app.py
├── auth.py
├── config.py
├── generate_model.py
├── models.py
└── views
    ├── api
    │   ├── authentication.py
    │   ├── __init__.py
    │   ├── posts.py
    │   ├── subvues.py
    │   └── users.py
    └── __init__.py
```

## 配置选项

应用经常需要设定多个配置. 这方面最好的例子就是开发、测试和生产环境要使用不同的数据库, 这样才不会彼此影响.

我们可以设计一个名为配置的基类, 里面存放一些通用的配置选项, 例如密钥, 发送邮箱的名称, 等等. 然后在不同的具体配置中, 设置例如数据库地址, 是否使用调试器等.

为了让配置方式更灵活且更安全, 多数配置都可以从环境变量中导入.

* 你当然可以直接写成字面量: `MY_PASSWORD = 'hard to guess haha'`
  * 然后你忘记改就把文件上传到 Git 里了, 然后被迫用到了我们学到的永久删除大法.
* 写成 `MY_PASSWORD = os.environ.get('MY_PASSWORD')`
  * 或者更好, 对部分字段做一个空值检查或者添加一个默认值 `or 'default'`

你可以使用 [dotenv](https://github.com/theskumar/python-dotenv), 它可以将 `.env` 文件里的键值对当成系统变量来用. 具体使用方式可以参考文档.

{% hint style="danger" %}
千万不要把密码或其他机密信息写在纳入版本控制的配置文件中! `.gitignore` 有大用.
{% endhint %}

```python
import os
from dotenv import load_dotenv

basedir = os.path.abspath(os.path.dirname(__name__))
load_dotenv('.env')


class Config(object):
    SECRET_KEY = os.environ.get('SECRET_KEY')
    HOSTNAME = os.environ.get('HOSTNAME')
    PORT = os.environ.get('PORT')
    USERNAME = os.environ.get('USERNAME')
    PASSWORD = os.environ.get('PASSWORD')
    DB_NAME = os.environ.get('DB_NAME')
    SQLALCHEMY_DATABASE_URI = f'mysql+pymysql://{USERNAME}:{PASSWORD}@{HOSTNAME}:{PORT}/{DB_NAME}'
    SQLALCHEMY_COMMIT_ON_TEARDOWN = True
    SQLALCHEMY_TRACK_MODIFICATIONS = False
    
    @staticmethod
    def init_app(app):
        # Example: app.config['SQLALCHEMY_TRACK_MODIFICATIONS'] = ...
        pass

class DevelopmentConfig(Config):
    DEBUG = True


class TestingConfig(Config):
    DEBUG = False
    TESTING = True


class ProductionConfig(Config):
    DEBUG = False


config = {
    'development' : DevelopmentConfig,
    'testing': TestingConfig,
    'production': ProductionConfig,
    'default': DevelopmentConfig
}

```

为了再给应用提供一种定制配置的方式, Config 类及其子类可以定义 init\_app() 类的静态方法, 其参数为应用实例:

```python
Config.init_app(app)
```

config 字典中注册了不同的配置环境. 怎么让应用加载不同的配置呢? 也就是 Config 类实例化以后怎样让 Flask 的应用实例加载这些配置.

* 要读取全部字段然后一个个设置 `app.config`?
  * 太麻烦了. 其实有更简洁的方式.
* 你可以用 `app.config.from_object()` 来导入我们的配置类.

## 工厂函数

啥子是个工厂函数哟?

* 通俗地来说, 它就像工厂一样, 批量生产.
  * 批量生产啥呢? 那就是应用实例.
* 换个人话: 把创建实例的过程用一个函数封装起来.
  * 这样, debug 要测试不同环境的应用, 那就批量生产不同的应用实例就可以.

```python
from flask import Flask, render_template
from flask_sqlalchemy import SQLAlchemy
from config import config

db = SQLAlchemy()

def create_app(config_name=config['default']):
    app = Flask(__name__)
    
    app.config.from_object(config_name)
    config[config_name].init_app(app)

    db.init_app(app)
    # 添加路由和自定义的错误页面
    # ...
    
    return app
```

构造文件导入了大多数正在使用的 Flask 扩展(这里是 db), 你还可以添加例如 `from flask_mail import Mail`, 实例化一个 `mail` 然后 `mail.init_app`.

## 蓝图

上面的示例函数并不完整. 你马上就会出现一个问题:

* 既然我们的应用实例由 `create_app` 创建. 那定义路由的那个 app 要写什么?
  * 总不至于 `app = create_app(...)` 再写吧? 太晚了.
* 错误页面处理程序使用 `app.errorhandler` 装饰器定义, 也是同样的问题...

Flask 使用**蓝图** (blueprint) 提供了更好的解决方法. 蓝图和应用类似, 也可以定义路由和错误处理程序. 不同的是, 在蓝本中定义的路由和错误处理程序处于休眠状态, 直到蓝本注册到应用上之后, 它们才真正成为应用的一部分.

与应用一样, 蓝图可以在单个文件中定义, 也可使用更结构化的方式在包中的多个模块中创建. 为了方便调整, 我们在 views 文件夹下新建一个 `__init__.py`.

{% hint style="info" %}
在 Python 的工程项目中, Python 会把含有 \_\_init\_\_.py 的文件夹作为一个模块(Module).
{% endhint %}

```python
from flask import Blueprint

api = Blueprint('api', __name__, url_prefix='/api')

from . import authentication, posts, subvues, users
```

蓝本通过实例化一个 Blueprint 类对象创建. 这个构造函数有两个必须指定的参数: 蓝本的名称和蓝本所在的包或模块. 与应用一样, 多数情况下第二个参数使用Python 的 `__name__` 变量即可. 此外, 在我们的示例中, `url_prefix` 参数表示这个蓝本路由的 url 会自带一个 `'/api'` 的前缀.

应用的路由保存在 authentication, posts, subvues, users 这几个模块中, 导入这两个模块就能把路由和错误处理程序(另外添加)与蓝本关联起来.

{% hint style="warning" %}
注意, 引入模块这一步**不可以缺失**, 也**不可以随意调换位置**, 否则可能会出现循环引用的情况出现.

_\*\*还有一件事\*\*_(老爹音), 引用模块的句法, `from .` 表示从当前目录下引用, 这是一种相对引用的方式, 这样的写法要比绝对引用好. 另外, `from ..` 表示从上一目录引用.
{% endhint %}

在 users.py 文件里, 就可以这么写了:

```python
@api.route("/users/<string:user_name>")
def user_username(user_name: str) -> str:
    pass
```

记得在创建应用的工厂函数里加上使用蓝图

```python
def create_app(config_name=config['default']):
    app = Flask(__name__)
    
    app.config.from_object(config_name)
    config[config_name].init_app(app)
    # blueprints here
    app.register_blueprint(api)

    db.init_app(app)
    
    return app
```

然后应用脚本这么用

```python
from flask import Flask
from flask_migrate import Migrate

from views import create_app, db
from config import config

app = create_app(config['development'])
migrate = Migrate(app, db)

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000)
```

## 需求文件

你可能在别人的 Python 项目里见过 requirements.txt 这一个文件. pip 的 -r 选项读取一个文件中需求的库作为输入:

```bash
pip install -r requirements.txt
```

现在问题是, 怎么生成这个文件. 我们有两种方式.

* 把全部需求文件全部输出. 如数家珍.

```bash
pip freeze > requirements.txt
```

* 只把要用的输出.

```bash
pip install pipreqs
pipreqs .
```

## 一些工程上的实现

### 用户身份验证

* Flask-Login：管理已登录用户的用户会话
* Werkzeug：计算密码散列值并进行核对
* itsdangerous：生成并核对加密安全令牌

### 用户角色

* 设计一个 Role 类

## 拓展阅读

[Salted Password Hashing - Doing it Right](https://zyw271828.github.io/sphdr-zh-cn/ch00.html): 你可能会想, 为啥要大费周章用一套加密的登录机制, 简单地通过某种 hash 映射一下不就完了, 甚至设计了一套自己的算法. 阅读这个文章可能会让你改变一些看法.

_Flask Web开发: 基于Python的Web应用开发实战. 第2版_: 这也是我们一开始推荐的 Flask 阅读图书. 你可以阅读第七章以及后面的章节(例如十四章讲的是RESTful接口)来进一步学习.
