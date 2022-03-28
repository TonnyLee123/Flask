
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
## ORM (Object Relational Mapper)
- 在 Database 和 Application 之間做 mapping
- 自動將 Python 代碼轉換成對應的SQL語法，再來進行對資料庫的操作
- 因此可以直接用 Python 對資料庫進行操作，不需要再去寫複雜的SQL語法處理資料的選取。
- 補圖片


# 二. 操作 Flask-SQLAlchemy 
## 1. 安裝 Flask-SQLAlchemy 
```
pip install flask-sqlalchemy
```
##  2. 引入 SQLAlchemy
```python
# app.py
from flask_sqlalchemy import SQLAlchemy
```
## 3. config[...]：設定資料庫連線
設定 SQLite 檔案路徑。
```python
app.config['SQLALCHEMY_DATABASE_URI']='sqlite:////tmp/test.db' 
# app.config['SQLALCHEMY_DATABASE_URI'] = [DB_TYPE]+[DB_CONNECTOR]://[USERNAME]:[PASSWORD]@[HOST]:[PORT]/[DB_NAME]
```
## 4. 建立 SQLAlchemy物件
物件建立後，提供一個名為Model的類別。
```
db = SQLAlchemy(app)
```
## 5. 建立 Model (database structure)
```python
class User(db.Model): # 繼承db.Model
    id = db.Column("id", db.Integer, primary_key=True)
    username = db.Column("username", db.String(80), unique=True, nullable=False)
    email = db.Column("email", db.String(120), unique=True, nullable=False)
    
    # how object is printed.
    # query時, 該print出何種結果在Terminal
    # 補截圖
    def __repr__(self):
        return f"User('{self.id}', '{self.username}', '{self.email}')"
```
# 三. 在 cmd 操作 db
## A. 存入資料
### 1. 開啟cmd, 並進入 project_folder
### 2. python
### 3. 引入db
```
from app.py import db
```
### 4. 引入 table模型
```
from app.py import User
```
### 5. 創建初始化的db
```
db.create_all()
```
### 5. 創建資料(row)
id 是 primary key，自動產出，不必給值。
```
user_1 = User(username = 'Tony', email = '123@demo.com')
user_2 = User(username = 'James', email = '456@demo.com')
```
### 6. 加入資料到 table
```
db.session.add(user_1)
db.session.add(user_2)
```
### 7. 提交資料
```
db.session.commit()
```

## B. Accessing data 

### 1. Get all user in user_table
Return list
```
User.query.all()
```
### 2. Get first user
list中的第一個
```
User.query.first()
```
### 3. Filter result
```
User.query.filter_by(username = 'Tony').all()
```
user = User.query.filter_by(username = 'Tony').first()
user
user.id
user.name


### 4. Get id為1的資料
```
User.query.get(1)
```
post_1 = Post(title = "Blog 1", content = "First Post", user_id = user.id)
post_2 = Post(title = "Blog 2", content = "Second Post", user_id = user.id)
db.session.add(post_1)
db.session.add(post_2)
db.session.commit()

user.posts
for p in user.posts:
print(post.title)
---
post = Post.query.first()
post
post.author

---
db.drop_all() 刪除db內所有資料
```

# ORM 一對多關聯
### 定義一對多模型
Step1. 設定 db.relationship(…) 關係  
Step2. 設定 db.ForeignKey(…) 關係
Product 和 AddToCar 這兩個部分，他們的關係為一對多 (表示為一個 Product 可以被多個 AddToCar 包含)  
首先 Product (一對多的一) 的部分需要設定 db.relationship() 來讓 SQLAlchemy 知道 Product 和 AddToCar 是有關聯的，而 backref=”product” 中的 product 則像是暗號，未來在讀取 AddToCar 表格時，後面只需像這樣加上 AddToCar.product，就可以輕鬆讀取到 Product 表格內的資料囉！
再來 AddToCar (一對多的多) 的部分需要設定 db.ForeignKey() 來告訴 SQLAlchemy 當兩張表連結時要以什麼為外接的 key。

```python
class User(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    name = db.Column(db.String(80), unique=True, nullable = False)
    email = db.Column(db.String(120), unique=True, nullable = False)
    posts = db.relationship('Post', backref='user')
        
    def __repr__(self):
        return f"User('{self.id}', '{self.username}', '{self.email}')"

class Post(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    title = db.Column(db.String(100, nullable = False))
    content = db.Column(db.Text, nullable = False)
    user_id = db.Column(db.Integer, db.ForeignKey('user.id'), nullable = False)
    
     def __repr__(self):
        return f"User('{self.title}', '{self.user_id}')"
  
  

```        


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
user_1 = User(username = 'Tony', email = '123@demo.com')
user_2 = User(username = 'James', email = '456@demo.com')
# id 是primary key，會自動產出id，不必給值。
# 加入db
```python
db.session.add(user_1)
db.session.add(user_2)
```


# ORM 一對多關聯
### 定義一對多模型
Step1. 設定 db.relationship(…) 關係  
Step2. 設定 db.ForeignKey(…) 關係
Product 和 AddToCar 這兩個部分，他們的關係為一對多 (表示為一個 Product 可以被多個 AddToCar 包含)  
首先 Product (一對多的一) 的部分需要設定 db.relationship() 來讓 SQLAlchemy 知道 Product 和 AddToCar 是有關聯的，而 backref=”product” 中的 product 則像是暗號，未來在讀取 AddToCar 表格時，後面只需像這樣加上 AddToCar.product，就可以輕鬆讀取到 Product 表格內的資料囉！
再來 AddToCar (一對多的多) 的部分需要設定 db.ForeignKey() 來告訴 SQLAlchemy 當兩張表連結時要以什麼為外接的 key。

```python
 class User(db.Model):
        id = db.Column(db.Integer, primary_key=True)
        name = db.Column(db.String(80))
        email = db.Column(db.String(120), unique=True)
        posts = db.relationship('Post', backref='user')


    class Post(db.Model):
        id = db.Column(db.Integer, primary_key=True)
        content = db.Column(db.Text)
        user_id = db.Column(db.Integer, db.ForeignKey('user.id')
        
query = Post.query.first()
print(query.user.name)  
```
## 在 cmd 操作 db
### 創建db，並填入資料
```
1. 進入 project_folder
2. python
3. 引入db
from app.py import db
3. 創建初始化的db
db.create_all
4. 創建 table
from app.py import User, Post
user_1 = User(username = 'Tony', email = '123@demo.com')
user_2 = User(username = 'James', email = '456@demo.com')
5. 加入資料到table
db.session.add(user_1)
db.session.add(user_2)
6. 提交資料
db.session.commit()

```
### 2. Accessing data in DB
```
1. Get all user in user_table (return list)
User.query.all
2. Get first user  (list中的第一個)
User.query.first()
3. Filter result
User.query.filter_by(username = 'Tony').all()

user = User.query.filter_by(username = 'Tony').first()
user
user.id
user.name


### 4. Get id為1的user 
User.query.get(1)

post_1 = Post(title = "Blog 1", content = "First Post", user_id = user.id)
post_2 = Post(title = "Blog 2", content = "Second Post", user_id = user.id)
db.session.add(post_1)
db.session.add(post_2)
db.session.commit()

user.posts
for p in user.posts:
print(post.title)


---
post = Post.query.first()
post
post.author

---
db.drop_all() 刪除db內所有資料
```
