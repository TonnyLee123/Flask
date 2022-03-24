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
