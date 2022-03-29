# Flask-Login
**藉由 session 來提供使用者實現網站登入與登出的功能**
# Flask-Login 大致操作
## 1. 安裝 flask-login
```
pip install flask-login
```

## 2. 匯入 
```python
from flask_login import LoginManager # class
```
## 3. 建立LoginManager實體
```python
login_manager = LoginManager()
```
## 4. 初始化，並且提供一個登入的view
初始化
- 使app有login_manager的功能
```python
login_manager.init_app(app)
login_manager.login_view = 'login' # 將view指向login頁面。
```

# 實作
# \_\_init_\_\.py
用來初始化Python的「myproject」 packages
## 1. 匯入Flask套件，以及其他本應用程式所需要的套件
```python
import os
from flask import Flask
from flask_sqlalchemy import SQLAlchemy
from flask_migrate import Migrate

# flask_login
from flask_login import LoginManager
```
## 2. 路徑設定
設定專案路徑
```python
basedir = os.path.abspath(os.path.dirname(__file__))
# os.path.abspath()取得絕對的路徑位置。__file__ 為目前的檔案。os.path.dirname(__file__) 是指目前檔案所在的資料夾名稱。
```

## 3. 建立app實體
```python
app = Flask(__name__)
```

## 4. 設定config
```
app.config['SECRET_KEY']= 'acretkeyinthisproject'
app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:///'+os.path.join(basedir,'data.sqlite')
app.config['SQLALCHEMY_TRACK_MODIFICATIONS'] = False
```
## 5.建立資料庫實體與設定Migrate
```python
db = SQLAlchemy(app)
Migrate(app,db)
```
## 建立LoginManager實體
```python
login_manager = LoginManager()
login_manager.init_app(app)
login_manager.login_view = 'login'
```
# models.py
處理與資料庫相關問題

## 1. 匯入套件
匯入實體
```python
from myproject import db, login_manager
from werkzeug.security import generate_password_hash, check_password_hash
from flask_login import UserMixin
```

## 2. 建立 User Class
```python
class User(db.Model, UserMixin):
    id = db.Column(db.Integer, primary_key = True)
    email = db.Column(db.String(64),unique=True, index=True)
    username = db.Column(db.String(64),unique=True, index=True)
    password_hash = db.Column(db.String(128))
    def __init__(self, email, username, password):
        self.email = email
        self.username = username
        self.password_hash = generate_password_hash(password) # 儲存加密後的密碼
    
    # 檢查登入時輸入的密碼與資料庫中存放的加密後password_hash是否一樣，如果相同，可以進入網站，反之則否。
    def check_password(self, password):
        return check_password_hash(self.password_hash, password)
        
    # login_manager裝飾器，用來載入user
    @login_manager.user_loader
    def load_user(user_id):
        return User.query.get(user_id)
```
# forms.py
處理表單及資料輸入相關問題
## 1. 匯入套件
```python
from flask_wtf import FlaskForm
from wtforms import StringField, PasswordField, SubmitField
from wtforms.validators import DataRequired, Email, EqualTo, email_validator
from wtforms import ValidationError
```
## 2. 表單製作(Login及Registration表單)
## 2.1 登入表單
```
class LoginForm(FlaskForm):
    email = StringField('電子郵件', validators=[DataRequired(), Email()])
    password = PasswordField('密碼',validators=[DataRequired()])
    submit = SubmitField('登入系統')
```
## 2.2 註冊表單
DataRequired("目標欄位")
- 在這個欄位輸入的值需要與目標欄位一致
```
class RegistrationForm(FlaskForm):
    email = StringField('電子郵件', validators=[DataRequired(), Email()])
    username = StringField('使用者', validators=[DataRequired()])
    password = PasswordField('密碼', validators=[DataRequired(), EqualTo('pass_confirm', message='密碼需要吻合')])
    pass_confirm = PasswordField('確認密碼', validators=[DataRequired()])
    submit = SubmitField('註冊')
     
    # 檢查輸入的email與username是否與資料庫中存在的紀錄重複。重複就產生錯誤。
    # 檢查email
    def check_email(self, field): # field 用來接收 email
        if User.query.filter_by(email=field.data).first():
            raise ValidationError('電子郵件已經被註冊過了')
    # 檢查username 
    def check_username(self, field):
        if User.query.filter_by(username=field.data).first():
            raise ValidationError('使用者名稱已經存在')
```
# app.py
建立網站需要的所有路由

## 1. 匯入套件
```
from flask import render_template, redirect, request, url_for, flash, abort

from flask_login import login_user, logout_user, login_required # 

from myproject import app, db
from myproject.models import User
from myproject.forms import LoginForm, RegistrationForm
```
## 2. 新增路由
### 2.1 首頁
```
@app.route('/')
def home():
    return render_template('home.html')
```
### 2.2 登入
```
@app.route('/login',methods=['GET','POST'])
def login():
    form = LoginForm()
    if form.validate_on_submit():
        user = User.query.filter_by(email=form.email.data).first()
        if user.check_password(form.password.data) and user is not None:
            login_user(user)
            flash("您已經成功的登入系統")
            next = request.args.get('next')
            if next == None or not next[0]=='/':
                next = url_for('welcome_user')
            return redirect(next)
    return render_template('login.html',form=form)
```
### 2.3 登出
登出雖然沒有頁面，但是仍然需要路由/logout提供給登出使用。
@login_required用來確認使用者狀態必須是在登入狀態
```
@app.route('/logout')
@login_required
def logout():
    logout_user()
    flash("您已經登出系統")
    return redirect(url_for('home'))
```

### 2.4 註冊
讓經過驗證的使用者註冊資料可以被存在資料庫中，我們需要對db.session進行寫入的步驟：
```
@app.route('/register',methods=['GET','POST'])
def register():
    form = RegistrationForm()
    if form.validate_on_submit():
        # 建立一個row 在user_table
        user = User(email=form.email.data,
        username=form.username.data, password=form.password.data)       
        # add to db table
        db.session.add(user)
        db.session.commit()
        flash("感謝註冊本系統成為會員")
        return redirect(url_for('login'))
    return render_template('register.html',form=form)
```
### 2.5 會員專區
```
@app.route('/welcome')
@login_required
def welcome_user():
    return render_template('welcome_user.html')
```
## 3. 啟動app
```
if __name__ == '__main__':
    app.run(debug=True)
```
# templates.py
## 1. base.html
網站共用的部分寫在這個樣板中
```
{% if current_user.is_authenticated %}
<li class="nav-item">
<a class="nav-link" href="{{url_for('logout')}}">登出系統</a>
</li>
{% else %}
<li class="nav-item">
<a class="nav-link" href="{{url_for('login')}}">登入系統</a>
</li>
<li class="nav-item">
<a class="nav-link" href="{{url_for('register')}}">註冊帳號</a>
</li>
{% endif %}
```
## 2. home.html
```
{% extends "base.html" %}
{% block title %}首頁 ｜Login Exp{% endblock %}
{% block content %}
 ...略...
{% endblock %}
```
在選單做切換開關，如果目前的狀態是登入時，這時候current_user.is_authenticated 即為True，就顯示「登出系統」的連結；反之則顯示「登入系統」與「註冊帳號」的連結。
```
{% if current_user.is_authenticated %}
<h1 class="display-4">{{current_user.username}}</h1>
{% else %}
<p>請 <a href="{{url_for('login')}}">登入帳號</a> 或 <a href="{{url_for('register')}}">註冊帳號</a>
</p>
{% endif %}
```
### 3. login.html
```
<form method="POST">
{# This hidden_tag is a CSRF security feature. #} 
{{ form.hidden_tag() }}
<div class="form-group">
{{ form.email.label }} 
{{ form.email(class_="form-control form-control-lg") }}
</div>
<div class="form-group">
{{ form.password.label }} 
{{ form.password(class_="form-control form-control-lg") }}
</div>
{{form.submit(class_="btn btn-primary btn-lg")}}
</form>
```
## 4. register.html
```
<form method="POST">
{{ form.hidden_tag() }}
<div class="form-group">
{{ form.email.label }} 
{{ form.email(class_="form-control form-control-lg") }}
</div>
<div class="form-group">
{{form.username.label}} 
{{form.username(class_="form-control form-control-lg") }}
</div>
<div class="form-group">
{{ form.password.label }} 
{{ form.password(class_="form-control form-control-lg") }}
</div>
<div class="form-group">
{{ form.pasw_confirm.label }} 
{{ form.pasw_confirm(class_="form-control form-control-lg") }}
</div>
{{form.submit(class_="btn btn-primary btn-lg")}}
</form>
```
## 5. welcome_user.html
```
<h3>{{current_user.username}}，歡迎回來</h3>
```

# 使用 app.py 來啟動網站應用
