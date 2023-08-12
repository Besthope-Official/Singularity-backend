# Flask 进阶

本周我们会介绍一种大型 Flask 项目的文件架构. 尽管 Flask 并不强制要求项目的具体组织方式(换句话说任凭你放飞自我, 写在单文件里也是没问题的), 但是采用一种具体的组织方式总能使你的编码更清晰.

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

为了再给应用提供一种定制配置的方式, Config 类及其子类可以定义 init\_app() 类方法, 其参数为应用实例.

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
def create_app(config_name=config['default']):
    app = Flask(__name__)
    
    app.config.from_object(config_name)
    app.register_blueprint(api)

    db.init_app(app)
    
    return app
```
