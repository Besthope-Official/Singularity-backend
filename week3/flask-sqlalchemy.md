# Flask SQLAlchemy

## 事前准备

如果你按照我们求生手册的一步步走下来的话, 你应该已经准备好了大部分的工具.

确保你安装好了必要的工具:

* MySQL: 数据库连接

```bash
$ sudo mysql
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 10
Server version: 8.0.33-0ubuntu0.22.04.2 (Ubuntu)

Copyright (c) 2000, 2023, Oracle and/or its affiliates.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql>
```

* 数据库连接的插件:&#x20;
  * 如果你使用 vscode 的话, 安装好 Database Client.
  * Pycharm 集成了数据库管理, 你可以直接用.
* 必要的库

```
(.venv) $ pip install flask-sqlalchemy
(.venv) $ pip install mysqlclient
```

## 连接到数据库

数据库 URL:

<table><thead><tr><th>数据库引擎</th><th>URL</th><th data-hidden></th></tr></thead><tbody><tr><td>MySQL</td><td>mysql://username:password@hostname/database</td><td></td></tr><tr><td>Postgres</td><td>postgresql://username:password@hostname/database</td><td></td></tr><tr><td>SQLite (Unix)</td><td>sqlite:////absolute/path/to/database</td><td></td></tr></tbody></table>

<pre class="language-python"><code class="lang-python"><strong>SQLALCHEMY_DATABASE_URI = f'mysql://
</strong><strong>    {USERNAME}:{PASSWORD}@{HOSTNAME}:{PORT}/{DB_NAME}'
</strong></code></pre>

为了安全起见, 最好把数据库用户及密码等隐私信息保存在另外的文件.

应用使用的数据库 URL 必须保存到 Flask 配置对象的 SQLALCHEMY\_DATABASE\_URI 键中.

Flask-SQLAlchemy 文档还建议把 SQLALCHEMY\_TRACK\_MODIFICATIONS 键设为False, 以便在不需要跟踪对象变化时降低内存消耗.

## 示例1: MySQL, 启动!

```python
from flask import Flask
from flask_sqlalchemy import SQLAlchemy

app = Flask(__name__)

# MySQL configuration
app.config['SQLALCHEMY_DATABASE_URI'] = 'mysql://username:password@hostname/database'
db = SQLAlchemy(app)
app.config['SQLALCHEMY_TRACK_MODIFICATIONS'] = False
```

我们可以利用 `db.session.execute()` 去执行原生的 SQL 语句. 例如, 查询一个已有表(user)中的所有行, 并输出字段(username), 你可以按照下面的方式进行:

```python
from sqlalchemy import text

@app.route('/')
def index():
    res = db.session.execute(text('SELECT * FROM users'))
    for row in res:
        print(row.username)
```

但这并不是一个好的做法, 因为 Flask-SQLAlchemy 自带对象关系映射 (ORM) 模式, 让我们进一步了解下这是个什么东西!

## 对象关系映射

* ORM(Object **R**elational Mapping): 它把数据库中的表格映射成程序中的对象. 我们可以用类似编程语言的方式来操作数据库, 而不用直接编写 SQL 查询语句

{% hint style="info" %}
这个世界上还有 ODM 的东西......

ODM(Object **D**ocument Mapping): 文档数据库不使用表格, 而是以类似 JSON 的文档格式存储数据,但效果是类似的

* 你可以去了解些 NoSQL, 例如 MongoDB.
{% endhint %}

总的来说, ORM 模式可以提供更高层次的抽象, 使得数据库操作更易于管理和维护, 并且可以帮助你避免一些常见的安全问题, 比如 SQL 注入攻击:

<figure><img src="../.gitbook/assets/OIP-C.jpg" alt=""><figcaption><p>如果敢拍我我就把你数据库扬了:></p></figcaption></figure>

我们给出一个 SQL 注入的示例. 用一个登录的例子来说, 要验证的是用户名和密码匹配, 用原生的 SQL 语句去执行

```python
@app.route('/login', methods=['POST'])
def login():
    username = request.form.get('username')
    password = request.form.get('password')
    
    query = f"SELECT * FROM User WHERE username = '{username}' AND password = '{password}'"
    result = db.session.execute(query)
    
    if result.fetchone():
        return "Login successful"
    else:
        return "Login failed"
```

但如果, 有个不做人的用户, 通过了某种手段, 在请求里将用户名设置为了 `' OR '1'='1`, 那么

```sql
SELECT * FROM User WHERE username = '' OR '1'='1' AND password = 'password'
```

注意 `'1'='1'` 是一个永真的语句, 又因为添加了一个 `OR`, 这个筛选条件永远为真, 因此, 无论密码是否匹配, 都能获取对应用户的权限!

如果你对这个话题很感兴趣, 可以阅读[这篇文章](https://guicommits.com/how-sql-injection-attack-works-with-examples/). 以及去了解 CTF!

### 模型类

模型这个术语表示应用使用的持久化实体. 在 ORM 中, 模型一般是一个 Python 类, 类中的属性对应于数据库表中的列.

Flask-SQLAlchemy 创建的数据库实例为模型提供了一个基类以及一系列辅助类和辅助函数, 可用于定义模型的结构.

```python
class User(db.Model):
    __tablename__ = 'users'
    id = db.Column(db.Integer, primary_key=True)
    username = db.Column(db.String(80), unique=True, nullable=False)
    email = db.Column(db.String(120), unique=True, nullable=False)
    
    def __repr__(self):
        return f'{self.name}'
```

* Flask-SQLAlchemy 要求每个模型都定义主键,  这一列经常命名为id.
* 类变量 \_\_tablename\_\_ 定义在数据库中使用的表名(不指定 Flask 自己会创建).
* 其余的类变量都是该模型的属性, 定义为 db.Column 类的实例.
* db.Column 类构造函数的第一个参数是数据库列和模型属性的类型. 下面两个表贴出了常用的类型:

| 类型名          | Python 类型 | 说明              |
| ------------ | --------- | --------------- |
| Integer      | int       | 普通整数，通常是32位     |
| SmallInteger | int       | 取值范围小的整数，通常是16位 |

<table><thead><tr><th>类型名</th><th>Python 类型</th><th>说明</th><th data-hidden></th></tr></thead><tbody><tr><td>BigInteger</td><td>int 或 long</td><td>不限制精度的整数</td><td></td></tr><tr><td>Float</td><td>float</td><td>浮点数</td><td></td></tr><tr><td>Numeric</td><td>decimal.Decimal</td><td>定点数</td><td></td></tr><tr><td>String</td><td>str</td><td>变长字符串</td><td></td></tr><tr><td>Text</td><td>str</td><td>变长字符串，对较长或不限长度的字符串做了优化</td><td></td></tr><tr><td>Unicode</td><td>unicode</td><td>变长 Unicode 字符串</td><td></td></tr><tr><td>UnicodeText</td><td>unicode</td><td>变长 Unicode 字符串，对较长或不限长度的字符串做了优化</td><td></td></tr><tr><td>Boolean</td><td>bool</td><td>布尔值</td><td></td></tr><tr><td>Date</td><td>datetime.date</td><td>日期</td><td></td></tr><tr><td>Time</td><td>datetime.time</td><td>时间</td><td></td></tr><tr><td>DateTime</td><td>datetime.datetime</td><td>日期和时间</td><td></td></tr><tr><td>Interval</td><td>datetime.timedelta</td><td>时间间隔</td><td></td></tr><tr><td>Enum</td><td>str</td><td>一组字符串</td><td></td></tr><tr><td>PickleType</td><td>任何 Python 对象</td><td>自动使用 Pickle 序列化</td><td></td></tr><tr><td>LargeBinary</td><td>str</td><td>二进制 blob</td><td></td></tr></tbody></table>

你可以试试 flask-sqlacodegen 这个插件, 就省去了手写模型类的麻烦.

很多情况下, 在编写 RESTful 接口时, 我们希望返回一个 JSON 格式的数据, 那么, 在模型类里, 我们可以定义一个 `to_json` 方法(这也称为**序列化**):

```python
class Post(db.Model):
    __tablename__ = 'posts'

    id = db.Column(db.Integer, primary_key=True, info='主键')
    title = db.Column(db.String(255))
    create_time = db.Column(db.DateTime, info='创建时间')
    image = db.Column(db.String(255))
    content = db.Column(db.Text)
    
    def to_json(self):
        json_post = {
            'id' : self.id,
            'title' : self.title,
            'created_time' : self.create_time,
            'image' : self.image,
            'content' : self.content
        }
        return json_post
```

这么写的好处是: 在视图函数里返回就可以用一个简洁的列表推导式

```python
@api.route('posts')
def get_posts():
    posts = Post.query.all()
    return jsonify({'Posts' : [post.to_json() for post in posts]})
```

### 数据库操作

{% hint style="info" %}
你可以在 flask shell 里实时进行操作! 另外, 这些数据库相关的操作建议查阅[文档](http://www.pythondoc.com/flask-sqlalchemy/quickstart.html).
{% endhint %}

* 创建表 `db.create_all()`
* 删除表 `db.delete_all()`
* 插入行要分几个步骤

创建一个模型实例, 然后把它加入到表里

```python
>>> test = User(id=1, username='123')
>>> db.session.add(test)
```

插入多个行 `add_all()`, 传入的是模型实例的一个列表.

要记得写入数据 `db.session.commit()` 这个和 Git 操作很像!

修改数据, 直接修改实例就好. 记得每次修改需要重新写入.

* 查询行 query

用 all() 方法取回对应表中的所有记录

```python
>>> User.query.all()
[<User 1>]
```

筛选方法:

```python
>>> User.query.filter_by(username='1').all()
```

如果你想知道 ORM 把这些操作都转换成了哪些SQL语句, 你可以将这些 query 对象转换成字符串即可

<pre class="language-python"><code class="lang-python"><strong>>>> str(User.query.filter_by(username='1'))
</strong>'SELECT user.id AS user_id, user.username AS user_username, user.email AS user_email \nFROM user \nWHERE user.username = %s'
</code></pre>

我们给出一些常用的过滤器:

| 方法名           | 说明                         |
| ------------- | -------------------------- |
| `filter()`    | 把过滤器添加到原查询上，返回一个新查询        |
| `filter_by()` | 把等值过滤器添加到原查询上，返回一个新查询      |
| `limit()`     | 使用指定的值限制原查询返回的结果数量，返回一个新查询 |
| `offset()`    | 偏移原查询返回的结果，返回一个新查询         |
| `order_by()`  | 根据指定条件对原查询结果进行排序，返回一个新查询   |
| `group_by()`  | 根据指定条件对原查询结果进行分组，返回一个新查询   |

下表列出了执行查询的其他方法

| 方法名              | 说明                                 |
| ---------------- | ---------------------------------- |
| `all()`          | 以列表形式返回查询的所有结果                     |
| `first()`        | 返回查询的第一个结果，如果没有结果，则返回 None         |
| `first_or_404()` | 返回查询的第一个结果，如果没有结果，则返回 404 错误响应     |
| `get()`          | 返回指定主键对应的行，如果没有对应的行，则返回 None       |
| `get_or_404()`   | 返回指定主键对应的行，如果没找到指定的主键，则返回 404 错误响应 |
| `count()`        | 返回查询结果的数量                          |
| `paginate()`     | 返回一个 `Paginate` 对象，包含指定范围内的结果      |

## 关系

关系型数据库使用关系把不同表中的行联系起来, 这也正是它强大处之一.

* 角色到用户的一对多关系. 一个角色可属于多个用户, 而每个用户都只能有一个角色.

```python
class Role(db.Model):
    # ...
    users = db.relationship('User', backref='role')

class User(db.Model):
    # ...
    role_id = db.Column(db.Integer, db.ForeignKey('roles.id'))
```

关系使用 users 表中的外键连接两行, 添加到User 模型中的 role\_id 列被定义为外键, 就是这个外键建立起了关系.

db.relationship() 中的 backref 参数向 User 模型中添加一个 role 属性, 从而定义反向关系. 通过User 实例的这个属性可以获取对应的 Role 模型对象, 而不用再通过 role\_id 外键获取.

多数情况下, db.relationship() 都能自行找到关系中的外键, 但有时却无法确定哪一列是外键. 例如, 如果 User 模型中有两个或以上的列定义为 Role 模型的外键, SQLAlchemy 就不知道该使用哪一列. 如果无法确定外键, 就要为db.relationship() 提供额外的参数.

下表列出了常用的SQLAlchemy关系选项.

| 选项名           | 说明                                                                                                                                    |
| ------------- | ------------------------------------------------------------------------------------------------------------------------------------- |
| backref       | 在关系的另一个模型中添加反向引用                                                                                                                      |
| primaryjoin   | 明确指定两个模型之间使用的联结条件；只在模棱两可的关系中需要指定                                                                                                      |
| lazy          | 指定如何加载相关记录，可选值有select（首次访问时按需加载）、immediate（源对象加载后就加载）、joined（加载记录，但使用联结）、subquery（立即加载，但使用子查询）、noload（永不加载）和dynamic（不加载记录，但提供加载记录的查询） |
| uselist       | 如果设为False，不使用列表，而使用标量值                                                                                                                |
| order\_by     | 指定关系中记录的排序方式                                                                                                                          |
| secondary     | 指定多对多关系中关联表的名称                                                                                                                        |
| secondaryjoin | SQLAlchemy 无法自行决定时，指定多对多关系中的二级联结条件                                                                                                    |

除了一对多之外, 还有其他几种关系类型.

* 一对一关系可以用前面介绍的一对多关系表示, 但调用 db.relationship() 时要把 uselist 设为 False, 把“多”变成“一”.
* 多对一关系也可使用一对多表示, 对调两个表即可, 或者把外键和db.relationship() 都放在 “多”这一侧.

最复杂的关系类型是多对多，需要用到第三张表，这个表称为**关联表**（或 联结表）。

例如, 你要表示学生和课程的关系. 显然一个学生可以选多门课程, 一个课程也可以包含多名学生.

你需要建立一个 registrations 的关联表, 里面包含 student\_id 和 course\_id, 构建起学生和课程的映射. 查询多对多关系时, 现在关联表里找到这名学生的所有记录, 然后按照多到一的方向遍历课程和注册之间的一对多关系.(听起来是挺绕的)

数据库设计和用法相关的话题十分重要, 有大量相关的图书, 本节只是一个最简单的介绍.

## 拓展: sqlacodegen

给各位一段 Snippet, 大家可以按照自身需求修改, 不用自己去写 Model 类.

推荐 [flask-sqlacodegen](https://github.com/ksindi/flask-sqlacodegen), 安装通过 Git 安装比较好(`pip` 安装不知道为什么在我这里会出现兼容问题)

```bash
git clone https://github.com/ksindi/flask-sqlacodegen.git
cd flask-sqlacodegen/
python setup.py install
```

```python
import os
from config import Config


def gen_signal(table_name, mysql_url):
    args_str = "flask-sqlacodegen %s --flask --tables %s --outfile %s.py" % (
        mysql_url, table_name, table_name)
    try:
        os.system(args_str)
    except Exception as e:
        print(f"Error generating signal for table {table_name}: {e}")


def gen_all(name, mysql_url):
    args_str = "flask-sqlacodegen %s --flask --outfile %s.py " % (
        mysql_url, name)
    try:
        os.system(args_str)
    except Exception as e:
        print(f"Error generating all for {name}: {e}")


if __name__ == '__main__':
    url = Config().SQLALCHEMY_DATABASE_URI
    try:
        gen_all('models', url)
        # gen_signal('models_all', url)
        # gen_signal('users', url)
        print('Models generate complete.')
    except Exception as e:
        print(f'Error occurs in main: {e}')

```
