
# 一. 前言
## 1. 什麼是 SQLAlchemy ？
- A library that facilitates(促進) the communication between Python programs and databases.
- An ORM
## 2. 什麼是 Flask-SQLAlchemy ？
- Flask 本身不支援直接對資料庫操作
- Flask 的 extension
## 3. 為什麼使用 Flask-SQLAlchemy ？
- 可支援常用的資料庫, 如: sqlite、Mysql、PostgreSQL、MSSql、Oracle 等等。

## ORM (Object Relational Mapper)
- 在 Database 和 Application 之間做 mapping
- 將 **Python代碼**轉成對應的**SQL語法**，再來進行對資料庫的操作。
- Translate Python **classes** to **tables** on relational databases. 
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
- C:\Users\a1003\Desktop\flask_project_test\flask_test\site.db
- sqlite: 使用的db種類
- \/// 表示相對目前folder的路徑
```python
app.config['SQLALCHEMY_DATABASE_URI']='sqlite:///site.db' 
# app.config['SQLALCHEMY_DATABASE_URI'] = [DB_TYPE]+[DB_CONNECTOR]://[USERNAME]:[PASSWORD]@[HOST]:[PORT]/[DB_NAME]
```
## 4. 建立 SQLAlchemy物件
物件建立後，提供一個名為**Model**的類別(建立model時，供繼承)
```python
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
注意: 4, 5 順序調換會不能執行 為甚麼???
### 1. 開啟cmd, 並進入 project_folder
### 2. python
### 3. 引入db
```
from app import db
```
### 4. 創建初始化的db
```
db.create_all()
```
### 5. 引入 table模型
```
from app import User
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
User.query.filter_by(username = 'Tony')
```
### 4. Get id為1的資料
```
User.query.get(1)
```
### 5. 刪除db內所有資料
```
db.drop_all()
```

# One-to-many Relationship
## 1. 定義models
### 1. 設定 db.relationship(…)
一對多的**一** 設定 db.relationship() 讓 SQLAlchemy 知道 User 和 Post 是有關聯的
- posts不是Column，所以不會顯示在表格上 
- backref = ”user” 中的 user 像是暗號，未來在讀取 Post 表格時，可透過 Post.user，讀取到 User 表格內的資料 (user.post)
- lazy=True ???
```python
posts = db.relationship('Post', backref='user', lazy=True)
```
### 2. 設定 db.ForeignKey(…)
一對多的**多**設定 db.ForeignKey()
- 透過 ForeignKey 來連接兩個表格
```python
 user_id = db.Column(db.Integer, db.ForeignKey('user.id'), nullable = False) # 透過user_table中(因此user小寫)的id(pk)為fk
```

```python
class User(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    name = db.Column(db.String(80), unique=True, nullable = False)
    email = db.Column(db.String(120), unique=True, nullable = False)
    posts = db.relationship('Post', backref='user', lazy=True) # 1
        
    def __repr__(self):
        return f"User('{self.id}', '{self.username}', '{self.email}')"

class Post(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    title = db.Column(db.String(100, nullable = False))
    content = db.Column(db.Text, nullable = False)
    user_id = db.Column(db.Integer, db.ForeignKey('user.id'), nullable = False) # 2
    
    def __repr__(self):
       return f"User('{self.title}', '{self.user_id}')"
```
## 2. cmd 操作
```
user_1 = User(username = 'Tony', email = '123@demo.com)
db.session.add(user_1)
db.session.commit()
user = User.query.filter_by(username = 'Tony')
user.id

post_1 = Post(title = "Blog 1", content = "First Post", user_id = user.id)
post_2 = Post(title = "Blog 2", content = "Second Post", user_id = user.id)
db.session.add(post_1)
db.session.add(post_2)
db.session.commit()

user.posts
for p in user.posts:
print(post.title)

post = Post.query.first()
post
post.user
```
# 補充
## 1. URI, URL. URN
![image](https://user-images.githubusercontent.com/90739897/160451676-d3ae037c-6aee-4ab3-a99c-9c91d084e255.png)
