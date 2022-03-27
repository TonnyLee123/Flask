
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

# database structure
# 建立 Model
class User(db.Model): # 繼承db.Model
    id = db.Column("id", db.Integer, primary_key=True)
    username = db.Column("username", db.String(80), unique=True, nullable=False)
    email = db.Column("email", db.String(120), unique=True, nullable=False)
    # how object is printed when we print it out
    # query 時顯示的結果
    def __repr__(self):
        return f"User('{self.id}', '{self.username}', '{self.email}')"
```
app.config['SQLALCHEMY_DATABASE_URI'] = [DB_TYPE]+[DB_CONNECTOR]://[USERNAME]:[PASSWORD]@[HOST]:[PORT]/[DB_NAME]

To create the initial database, just import the db object from an interactive Python shell and run the SQLAlchemy.create_all() method to create the tables and database:
## 建立 Model (database structure)
```python
class User(db.Model): # 繼承db.Model
    id = db.Column("id", db.Integer, primary_key=True)
    username = db.Column("username", db.String(80), unique=True, nullable=False)
    email = db.Column("email", db.String(120), unique=True, nullable=False)
```


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
# 建立初始化的資料庫
db.create_all()

# 提交資料到db
## why commit? 尚未學習
```python
db.session.commit()
```
## Accessing data in DB
### 1. Get all user
```python
User.query.all()
```
### 2. Get first user
```python
# list中的第一個
User.query.first()
```
### 3. Filter result
```python
User.query.filter_by(username = 'Tony').all()
```

### 4. Get id為1的user 
```python
User.query.get(1)
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
# 建立初始化的資料庫
db.create_all()

# 提交資料到db
## why commit? 尚未學習
```python
db.session.commit()
```
## Accessing data in DB
### 1. Get all user
```python
User.query.all()
```
### 2. Get first user
```python
# list中的第一個
User.query.first()
```
### 3. Filter result
```python
User.query.filter_by(username = 'Tony').all()
```

### 4. Get id為1的user 
```python
User.query.get(1)
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
# ORM（Object-relational Mapping）
會在 Database 和 Application 之間做 mapping
ORM 會在背後自動將 Python 代碼轉換成應對的SQL語法，再來進行對資料庫的操作。換句話說，開發者可以直接用 Python 的語法對資料庫進行操作，不需要再去寫複雜的SQL語法處理資料的選取，只需要簡單的操作物件的屬性與方法就可以達到寫SQL語法的效果。
另外，ORM提供了獨立於SQL的介面，它會自己處理不同資料庫之間的差異，因此便於移轉資料庫。並且使用快取最佳化技術可以加強資料庫操作的效率。

## 安裝Flask-SQLAlchemy
```python
pip install flask-sqlalchemy
```
## 載入
```python
from flask_sqlalchemy import SQLAlchemy
```
## config：設定資料庫連線
```python
app.config['SQLALCHEMY_DATABASE_URI']='sqlite:///users.sqlite3' # 用來設定SQLite資料庫檔案路徑。
app.config['SQLALCHEMY_TRACK_MODIFICATIONS'] = False
db = SQLAlchemy(app) # 建立SQLAlchemy物件。一旦建立了物件之後，就會提供一個名為Model的類別。
```
## Model
建立User類別，該類別繼承了db.Model
```python
class Users(db.Model):
    id = db.Column('id', db.Integer, primary_key=True) # primary_key 會自動產生
    name = db.Column('name', db.String(100))
    email = db.Column(db.String(100))
    
    def __init__(self, name, email):
        self.name =name
        self.email = email
```
![image](https://user-images.githubusercontent.com/90739897/159874353-ba72a413-d1b1-4929-b47c-b4a0b209aeb1.png)
### cmd 操作db
```
進入project folder
python
# 創建 db
from app.py import db
db.create_all
# 創建 table
from app.py import User, Post
user_1 = User(username = 'Tony', email = '123@demo.com')
user_2 = User(username = 'James', email = '456@demo.com')
# 加入資料到table
db.session.add(user_1)
db.session.add(user_2)
# 提交
db.session.commit()
# Accessing data in DB
### 1. Get all user
User.query.all() return list
### 2. Get first user
# list中的第一個
User.query.first()
### 3. Filter result
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
```
