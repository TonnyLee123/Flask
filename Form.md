# POST form
### 補充html 表單

## 1. 建立HTML樣板
### 1.1 login.html
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
		    <input id="username" class="form-control" type="text" name="nm" /> <!--name 是為了讓.py抓取這表單輸入的值-->
		  </div>
		  <div class="form-group">
		    <button type="submit" class="btn btn-primary">Submit</button>
		  </div>
		</form>
	</body>
</html>
```
### 1.2 user.html
```html
<html>
	<head>
		<title> 網頁標題 </title>
		<meta charset="utf-8">
	</head>
	<body>
		<h1> 你好 {{ user }} </h1>
	</body>
</html>
```
### 1.3 login.html output

![image](https://user-images.githubusercontent.com/90739897/159725435-4e2a3878-5cf2-4057-88f6-7b201cb57d48.png)

## 2. 建立 routes 並 傳遞表單資料到 user page
- 取得使用者在表單上輸入的資料
- request.form["inuput.name"]

request.form[“nm”]。


，並且指定給變數user後
```python
from flask import Flask, redirect, url_for, render_template,request
app = Flask(__name__)

#login page
@app.route("/", methods=["POST","GET"]) # 使用POST與GET兩種HTTP存取方式
def login():
    if request.method == "POST":  # 當發送POST請求時(按下submit)
        user = request.form["nm"] # 抓取表單中 name 為 "nm" 的值(輸入在form的值) assign 給 user
        return redirect(url_for("user",user=user)) # 導向user頁面，並將 user 的值傳入該頁面
    else:
        return render_template("login.html") # 若沒有填入資料，按下Submit，則回到 login.html 樣版

#user page
@app.route("/<user>")
def user(user):
    return render_template("user.html", user=user)
    
if __name__ =="__main__":
    app.run(debug=True)
```
![0111](https://user-images.githubusercontent.com/90739897/159751165-704b6201-d2e8-4f01-ab03-c031d6fed819.png)
![image](https://user-images.githubusercontent.com/90739897/159751492-dd3ad530-3ac3-4ef5-9fb8-33386dc21958.png)

-----------------------------------------------------------------------------------------------------
### 2.1 問題點
無法在其他頁面保持登入狀態。

## 解決方式：sessions
- 當登入網站時，session可以**紀錄登入的帳號資訊**，讓在之後瀏覽這網站其他頁面時，可繼續使用這個session資訊作為判斷。
- 登出或關閉網站時，session儲存的資訊也會跟著消失
- 不像cookie，session的資訊是儲存在伺服器端，相對較安全。
- 待補充更多session資料

### 匯入session套件
```python
from flask import session
```
### 3.1 設定secret_key
```python
app.secret_key = "#230dec61-fee8-4ef2-a791-36f9e680c9fc"
```

### 3.2 修改login函式
```python
#login
@app.route("/", methods=["POST","GET"])
def login():
    if request.method == "POST":
        user = request.form["nm"]
        session["USER"] = user
        return redirect(url_for("user"))
    else:
   	# 可避免已登入的用戶透過在URL bar 輸入 /login 回到 login_page
        if "USER" in session:                 # 如果session內已經有user資料(表示已經登入)
            return redirect(url_for("user"))  # 則回到user頁面
      
        return render_template("login.html")  # 如果還沒登入則回到login_page(最一開始)
```

### 3.3 修改user函式
在user函式中取得傳來的session資料：
反之，若沒有從session中取得user這個key，就表示使用者尚未登入，我們可以將使用者導引至login的頁面。因此我們將user函式改為：
```python
@app.route("/user")
def user():
    if "USER" in session:      # 如果"user"(Key)存在
        user = session["USER"] #用來接收 session 內的資料
        return render_template("user.html", user=user)
    else:
        return redirect(url_for("login"))
```

### 結合後
```python
from flask import Flask, redirect, url_for, render_template, request, session

app = Flask(__name__)
app.secret_key = "#230dec61-fee8-4ef2-a791-36f9e680c9fc"

#login
@app.route("/", methods=["POST","GET"])
def login():
    if request.method == "POST":
        user = request.form["nm"]
        session["USER"] = user
        return redirect(url_for("user"))
    else:
        return render_template("login.html")

@app.route("/user")
def user():
    if "USER" in session:
        user = session["USER"]
        return render_template("user.html",user=user)
    else:
        return redirect(url_for("login"))
	
if __name__ =="__main__":
    app.run(debug=True)
```

# Logout
目前為止，若要刪除session內的資料，必須手動關閉瀏覽器。若要在不關閉瀏覽器的狀態下就刪除session的話該如何處理？
使用「登出」的方式來達成。
### 3.4 建立一個logout的路由，再透過session.pop()刪除資料
當程式清除session紀錄之後，隨即讓頁面導向login頁。
```python
@app.route("/logout")
def logout():
    session.pop("USER", None) # 將session裡面記錄的user清除(即登出)
    return redirect(url_for("login")) # 回到登入畫面
```
為了配合logout的使用，也需要修改login的部分，讓已經有session紀錄（session[“user”]）的使用者，從login的路由，直接被導向user頁面。
```python

```
### 總結3 
```python
from flask import Flask, redirect, url_for, render_template, request, session
app = Flask(__name__)
app.secret_key = "#230dec61-fee8-4ef2-a791-36f9e680c9fc"

#login
@app.route("/", methods=["POST","GET"])
def login():
    if request.method == "POST":
        user = request.form["nm"]
        session["USER"] = user
        return redirect(url_for("user"))
    else:
        # 如果session內已經有資料(即已登入)，又試著到login_page
        if "USER" in session:
            return redirect(url_for("user")) # 則到user_page  

        return render_template("login.html")

@app.route("/user")
def user():
    if "USER" in session:
        user = session["USER"]
        return render_template("user.html",user=user)
    else:
        return redirect(url_for("login"))

#logout  
    
@app.route("/logout")
def logout():
    session.pop("USER", None)
    return redirect(url_for("login")) 
    
if __name__ =="__main__":
    app.run(debug=True)
```

## 讓session儲存的資料持續一段時間
- 目前使用的session，只要關閉瀏覽器，session內的資料就會被刪除。
- 不因為瀏覽器的關閉而消失並且效力可以持續一段特定的時間，該如何達成這個效果？
- 使用 timedelta (待補充)
	- 可以計算時間，在這裡可以替session設定資料的保存期限。
	- 可以以「minutes」或 「days」為單位來設定	 	
### import timedelta套件
```python
from datetime import timedelta
```
### 設定session的保存期限
```python
app.permanent_session_lifetime = timedelta(minutes=5)
```

### 接著，在login函式加入此行：
```python
session.permanent = True
```
login總結
```python
#login
@app.route("/login", methods=["POST","GET"])
def login():
    if request.method == "POST":
        session.permanent = True # 加入此行，告訴網頁允許設session期限
        user = request.form["nm"]
        session["user"] = user
        return redirect(url_for("user"))
    else:
        if "user" in session:
            return redirect(url_for("user"))
        return render_template("login.html")
```
你可以先登入您的姓名後關閉瀏覽器。接著，再打開一次瀏覽器瀏覽login頁面，看看是否仍然是已經登入的狀態。如果是，等待五分鐘看看結果如何？
