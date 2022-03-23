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
@app.route("/login", methods=["POST","GET"])
def login():
    if request.method == "POST":
        user = request.form["nm"]
        return redirect(url_for("user",usr=user))
    else:
        return render_template("login.html")
```
#### 問題點
去其他頁面後再回來，也會要求輸入帳號。如果我們想要將輸入的資訊，帶到接下來的頁面，該如何處理？說實在的這樣子的設計很不方便，而且我們也不希望登入的帳號出現在網址列上面。以下我們就要使用session來解決這個問題。
```python
```
```python
```
```python
```
```python
```
