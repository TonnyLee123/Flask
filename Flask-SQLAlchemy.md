
# 一. 前言
## 1. 什麼是 SQLAlchemy ？
- A library that facilitates(促進) the communication between Python programs and databases.
- An ORM
## 2. 什麼是 Flask-SQLAlchemy ？
- Flask 本身不支援直接對資料庫操作
- Flask 的 extension
- 可支援常用的資料庫, 如: sqlite、Mysql、PostgreSQL、MSSql、Oracle 等等。

## 3. ORM (Object Relational Mapper)
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
- \/// 表示相對目前folder的路徑
```python
app.config['SQLALCHEMY_DATABASE_URI']='sqlite:///site.db' 
                                        [DB_TYPE]+[DB_CONNECTOR]://[USERNAME]:[PASSWORD]@[HOST]:[PORT]/[DB_NAME]
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

### URI
- Web使用的每種資源, 如HTM、圖像、影片等等都是使用URI來定位的
- URI一般由三部分組成：
    - 訪問資源的命名機制
    - 存放資源的主機名
    - 資源自身的名稱，由路徑表示，著重強調於資源
### URL
- 一種具體的URI
- 表示資源的所在位置
- 指明如何locate這個資源
- 採用URL可以用一種統一的格式來描述各種信息資源，包括文件、伺服器的地址和目錄等。
- URL一般由三部分組成：
    - 協議(或稱為服務方式)
    - 存有該資源的主機IP位址(有時也包括埠號)
    - 主機資源的具體地址。如目錄和文件名等
### URN（uniform resource name）
- 通過名字來標識資源，如mailto:java-net@java.sun.com
- 提供位置無關的標識方式

URI是以一種抽象的概念  
URL和URN則是具體的資源標識的方式  
URL和URN都是一種URI。  

## GET和POST的區別
- GET提交的數據會放在URL之後
    - 以?分割URL和傳輸數據
    - 以&相連參數
- EditPosts.html?name=test1&id=123456. POST方法是把提交的數據放在HTTP包的Body中.
- GET提交的數據大小有限制（因為瀏覽器對URL的長度有限制），而POST方法提交的數據沒有限制
- GET方式提交數據，會帶來安全問題
    - 如一個登錄頁面，通過GET方式提交數據時，用戶名和密碼將出現在URL上


## URI與URL的區別
- URI注重的是唯一標識符，而URL注重的是位置
- URI就是身份證號碼，URL就是身份證上的住址，通過身份證號（URI）肯定能找到我，但是你通過我的住址（URL）那就不一定能找到我
- 資源可以是圖片、文檔，也可以是今天的天氣
- 一個資源是可以擁有很多個URI，但一個URI只會對應一個資源，就像我們手上有很多張銀行卡，但每個銀行卡對應的開戶人，也只有我們自己一個人

## URI的組成
![image](https://user-images.githubusercontent.com/90739897/162347911-624a2ad0-720f-4233-9757-5ff45c0d87be.png)  

### https://naonao.com?name=naonao&age=18#page-7
- Scheme
    - 協議    
    - 如HTTP，HTTPS，FTP等
- Query
    - 查詢參數
    - 可選
    - 必須以 ? 開頭
    - 常用形式是 key=value，比如 name=naonao
- fragment
    - 可選的
    - 必須以 # 開頭
    - 如上 page-7 指向的是一個段落
- authority
    - 包含了用戶名與密碼（user infomation），主機名（host），以及端口號（port）
- path
    - 必須以 / 開頭
## lazy(懶載入)
- 延遲載
```
舉例
school和student,1對多
- lazy=False
  - 那麼只要載入了一個學校的資訊,就會根據一對多配置的關係把所有學生的資訊也載入出來。但實際上有時候只需要用到學校的資訊，不需要用到學生的資訊，這時學生資訊的載入就等於浪費資源。
- lazy=True
  - 只有當你訪問學校資訊的學生資訊才會去載入學生的資訊。
```
