# POST form
### 補充html 表單
## 建立 form
```python
from flask import Flask, redirect, url_for, render_template,request
```
建立login函式對應login路由，並在route中宣告使用POST與GET兩種HTTP存取方式，對應的HTML樣板則是login.html：
```python
@app.route("/login", methods=["POST","GET"])
def login():
    return render_template("login.html")
```
建立user函式與路由：
```python
@app.route("/<user>")
def user(user):
    return render_template("user.html",user=user)
```
建立HTML樣板，樣板裡面放form表單，使用POST方法。
```html
{% extends "base.html" %}
{% block title %}登入{% endblock %}

{% block body %}
<form action="#" method="post"> 
  <div class="form-group"> 
    <label for="username">Name:</label> 
    <input id="username" class="form-control" type="text" name="nm" />
  </div> 
  
  <div class="form-group"> 
    <button type="submit" class="btn btn-primary">Submit</button>
  </div> 
</form>

{% endblock %}
```
## 傳遞表單資料
- 取得使用者在表單上輸入的資料
- request.form["inuput.name"]

request.form[“nm”]。
在 "login函式" 加上if…else…
當user透過POST傳遞資料時,
就抓取表單中 name 為 "nm" 的值，並且指定給變數user後，
將網頁導向user頁面，並且將user的值帶入該頁面
；否則就直接解析login.html樣版並呈現給使用者。
```python
from flask import Flask, redirect, url_for, render_template,request
@app.route("/", methods=["POST","GET"])
def login():
    if request.method == "POST":
        user = request.form["nm"]
        return redirect(url_for("user",user=user))
    else:
        return render_template("login.html")

#user page
@app.route("/<user>")
def user(user):
    return render_template("user.html",user=user)
```
#### 問題點
去其他頁面後再回來，要求重新輸入帳號。

#### 解決方式：使用sessions
當登入一個網站，session可以紀錄登入的帳號資訊，讓在之後瀏覽這網站其他頁面可繼續使用這個session資訊作為判斷。如此，不用每次進入一個頁面，就要重新再登入一次。當離開這個網站，或登出網站時，session儲存的資訊也會跟著消失。另外，它不像cookie，session的資訊是儲存在伺服器端，相對較安全。


接下來我們要試著做一個登入頁面，當使用者登入後，session會紀錄登入者的名稱，並且引導使用者到其他的頁面。
匯入session套件
為了達成這個目的，我們要修改上面的程式碼login的部分，在login函式中加上session，實際上方法很簡單，首現要匯入session套件：
```python
from flask import session
```
```python
```
```python
```
```python
```
