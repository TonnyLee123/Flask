Flask-Login
實現網站登入與登出的功能


Flask-Login provides user session management for Flask. It handles the common tasks of logging in, logging out, and remembering your users’ sessions over extended periods of time.
所謂flask-login藉由 user session來提供使用者登入的任務，諸如使用者的登入、登出以及在一定的期間內記住目前的使用者，因此，flask-login可以將使用者的 ID儲存在session中，讓使用者可以方便的登入、登出網站應用服務。
安裝套件
在使用flask-login前，必須透過pip將套件安裝在自己的虛擬環境中：

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
```python
login_manager.init_app(app)
login_manager.login_view = 'login' # 將view指向login頁面。
```

# __init__.py
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
## 5.資料庫db
設定資料庫db與Migrate
```python
db = SQLAlchemy(app)
Migrate(app,db)
```
## 建立LoginManager實體
初始化
- 使app有login_manager的功能
```python
login_manager = LoginManager()
login_manager.init_app(app)
login_manager.login_view = 'login'
```
# models files
處理與資料庫相關的問題

## 1. 匯入套件
```python
from myproject import db, login_manager
from werkzeug.security import generate_password_hash, check_password_hash
from flask_login import UserMixin
```

## 建立 User Class
```python
class User(db.Model, UserMixin):
    id       = db.Column(db.Integer, primary_key = True)
    email    = db.Column(db.String(64),unique=True, index=True)
    username = db.Column(db.String(64),unique=True, index=True)
    password_hash = db.Column(db.String(128))
    def __init__(self, email, username, password):
        """初始化"""
        self.email = email
        self.username = username
        self.password_hash = generate_password_hash(password) # 儲存加密後的密碼
    
    # 檢查登入時輸入的密碼與資料庫中存放的加密後password_hash是否一樣，如果相同，可以進入網站，反之則否。
    def check_password(self, password):
        return check_password_hash(self.password_hash, password)
```
