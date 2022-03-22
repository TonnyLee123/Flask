
# 一. 前言
## 1. 什麼是 SQLAlchemy ？
- A library that facilitates the communication between Python programs and databases.
- An ORM tool
    -  Translate Python classes to tables on relational databases. 
    -  Automatically converts function calls to SQL statements.
## 2. 什麼是 Flask-SQLAlchemy ？
- Flask 本身不支援直接對資料庫操作，而 Flask-SQLAlchemy 是 Flask 的 extension，
- 簡化 Flask 開發人員操作資料庫而出現的套件，它適度的包裝了SQLAlchemy。

## 3. 為什麼使用 Flask-SQLAlchemy ？
- 可支援市面上常用的資料庫, 如: sqlite、Mysql、PostgreSQL、MSSql、Oracle。
- 可以使用原生 SQL下指令，也同時支援 ORM 框架來操作資料庫，可以隨時切換很方便。
- 
## 補充 Object Relational Mapper


# 二. 操作 Flask-SQLAlchemy 
## 1. 安裝
```
pip install flask-sqlalchemy
```
## 2. 進行資料庫連線

###  sqlite 連線
```python
# app.py
from flask import Flask
from flask_sqlalchemy import SQLAlchemy

app = Flask(__name__)

# 設定資料庫連線
# 設定SQLite資料庫檔案路徑
app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:////tmp/test.db'
# 物件建立後，會提供一個名為Model的class。
db = SQLAlchemy(app)

# 建立 Model
class User(db.Model): # 繼承db.Model
    id = db.Column("id", db.Integer, primary_key=True)
    username = db.Column("username", db.String(80), unique=True, nullable=False)
    email = db.Column("email", db.String(120), unique=True, nullable=False)

    def __repr__(self):
        return f"User('{self.id}', '{self.username}', '{self.email}')"
```
app.config['SQLALCHEMY_DATABASE_URI'] = [DB_TYPE]+[DB_CONNECTOR]://[USERNAME]:[PASSWORD]@[HOST]:[PORT]/[DB_NAME]

To create the initial database, just import the db object from an interactive Python shell and run the SQLAlchemy.create_all() method to create the tables and database:

# 建立初始化的資料庫
db.create_all()


from yourapplication import User
admin = User(username='admin', email='admin@example.com')
guest = User(username='guest', email='guest@example.com')
# But they are not yet in the database, so let’s make sure they are:

db.session.add(id)
db.session.add(username)
db.session.commit()

Accessing the data in database
User.query.all()
[<User u'admin'>, <User u'guest'>]
>>> User.query.filter_by(username='admin').first()
<User u'admin'>
Note how we never defined a __init__ method on the User class? That’s because SQLAlchemy adds an implicit constructor to all model classes which accepts keyword arguments for all its columns and relationships. If you decide to override the constructor for any reason, make sure to keep accepting **kwargs and call the super constructor with those **kwargs to preserve this behavior:

class Foo(db.Model):
    # ...
    def __init__(self, **kwargs):
        super(Foo, self).__init__(**kwargs)
        # do custom stuff
```
