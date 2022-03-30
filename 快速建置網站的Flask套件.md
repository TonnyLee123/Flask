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



取得資訊的時候GET
送出資訊的時候POST
更新資訊的時候UPDATE
刪除資訊的時候DELETE

預設GET

#The action attribute specifies where to send the form-data when a form is submitted.

使用者在提交文件的時候如果有格式不符的情況，系統需要讓使用者知道那一部份不符合規範，這時候可以利用flash將訊息由後端傳到前端，這是一個非常實用的function。
這是因為在flask中只要你要用到session，就需要有一組key去做加密，避免資安問題


# Template Engine
- 可以在 HTML file 中使用特殊語法來操作變量。
- 作用: 讀取並執行模板中的特殊語法標記，並根據傳入值傳給變數，輸出成最終的HTML頁面，以上過程稱為 rendering
- Jinja2 
# Jinja2 
- 允許部分python語法。
## Syntax
### 1. print variable
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

### 2. if statement 
```python
{% if user %}
    歡迎，{{ user }}!
{% else %}
    請註冊帳號。
{% endif %}
```
### 3. for loop
```python
{% for user in users %}
    <li> {{ user }} </li>
{% endfor %}
```

# render_template("檔案名稱.html", local_variable)
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

## UserMixin
記錄了四種用戶狀態
```
is_authenticated
登入成功時return True(這時候才能過的了login_required)
is_active
帳號啟用並且登入成功的時候return True
is_anonymous
匿名用戶return True(登入用戶會return False)
get_id()
取得當前用戶id
-把HTML檔案放在templates目錄中，這樣子render_template才有辦法載入HTML檔案。
```

	
# Template Inheritance
- 一個網站中，很多頁面部分內容是一樣的，如頁尾、導覽區、廣告區等等
- Jinja2 可以將網頁裡內容一樣的部分抽出來放在 "Base Template" 中，並套用到Child Template網頁上。
## Base Template
- 一般命名為 layout.html
- 設定網站的HTML基本架構
- {% block head %}
    - 填入相對應名稱的Child Template  
## Child Template
- 使用 {% extends %} 繼承了layout.html
```
{% extends "layout.html" %}
```
- 使用 {% block content %}...{% endblock %} 包住這頁獨特內容
- content是Child Template的名稱。同一個頁面中，content的名稱不可以一樣

### 範例
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
瀏覽器在呈現完整畫面時所需要的資源檔案，如JavaScript、CSS、img等。
如何在Flask裡面使用靜態檔案
如何有效地組織靜態檔案始終是所有網站框架所關心的問題。Flask也不例外，它提供了一種在應用程式中組織靜態文件的特定方法，如下所示：

![image](https://user-images.githubusercontent.com/90739897/159871453-609d5c71-9250-46bb-b1e2-907e1fb569e3.png)


在這個結構裡面，假設我們想要在template樣板中呈現logo.png時，我們可以使用以下方式來引用靜態檔案：
<img src='/static/images/logo.png'>

Flask預設會自動讀取該static資料夾裡面的內容
static與hello.py位於同一資料層級的位置。

