## 安裝Flask
```
pip install Flask
```

## 使用Flask建立第一個網頁
```python
# app.py
from flask import Flask

app = Flask(__name__)

@app.route("/")
def hello_world():
    return "<p>Hello, World!</p>"
    
if __name__ == '__main__':
    app.run()
```

_name__ 用來定位目前載入資料夾的位置，用來判別 template__folder 或 static_folder 資料夾位置。
然後我們使用 route() 裝飾器告訴 Flask 哪個 URL 應該觸發我們的函數。

## 運行 Flask 的兩種方式
### 方式一 flask command
**通過export FLASK_APP environment variable來告訴terminal要使用的application**
```cmd
set FLASK_APP = app.py
flask run
```

### 方式二
**Python 會檢索 sys.path 路徑內的 name module 然後運行。**
```
set FLASK_APP = app.py
python -m flask run
```

### 方式三 
```
python app.py
```
