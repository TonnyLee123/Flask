## 一. 安裝Flask
```
pip install Flask
```

## 二. 使用Flask建立第一個網頁
```python
# app.py
from flask import Flask

app = Flask(__name__)

# 當 url 是 http://127.0.0.1:5000 時，執行 home function。
@app.route("/")
def home():
    return "<p>Hello, World!</p>"
    
if __name__ == '__main__':
    app.run()
```
- Flask 是 class
- \_\_name\_\_: 用來定位目前載入資料夾的位置，用來判別 template__folder 或 static_folder 資料夾位置。
- route(): Decorator, 告訴 Flask, 緊接在Decorator下面的函式要載入在哪個URL中。


## 三. 運行 Flask 的兩種方式
### 方式一 flask command
**通過export FLASK_APP environment variable來告訴terminal要使用的application**
```cmd
set FLASK_APP = app.py
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
