到目前為止，網站頁面中都只有靜態的文字。如果我們單純透過Python來產出一頁頁HTML標籤，並不是一件輕鬆的事
，Flask預設使用Jinja2樣版引擎。將HTML頁面與後台應用程式聯繫起來，達到簡化HTML輸出的目的。
# Template Engine
- 可以在 HTML file 中使用特殊語法來操作變量。
- 作用: 讀取並執行模板中的特殊語法標記，並根據傳入值傳給變數，輸出成最終的HTML頁面，以上過程稱為 rendering (渲染)
- Jinja2 
- render_template(“網頁檔案名稱.html”, 參數與區域變數)
## Jinja2 
- 讓我們可以寫python語法，但並不適用於全部。
### 語法
```
#1 keyword
{% if %}
  ...
{% endif %}

#2 print
{{ ... }}

#3 comment
{# ... #}

#4 '.'獲取變數屬性
user.username
```
### render_template()
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


變數
樣板要顯示的變數，可以是一般的變數，也可以是字典（dict）或串列（list）。Jinja2樣板的語法與Python的語法有些不同處，使用上要注意。
串列（list）：Python中的語法是 list_name[num]而在樣板語言則是：{{list_name.0}}
例如：

```python
list_foods = ['apple','banana']

//Python
list_foods[0]
list_foods[1]

//Template
{{list_foods.0}}
{{list_foods.1}}

dict_foods = {'1':'apple','2':'banana','3':'candy'}

//Python
dict_foods['1']
dict_foods['2']
dict_foods['3']

//Template
{{dict_foods.1}}
{{dict_foods.2}}
{{dict_foods.3}}
```

### if statement 

{% if user %}
    歡迎，{{ user }}!
{% else %}
    歡迎，第一次來本站。
{% endif %}
### for loop

<ul>
    {% for item in news %}
    	<li>{{ item }}</li>
    {% endfor %}
	
	
#範本繼承
一個網站中，很多頁面的部分內容是一樣的，如頁首、頁尾、以及導覽區、廣告區等等。Jinja2可以將網頁裡內容一樣的部分抽出來放在基本樣板中，並且套用到同一系列的子樣板網頁上。
# 基本樣板
存放共用內容的檔案。一般我們會將它命名為base.html，用這個檔案來設定網站的HTML基本架構。你可以將共通的部分放在這裡：
例如：<html>、<head>與<body>，以及<head>裡面放頁面標題的<title>、頁面關鍵字與說明的<meta>、CSS樣式檔的<link>與javaScript檔案的<script>。
# 子樣板
由於基本範本是HTML頁面共通的部分，在製作上我們不再需要重複這部分的內容，只要在其他頁面裡繼承基本範本的內容就可以。繼承的方式是在子樣本中使用{% extends %}這個關鍵字來呼叫base.html。這表示這個子範本繼承了base.html範本。
{% extends "base.html" %}
要注意的是，extends必須是子範本中的第一個標籤。
我們可以用{% block content %}{% endblock %}放基本範本所沒有的內容。
{% block content %}{% endblock %}
使用block區塊是子樣板的關鍵字，content是子範本本體的名稱。同一個頁面中，content的名稱不可以一樣。
實際使用
下面的base.html基本樣板使用了bootstrap的CSS框架。中間使用了{% block body %}{% endblock %} 來給子樣板使用。
<!DOCTYPE html>
<html lang="zh-tw">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>{{site_name}}</title>
<link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.5.2/css/bootstrap.min.css" integrity="sha384-JcKb8q3iqJ61gNV9KGb8thSsNjpSL0n8PARn9HuZOnIxN0hoP+VmmDGMN5t9UJ0Z" crossorigin="anonymous">
</head>
<body>
<nav class="navbar navbar-expand-lg navbar-light bg-light">
<a class="navbar-brand" href="#">Navbar</a>
<button class="navbar-toggler" type="button" data-toggle="collapse" data-target="#navbarNav" aria-controls="navbarNav" aria-expanded="false" aria-label="Toggle navigation">
<span class="navbar-toggler-icon"></span>
</button>
<div class="collapse navbar-collapse" id="navbarNav">
<ul class="navbar-nav">
  <li class="nav-item active">
  <a class="nav-link" href="/">Home <span class="sr-only">(current)</span></a>
  </li>
  <li class="nav-item">
  <a class="nav-link" href="/hello/sean">Hello Sean</a>
  </li>
</ul>
</div>
</nav>
<div class="container">
{% block body %}
{% endblock %}
</div>
<script src="https://code.jquery.com/jquery-3.5.1.slim.min.js" integrity="sha384-DfXdz2htPH0lsSSs5nCTpuj/zy4C+OGpamoFVy38MVBnE+IbbVYUew+OrCXaRkfj" crossorigin="anonymous"></script>
<script src="https://cdn.jsdelivr.net/npm/popper.js@1.16.1/dist/umd/popper.min.js" integrity="sha384-9/reFTGAW83EW2RDu2S0VKaIzap3H66lZH81PoYlFhbGU+6BZp6G7niu735Sk7lN" crossorigin="anonymous"></script>
<script src="https://stackpath.bootstrapcdn.com/bootstrap/4.5.2/js/bootstrap.min.js" integrity="sha384-B4gt1jrGC7Jh4AgTPSdUtOBvfO8shuf57BaghqFfPlYxofvL8/KUEfYiJOMMV+rV" crossorigin="anonymous"></script>
</body>
</html>
hello.html子樣板。在子樣板裡面，第一行必須是使用extends語法把基本樣板拉進來。之後，可以放block區塊，在這裡我們放了組block，名稱剛好等於基本樣板裡面的名稱（ 一樣是 {% block body %}，這樣兩者就可以對應在一起了）。
{% extends "base.html" %}
{% block body %}
<div class="row">
<div class="col">
  <h1>{{name}}, Welcome. This is Home Page</h1>
  <p>Lorem ipsum dolor sit amet consectetur adipisicing elit. Accusamus ducimus eveniet laudantium voluptatem labore, atque perferendis reiciendis quas ipsam a est assumenda eos distinctio molestiae nam itaque, cum voluptates eligendi.</p>
</div>
<div class="col">
  <h1>This is {{name}}'s first Page</h1>
  <p>Lorem ipsum dolor sit amet consectetur adipisicing elit. Maiores nemo maxime culpa, iure, minima ducimus magni non hic similique tempore impedit necessitatibus eum! Sunt adipisci porro aperiam fugit magnam delectus.</p>
</div>
</div>
{% endblock %}
基本上來說，就是在block的地方挖了個洞，預留一個空位，等之後套用這個樣板的頁面就可以填上。這就是 Jinja2 運作的方式，下一回我們會來討論關於傳送資料的方式。
