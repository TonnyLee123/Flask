
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
```python
app.config['SQLALCHEMY_DATABASE_URI'] = [DB_TYPE]+[DB_CONNECTOR]://[USERNAME]:[PASSWORD]@[HOST]:[PORT]/[DB_NAME]
```
### 方法一： 使用 sqlite 連線
```
# app.py
from flask_sqlalchemy import SQLAlchemy
from flask import Flask

db = SQLAlchemy() # 建立一個 SQLAlchemy物件

app = Flask(__name__)
app.config['SQLALCHEMY_DATABASE_URI'] = "sqlite:////tmp/test.db"

db.init_app(app)

@app.route('/create_db')
def index():
    db.create_all()
    return 'ok'

if __name__ == "__main__":
    app.run()
```
#### config：設定資料庫連線
- 進行初始化設定
- SQLALCHEMY_DATABASE_URI用來設定SQLite資料庫檔案路徑。（sqlite:///users.sqlite3）
```python
app.config['SQLALCHEMY_DATABASE_URI']='sqlite:///users.sqlite3'
app.config['SQLALCHEMY_TRACK_MODIFICATIONS'] = False
建立了物件之後，就會提供一個名為Model的類別，此類別可以用於宣告Model。
```
### 方法二： 使用 MySQL 連線
```python
app.config['SQLALCHEMY_TRACK_MODIFICATIONS'] = False
app.config['SQLALCHEMY_DATABASE_URI'] = "mysql+pymysql://user_name:password@IP:3306/db_name"

db.init_app(app)

@app.route('/')
def index():

    sql_cmd = """
        select *
        from product
        """

    query_data = db.engine.execute(sql_cmd)
    print(query_data)
    return 'ok'


if __name__ == "__main__":
    app.run()
```
如果使用 MySQL 當連線的資料庫，需要額外安裝 pymsql，安裝指令如下：

$ pip3 install pymysql
連線參數設定： 'SQLALCHEMY_DATABASE_URI' 為與資料庫連線的參數設定，其中 user_name、password 和 IP 請填入自己 Mysql 的資料，而 db_name 則是填入的 database 名稱。

3. 運行 python
最後運行 python3 main.py，如過沒有噴錯就代表資料庫連線成功囉～

如果有任何問題，歡迎底下留言或分享唷！
```
安裝
```python
pip install flask-sqlalchemy
```
安裝flask-sqlalchemy的時候會自動下載相依套件sqlachemy，不用特別另外安裝。

實務上我們會將Model另外設置一個Python文件管理

範例
範例_初始化資料庫

3. 運行 Flask 的兩種方式
▍選項1： (官方推薦此選項)
Flask 內建的 command line script 指令，在終端機輸入指令來運行 Flask：
export FLASK_APP 這行是環境設定待會 flask run 時，指定運行的 py 檔是 main.py
如果是 Windows 系統的朋友需將 export 改成 set

$ export FLASK_APP=main.py
$ flask run
Flask run 參數還可以加上以下指令

$ export FLASK_APP=main.py
$ flask run --reload --debugger --host 0.0.0.0 --port 80
–reload # 修改 py 檔後，Flask server 會自動 reload
–debugger # 如果有錯誤，會在頁面上顯示是哪一行錯誤
–host # 可以指定允許訪問的主機IP，0.0.0.0 為所有主機的意思
–port # 自訂網路埠號的參數
▍選項2：
export FLASK_APP=app.py
python -m flask run
此方法道理和選項一很像，只是用的不是 Flask 內建的 command line script，而是 Python -m <name> 的用法，一句話解釋就是 Python 會檢索 sys.path 路徑內的 name module 運行。

▍選項3 (不建議使用)：
使用 app.run() 方法有時會造成沒有原因的異常錯誤，或執行多次部分程式，所以在 Flask 0.11 後官方推出了上面的 command line script 方法，關於 Flask 官方文件中有說明此問題。
但部分網路上的教學是早期的文章，所以還是在教此方法，建議改用選項一的方法來執行 Flask server。

This works well for the common case but it does not work well for development which is why from Flask 0.11 onwards the flask method is recommended. The reason for this is that due to how the reload mechanism works there are some bizarre side-effects (like executing certain code twice, sometimes crashing without message or dying when a syntax or import error happens).

Flask document #in-code
在 main.py 的下方加入 if name == 'main':

if __name__ == '__main__':
    app.run()
並在終端機輸入以下指令

$ python app.py
3. Flask 運行後
