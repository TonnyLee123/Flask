# POST form
### 補充html 表單

## 1. 建立HTML樣板
```html
<html>
	<head>
		<title> 網頁標題 </title>
		<meta charset="utf-8">
	</head>

	<body>
		<form action="#" method="post">
		  <div class="form-group">
		    <label for="username">Name:</label>
		    <input id="username" class="form-control" type="text" name="nm" />
		  </div>
		  <div class="form-group">
		    <button type="submit" class="btn btn-primary">Submit</button>
		  </div>
		</form>
	</body>
</html>
```
output

![image](https://user-images.githubusercontent.com/90739897/159725435-4e2a3878-5cf2-4057-88f6-7b201cb57d48.png)


## 建立 routes 並 傳遞表單資料到 user page
- 取得使用者在表單上輸入的資料
- request.form["inuput.name"]

request.form[“nm”]。


抓取表單中 name 為 "nm" 的值，並且指定給變數user後
```python
from flask import Flask, redirect, url_for, render_template,request
app = Flask(__name__)

#login page
@app.route("/", methods=["POST","GET"]) # 使用POST與GET兩種HTTP存取方式
def login():
    if request.method == "POST": # 當發送POST請求時(按下submit)
        user = request.form["nm"] # 把輸入在 form 的值 assign 給 user
        return redirect(url_for("user",user=user)) # 導向user頁面，並將 user 的值傳入該頁面
    else:
        return render_template("login.html") # 否則使用login.html樣版

#user page
@app.route("/<user>")
def user(user):
    return render_template("user.html", user=user)
    
if __name__ =="__main__":
    app.run(debug=True)
```
#### 問題點
無法在其他頁面保持登入
只有在目前的頁面是登入狀態，
去其他頁面後再回來，要求重新輸入帳號。

#### 解決方式：使用sessions
當登入一個網站，session可以紀錄登入的帳號資訊，讓在之後瀏覽這網站其他頁面可繼續使用這個session資訊作為判斷。如此，不用每次進入一個頁面，就要重新再登入一次。當離開這個網站，或登出網站時，session儲存的資訊也會跟著消失。另外，它不像cookie，session的資訊是儲存在伺服器端，相對較安全。


接下來我們要試著做一個登入頁面，當使用者登入後，session會紀錄登入者的名稱，並且引導使用者到其他的頁面。
匯入session套件
為了達成這個目的，我們要修改上面的程式碼login的部分，在login函式中加上session，實際上方法很簡單，首現要匯入session套件：
```python
from flask import session
```
修改login函式
```python
@app.route("/login", methods=["POST","GET"])
def login():
    if request.method == "POST":
        user = request.form["nm"]
        session["user"] = user  # 只需新增這行
        return redirect(url_for("user")) # 不需要傳遞user值
    else:
        return render_template("login.html")
```
session的型態是字典
然後，將資料傳給user頁面。
由於有了session，需要修改原來的 return redirect(url_for(“user”,usr=user))，改為只要轉址到user頁面，而不需要再透過原來的方式傳遞user值。

修改user函式
在user函式中取得傳來的session資料：
首先我們先判斷是否從session中取得user這個key，如果已經取得到了再把user這個key的value取出來放到user變數中，最後將值回傳。反之，若沒有從session中取得user這個key，就表示使用者尚未登入，我們可以將使用者導引至login的頁面。因此我們將user函式改為：
```python
@app.route("/user")
def user():
    if "user" in session:
        user = session["user"]
        return render_template("user.html",user=user)
    else:
        return redirect(url_for("login"))
```

設定secret_key
```python
app.secret_key = "#230dec61-fee8-4ef2-a791-36f9e680c9fc"
```
最後
```python
from flask import Flask, redirect, url_for, render_template, request, session
app = Flask(__name__)
app.secret_key = "#230dec61-fee8-4ef2-a791-36f9e680c9fc"
#login
@app.route("/login", methods=["POST","GET"])
def login():
    if request.method == "POST":
        user = request.form["nm"]
        session["user"] = user
        return redirect(url_for("user"))
    else:
        return render_template("login.html")

@app.route("/user")
def user():
    if "user" in session:
        user = session["user"]
        return render_template("user.html",user=user)
    else:
        return redirect(url_for("login"))
if __name__ =="__main__":
app.run(debug=True)
```

# Logout
目前為止，想刪除session內的資料，必須手動關閉瀏覽器。若要在不關閉瀏覽器的狀態下就刪除session的話該如何處理？
使用「登出」的方式來達成。
首先建立一個logout的路由，再透過session.pop()刪除資料
當程式清除session紀錄之後，隨即讓頁面導向login頁。
```python
@app.route("/logout")
def logout():
    session.pop("user", None) # 將session裡面記錄的user清除
    return redirect(url_for("login"))
```
```python

```
```python

```
```python

```
```python

```
```python

```
```python

```
