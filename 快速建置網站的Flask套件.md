## 一. 安裝Flask
```
pip install Flask
```

## 二. 使用Flask建立第一個網頁
```python
# app.py
from flask import Flask

app = Flask(__name__)

# Decorator, 告訴 Flask, 緊接在Decorator下面的函式要載入在哪個 URL 中。  
@app.route("/") # 當 url 是 http://127.0.0.1:5000 時，執行 home function。
def home():
    return "<p>Hello, World!</p>" # 要顯示在網頁上的內容，HTML格式
    
# 透過 flask run 不會用到以下代碼
# python app.py 才會
if __name__ == '__main__':
    app.run()
```
- Flask 是 class
- \_\_name\_\_: 用來定位目前載入資料夾的位置，用來判別 template__folder 或 static_folder 資料夾位置。
## 三. 運行 Flask 的兩種方式
### 方式一 flask command
**通過export FLASK_APP environment variable來告訴terminal要使用的application**
```cmd
set FLASK_APP = app.py
# set Flask_DEBUG = 1
flask run

# 加上其他指令
flask run --reload --debugger --host 0.0.0.0 --port 80
```
- reload: 修改 py 檔後，Flask server 會自動 reload
- debugger: 如果有錯誤，會在頁面上顯示是哪一行錯誤
- host: 指定允許訪問的主機IP，0.0.0.0 為所有主機的意思
- port: 自訂網路埠號的參數
### 方式二
**Python 會檢索 sys.path 路徑內的 name module 然後運行。**
```cmd
set FLASK_APP = app.py
python -m flask run
```

### 方式三 
```
python app.py
```

## 四. Flask 運行後
### 終端機顯示：
```
Serving Flask app "main.py" (lazy loading)
Environment: production
WARNING: This is a development server. Do not use it in a production deployment.
Use a production WSGI server instead.
Debug mode: off
Running on http://127.0.0.1:5000/ (Press CTRL+C to quit)
Restarting with stat
Debugger is active!
```
- WARNING: This is a development server. Do not use it in a production deployment.
    - 目前使用的環境是 production，未來產品要上線，只需將 FLASK_ENV 設成 development 模式就不會顯示警告。
    - ```set FLASK_ENV=development```
- http://127.0.0.1:5000/
    - 網頁網址
    - 127.0.0.1 localhost


# 建立第一個app

```python
from flask import Flask
app = Flask(__name__)

@app.route("/") 
def home():  
    return "Hello"
    
app.run()
```
methods是用來定義這個路由是否執行相關的GET、POST
取得資訊的時候GET
送出資訊的時候POST
更新資訊的時候UPDATE
刪除資訊的時候DELETE

預設為允許GET

#The action attribute specifies where to send the form-data when a form is submitted.

使用者在提交文件的時候如果有格式不符的情況，系統需要讓使用者知道那一部份不符合規範，這時候可以利用flash將訊息由後端傳到前端，這是一個非常實用的function。
這是因為在flask中只要你要用到session，就需要有一組key去做加密，避免資訊安全問題，因此我們需要調整Python做參數設置如下：
{{ 兩個大括號是參數綁定 }} {% 這樣是執行jinja2內置函數 %} {# 這樣是註解說明 #}
　　目前來說，只要在企業內要自建一個系統就一定會牽扯到對資料庫的操作，如果每一個操作都寫著一句SQL語法，那維護起來版面著實嚇人，ORM的出現幫忙程式設計師簡化了大量這類基礎SQL語法的操作，也可以減少SQL injection攻擊(SQL injection)。

Flask並沒有限定一定要使用SQLAlchemy這類ORM操作的套件，但是SQLAlchemy可以搭配sqlite、Mysql、PostgreSQL、MSSql、Oracle，這已經涵蓋市面上常用的資料庫。而flask-sqlachemy是為了簡化Flask開發人員操作而出現的套件，它適度的包裝了SQLAlchemy。

直觀來看，透過ORM我們可以將資料庫當做物件來操作，這讓開發人員不用花心思的去處理資料庫的DDL(Create, Drop…)、DML(Select, Delete, Update, Insert…)。如果你開發過MVC那就一定很了解這一塊就是MODEL。
目前為止，網站頁面都只有些許靜態的文字，但實際一個網站是由複雜的HTML組成，為了使程式看起來簡潔，Flask預設使用Jinja2樣版引擎，將HTML頁面與後台應用程式聯繫起來。
# Template Engine
- 可以在 HTML file 中使用特殊語法來操作變量。
- 作用: 讀取並執行模板中的特殊語法標記，並根據傳入值傳給變數，輸出成最終的HTML頁面，以上過程稱為 rendering (渲染)
- Jinja2 
- 
## Jinja2 
- 允許部分python語法。
### Syntax
#### 1. print variable
```
{{ variable }}
```
```python
list = ['apple', 'banana']
dict = {'name': 'Tony', 
	'age': '20'}
	
# Python
lis[0]
dict['name']

# Template
{{ list.0 }}
{{ dict.name }}
```

#### 2. if statement 
```python
{% if user %}
    歡迎，{{ user }}!
{% else %}
    請註冊帳號。
{% endif %}
```
#### 3. for loop
```python
{% for user in users %}
    <li> {{ user }} </li>
{% endfor %}
```

### render_template("檔案名稱.html", local_variable)
```python
from flask import render_template
mylist = [1, 2, 3]
@app.route("/")
def home():
    return render_template('home.html', my_list = mylist)
    # 第一個my_list是模板中會用到的
```
```html
<!--home.html-->
<body>
	<p>列表第一個元素: {{ my_list[0] }}</p>
	<p>列表第二個元素: {{ my_list[1] }}</p>
	<p>列表第三個元素: {{ my_list[2] }}</p>
  
</body>
```
1. 模板文件名稱
- 'login.html'
2. 模板會使用到的varirable
- my_list
## 內建變數
可供template直接使用。
- config
- request
- session
- g
## 全局函數
可以在所有模板中使用
- url_for()
- get_flashed_message()

## flash()
- 產生 flash_message
- flash_message儲存在session中，因此要為程式設置密鑰，然後在模板中使用get_flashed_message()獲取訊息。
```python
------------------__init__.py----------------------
from flask_sqlalchemy import SQLAlchemy #(class)
db = SQLAlchemy(web)
--------------------------------------------------
# 創作user的database
from flaskblog import db, login_manager 
from datetime import datetime
from flask_login import UserMixin # (class)

@login_manager.user_loader # 新增以下功能到user_loader
def load_user(user_id) # 透過user_id載入user
    return User.query.get(int(user_id))
# db

class User(db.Model, UserMixin):
    id = db.Column(db.Integer, primary_key = True)
    username = db.Column(db.String(20), unique = True, nullable = False)
    email = db.Column(db.String(120), unique = True, nullable = False)
    img_file = db.Column(db.String(20), nullable = False, default = 'default.jpg')
    password = db.Column(db.String(60), nullable = False)
    
    # backref 類似於加入其他column to the post model
    # define when SQL alchemy loads the data from the database; true: load the data as necessary in one go
    posts = db.relationship('Post', backref = 'author', lazy = True) # posts 的值和 Post(class) 有 One to Many Relationship
    # how object is printed
    def __repr__(self):
        return f"User('{self.username}', '{self.email}', '{self.img_file}')"
    
class Post(db.Model):
    id = db.Column(db.Integer, primary_key = True)
    title = db.Column(db.String(100), nullable = False)
    date_posted = db.Column(db.DateTime(120), nullable = False, default = datetime.utcnow)
    content = db.Column(db.Text, nullable = False)
    # 注意user.id 不是User, 因為會自動創一個table called user, 根據前table(user)的primary key 為此table的foreign key
    user_id = db.Column(db.Integer, db.ForeignKey('user.id'), nullable = False )
    def __repr__(self):
        return f"User('{self.title}', '{self.date_posted}')"
```
# models.py
- 資料庫
- Table 模型

```
Foreign Key
Use to link two tables together via the primary key. It means the columns of one table points to the primary key attribute of the other table. It further means that if any attribute is set as a primary key attribute will work in another table as a foreign key attribute. 

```

UserMixin
UserMixin幫我們記錄了四種用戶狀態：

is_authenticated
登入成功時return True(這時候才能過的了login_required)
is_active
帳號啟用並且登入成功的時候return True
is_anonymous
匿名用戶return True(登入用戶會return False)
get_id()
取得當前用戶id
-把HTML檔案放在templates目錄中，這樣子render_template才有辦法載入HTML檔案。



	
	
# Template Inheritance
- 一個網站中，很多頁面部分內容是一樣的，如頁尾、導覽區、廣告區等等
- Jinja2 可以將網頁裡內容一樣的部分抽出來放在 "基本樣板" 中，並套用到子樣板網頁上。
## Base Template

- 一般命名為layout.html
- 用來設定網站的HTML基本架構。可以存放共用內容：
例如：<html>、<head>與<body>，以及<head>裡面放頁面標題的<title>、頁面關鍵字與說明的<meta>、CSS樣式檔的<link>與javaScript檔案的<script>。
## Child Template
- 繼承的方式是在子樣本中使用{% extends %}。這表示這個子範本繼承了layout.html範本。
{% extends "layout.html" %}

用{% block content %}{% endblock %}這頁獨特內容。

content是子範本本體的名稱。同一個頁面中，content的名稱不可以一樣。
實際使用

block預留一個空位，等之後套用這個樣板的頁面就可以填上
### 例子
```python
# base
<!doctype html>
<html>
  <head>
    {% block head %}
    <link rel="stylesheet" href="{{ url_for('static', filename='style.css') }}">
    <title>{% block title %}{% endblock %} - My Webpage</title>
    {% endblock %}
  </head>
  <body>
    <div id="content">{% block content %}{% endblock %}</div>
    <div id="footer">
      {% block footer %}
      &copy; Copyright 2010 by <a href="http://domain.invalid/">you</a>.
      {% endblock %}
    </div>
  </body>
</html>
```
	
```python
# child 
{% extends "layout.html" %}
{% block title %}Index{% endblock %}
{% block head %}
  {{ super() }}
  <style type="text/css">
    .important { color: #336699; }
  </style>
{% endblock %}
{% block content %}
  <h1>Index</h1>
  <p class="important">
    Welcome on my awesome homepage.
{% endblock %}
```
靜態檔案

哪些是靜態檔案
瀏覽器在呈現完整畫面時所需要的資源檔案，如JavaScript、CSS、img等。
如何在Flask裡面使用靜態檔案
如何有效地組織靜態檔案始終是所有網站框架所關心的問題。Flask也不例外，它提供了一種在應用程式中組織靜態文件的特定方法，如下所示：

![image](https://user-images.githubusercontent.com/90739897/159871453-609d5c71-9250-46bb-b1e2-907e1fb569e3.png)


在這個結構裡面，假設我們想要在template樣板中呈現logo.png時，我們可以使用以下方式來引用靜態檔案：
<img src='/static/images/logo.png'>

Flask預設會自動讀取該static資料夾裡面的內容
static與hello.py位於同一資料層級的位置。

