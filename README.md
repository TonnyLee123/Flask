
# Template Engine
- 可以在 HTML file 中使用特殊語法來操作變量。
- 作用: 讀取並執行模板中的特殊語法標記，並根據傳入值傳給變數，輸出成最終的HTML頁面，以上過程稱為 rendering (渲染)
- Jinja2 

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
```

```
