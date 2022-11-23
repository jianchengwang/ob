---
title: flask-todolist
slug: flask-todolist
author: [jianchengwang]
date: 2020-11-28
excerpt: "Flask是一个使用 Python 语言编写的 Web 框架，它可以让你高效的编写Web程序。我最近用flask+vue搭建一个简单的todolist项目示例来学习"
draft: false
tags: [python]
---

`Flask`是一个使用 Python 语言编写的 Web 框架，它可以让你高效的编写 Web 程序。我最近用`flask+vue`搭建一个简单的`todolist` 项目示例来学习，主要是参考[flask-tutorial](https://github.com/greyli/flask-tutorial)，感兴趣的可以看看，[示例代码](https://github.com/jianchengwang/todo-python/raw/master/flask/todo-list)

## 前言

这个项目比较简单，就是用户登录，然后有一个`todolist`列表，可以简单的增删改，前端界面使用`vue`框架，所以关于`flask template`涉及不多，感兴趣的可以自己去学习下。本系列基于`python3`版本，所以命令跟`python2`可能有些许出入。

## 准备工作

### 基础软件

安装`python`，

编辑软件，一般文本软件软件即可，这个看个人习惯，我这边是用`vscode`

安装`git`，window用户推荐使用`git bash` 内置了很多linux命令

### 创建目录

```shell
mkdir todo-list
cd todo-list
mkdir app serve
```

我这边项目里面创建两个目录`app`（vue前端页面，这个这里不多做介绍），`serve`（flask后端服务），

### 虚拟环境

```shell
cd serve 
# 安装虚拟环境
py -3 -m venv env  # Windows or 
python3 -m venv env # Linux 和 macOS
# 激活虚拟环境
env\Scripts\activate  # Windows
. env/bin/activate  # Linux 或 macOS
# 安装flask
 pip3 install flask
```

`env`这个名字不固定，你也可以使用`venv`，记得`.gitignore`忽略掉这个目录。

在激活虚拟环境后，无论操作系统和 Python 版本，都可以统一使用 `python` 和 `pip` 命令来调用当前虚拟环境内的 Python 和 pip 程序/二进制文件。此时执行 `python` 或 `pip` 命令指向的程序和激活脚本在同一个目录下，在 Windows 下所在目录为 `env\Scripts\`，Linux 和 macOS 下所在目录为 `env/bin/`

## Hello Flask

`serve`根目录下创建`app.py`

```shell
# 创建app.py
vim app.py 

# app.py内容
from flask import Flask
app = Flask(__name__)

@app.route('/')
def hello():
    return 'Welcome to My Watchlist!'
    
# 启动
flask run    
```

现在打开浏览器，访问 [http://localhost:5000](http://localhost:5000/) 即可访问我们的程序主页

### 程序发现机制

如果你把上面的程序保存成其他的名字，比如 hello.py，接着执行 `flask run` 命令会返回一个错误提示。这是因为 Flask 默认会假设你把程序存储在名为 `app.py` 或 `wsgi.py` 的文件中。如果你使用了其他名称，就要设置系统环境变量 `FLASK_APP` 来告诉 Flask 你要启动哪个程序。

```shell
export FLASK_APP=hello.py # Linux 或 macOS
$Env:FLASK_APP=hello.py # Window PowerShell
set FLASK_APP=hello.py # Window CMD
```

### 管理环境变量

为了不用每次打开新的终端会话都要设置环境变量，我们安装用来管理系统环境变量的 `python-dotenv`

```shell
pip3 install python-dotenv
```

当 `python-dotenv` 安装后，`Flask` 会从项目根目录的 `.flaskenv`和 `.env` 文件读取环境变量并设置。

```shell
touch .env .flaskenv
```

`.flaskenv` 用来存储 Flask 命令行系统相关的公开环境变量；

`.env` 则用来存储敏感数据，不应该提交进Git仓库，我们把文件名 `.env` 添加到 `.gitignore` 文件的结尾（新建一行）来让 Git 忽略它。

开启调试模式：

```shell
vim .flaskenv
FLASK_ENV=development
```

## Routing

Web的核心功能，这个后面经常用，有个印象即可。

### Variable Rules

```python
@app.route('/user/<username>')
def show_user_profile(username):
    # show the user profile for that user
    return 'User %s' % escape(username)

@app.route('/post/<int:post_id>')
def show_post(post_id):
    # show the post with the given id, the id is an integer
    return 'Post %d' % post_id

@app.route('/path/<path:subpath>')
def show_subpath(subpath):
    # show the subpath after /path/
    return 'Subpath %s' % escape(subpath)
```

注意用户输入可能包含恶意代码，所以最好用`escape`进行转义处理

### URL Building

```python
@app.route('/')
def index():
    return 'index'

@app.route('/login', methods=['GET', 'POST'])
def login():
    return 'login'

@app.route('/user/<username>')
def profile(username):
    return '{}\'s profile'.format(escape(username))

with app.test_request_context():
    print(url_for('index'))
    print(url_for('login'))
    print(url_for('login', next='/'))
    print(url_for('profile', username='John Doe'))
```

```shell
/
/login
/login?next=/
/user/John%20Doe
```

### Redirects and Errors

```python
from flask import abort, redirect, url_for

@app.route('/')
def index():
    return redirect(url_for('login'))

@app.route('/login')
def login():
    abort(401)
    this_is_never_executed()
```

```python
from flask import render_template

@app.errorhandler(404)
def page_not_found(error):
    return render_template('page_not_found.html'), 404
```

### Request

```python
from flask import request

@app.route('/login', methods=['POST', 'GET'])
def login():
    error = None
    if request.method == 'POST':
        if valid_login(request.form['username'],
                       request.form['password']):
            return log_the_user_in(request.form['username'])
        else:
            error = 'Invalid username/password'
    # the code below is executed if the request method
    # was GET or the credentials were invalid
    return render_template('login.html', error=error)
```

```python
searchword = request.args.get('key', '')
```

```python
@app.route('/upload', methods=['GET', 'POST'])
def upload_file():
    if request.method == 'POST':
        f = request.files['the_file']
        f.save('/var/www/uploads/' + secure_filename(f.filename))
```

### Response

#### return string

```python
return 'hello world'
```

#### rerun html

```python
return '<hr>Hello World</hr>'
```

#### render template

```python
return render_template('error.html'), 404

# or
resp = make_response(render_template('error.html'), 404)
resp.headers['X-Something'] = 'A value'
return resp
```

#### return json

```python
@app.route("/me")
def me_api():
    user = get_current_user()
    return {
        "username": user.username,
        "theme": user.theme,
        "image": url_for("user_image", filename=user.image),
    }
```

```python
@app.route("/users")
def users_api():
    users = get_all_users()
    return jsonify([user.to_json() for user in users])
```

## 数据库

这边为了简单，使用`Sqlite`

### flask-sqlalchemy

为了简化数据库操作，我们将使用 [SQLAlchemy](https://www.sqlalchemy.org/)——一个 Python 数据库工具（ORM，即对象关系映射）。借助 `SQLAlchemy`，你可以通过定义 Python 类来表示数据库里的一张表（类属性表示表中的字段 / 列），通过对这个类进行各种操作来代替写 SQL 语句。

Flask 有大量的第三方扩展，这些扩展可以简化和第三方库的集成工作。我们下面将使用一个叫做 [Flask-SQLAlchemy](http://flask-sqlalchemy.pocoo.org/2.3/) 的官方扩展来集成 SQLAlchemy。

```shell
pip3 install flask-sqlalchemy
```

大部分扩展都需要执行一个“初始化”操作。你需要导入扩展类，实例化并传入 Flask 程序实例：

```
from flask_sqlalchemy import SQLAlchemy  # 导入扩展类
app = Flask(__name__)
db = SQLAlchemy(app)  # 初始化扩展，传入程序实例 app
```

### 数据库URL

为了设置 Flask、扩展或是我们程序本身的一些行为，我们需要设置和定义一些配置变量

[Flask文档配置页面](https://flask.palletsprojects.com/en/1.1.x/config/)

 [Flask-SQLAlchemy 文档的配置页面](http://flask-sqlalchemy.pocoo.org/2.1/config/)

- `sqlite:////tmp/test.db`
- `mysql://username:password@server/db`

```python
import os
import sys

from flask import Flask
from flask_sqlalchemy import SQLAlchemy

WIN = sys.platform.startswith('win')
if WIN:  # 如果是 Windows 系统，使用三个斜线
    prefix = 'sqlite:///'
else:  # 否则使用四个斜线
    prefix = 'sqlite:////'

app = Flask(__name__)
app.config['SQLALCHEMY_DATABASE_URI'] = prefix + os.path.join(app.root_path, 'data.db')
app.config['SQLALCHEMY_TRACK_MODIFICATIONS'] = False  # 关闭对模型修改的监控
# 在扩展类实例化前加载配置
db = SQLAlchemy(app)
```

### 创建模型

我们这边就只有两个模型，一个用户模型，一个代办项目模型

```python
class User(db.Model, UserMixin): # 表名将会是 user
    id = db.Column(db.Integer, primary_key=True)
    username = db.Column(db.String(20))
    password_hash = db.Column(db.String(128))

class TodoItem(db.Model):
    __tablename__ = 'todo_item' # 表名将会是 todo_item
    id = db.Column(db.Integer, primary_key=True)  # 主键
    title = db.Column(db.String(32))  # 标题
    descs = db.Column(db.String(256))  # 描述
```

### 自定义命令

注册命令，便于我们通过**CLI**对程序进行一些额外的数据处理，比如数据库建表，脚本初始化等

```python
import click

@app.cli.command()  # 注册为命令
@click.option('--drop', is_flag=True, help='Create after drop.')  # 设置选项
def initdb(drop):
    """Initialize the database."""
    if drop:  # 判断是否输入了选项
        db.drop_all()
    db.create_all()
    click.echo('Initialized database.')  # 输出提示信息
```

```shell
flask initdb # 创建数据库
flask initdb --drop # 清空数据库
```

### CURD

#### 创建

```python
from app import User, TodoItem  # 导入模型类
>>> user = User(username='wjc')  # 创建一个 User 记录
>>> ti1 = TodoItem(title='study')  # 创建一个 TodoItem 记录
>>> ti2 = TodoItem(title='game')  # 再创建一个 TodoItem 记录
>>> db.session.add(user)  # 把新创建的记录添加到数据库会话
>>> db.session.add(ti1)
>>> db.session.add(ti2)
>>> db.session.commit()  # 提交数据库会话，只需要在最后调用一次即可
```

#### 读取

```shell
<模型类>.query.<过滤方法（可选）>.<查询方法>
```

```python
>>> from app import TodoItem  # 导入模型类
>>> todoItem = TodoItem.query.first()  # 获取 Movie 模型的第一个记录（返回模型类实例）
>>> todoItem.title  # 对返回的模型类实例调用属性即可获取记录的各字段数据
'study'
>>> TodoItem.query.all()  # 获取 TodoItem 模型的所有记录，返回包含多个模型类实例的列表
[<TodoItem 1>, <TodoItem 2>]
>>> TodoItem.query.count()  # 获取 Movie 模型所有记录的数量
2
>>> TodoItem.query.get(1)  # 获取主键值为 1 的记录
<TodoItem 1>
>>> TodoItem.query.filter_by(title='game').first()  # 获取 title 字段值为 game 的记录
<TodoItem 2>
>>> TodoItem.query.filter(TodoItem.title=='game').first()  # 等同于上面的查询，但使用不同的过滤方法
<TodoItem 2>
```

[sqlalchemy query](https://docs.sqlalchemy.org/en/14/orm/query.html)

#### 更新

```python
>>> todoItem = TodoItem.query.get(2)
>>> todoItem.title = 'play ball'  # 直接对实例属性赋予新的值即可
>>> todoItem.descs = '玩球'
>>> db.session.commit()  # 注意仍然需要调用这一行来提交改动
```

#### 删除

```python
>>> todoItem = TodoItem.query.get(1)
>>> db.session.delete(todoItem)  # 使用 db.session.delete() 方法删除记录，传入模型实例
>>> db.session.commit()  # 提交改动
```

### Flask Marshmallow

因为`sqlalchemy`查询出来的数据不能直接序列化，所以一般要转成`dict`，这边引入[flask-marshmallow](https://flask-marshmallow.readthedocs.io/en/latest/)来处理

```shell
pip3 install  marshmallow-sqlalchemy
pip3 install flask-marshmallow
```

引入

```python
from flask import Flask
from flask_marshmallow import Marshmallow

app = Flask(__name__)
ma = Marshmallow(app)
```

定义

```python
class TodoItemSchema(ma.SQLAlchemyAutoSchema):
    class Meta:
        model = TodoItem
```

使用

```python
@app.route('/todoItems', methods=['GET'])
def all():
    todoItems = TodoItem.query.all()
    todoitems_schema = TodoItemSchema()
    return jsonify(result = todoitems_schema.dump(todoItems, many=True))
```

## 用户认证

### 密码存储

`Flask` 的依赖 `Werkzeug` 内置了用于生成和验证密码散列值的函数，`werkzeug.security.generate_password_hash()` 用来为给定的密码生成密码散列值，而 `werkzeug.security.check_password_hash()` 则用来检查给定的散列值和密码是否对应

```python
from werkzeug.security import generate_password_hash, check_password_hash

class User(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    name = db.Column(db.String(20))
    username = db.Column(db.String(20))  # 用户名
    password_hash = db.Column(db.String(128))  # 密码散列值

    def set_password(self, password):  # 用来设置密码的方法，接受密码作为参数
        self.password_hash = generate_password_hash(password)  # 将生成的密码保持到对应字段

    def validate_password(self, password):  # 用于验证密码的方法，接受密码作为参数
        return check_password_hash(self.password_hash, password)  # 返回布尔值
```

### flask-jwt-extended

[flask-jwt-extended](https://flask-jwt-extended.readthedocs.io/en/stable/)

```shell
pip3 install flask-jwt-extended
```

```python
from flask_jwt_extended import (
    create_access_token,
    jwt_required,
    get_jwt_identity
)

# 创建toekn
access_token = create_access_token(identity=username)
# 获取当前用户
current_user_name = get_jwt_identity()

# 装饰器拦截接口
@app.route('/todoItem', methods=['POST'])
@jwt_required
def add():
    todoItem = TodoItem(title = request.form['title'], descs = request.form['descs'])
    db.session.add(todoItem)
    db.session.commit()
    return utils.result(msg= 'Item added.')
```

## 代码结构

代码结构调整，待定。。。

## 打包部署

待定

## 相关链接

[flask-tutorial](https://github.com/greyli/flask-tutorial)

[flask 1.1](https://flask.palletsprojects.com/en/1.1.x/)

