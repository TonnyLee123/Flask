先前使用 request.form 獲得使用者在表單上輸入的資料。
補充 flash
# [WTForms](https://wtforms.readthedocs.io/en/2.3.x/)
- Python的library
- 提供**表單呈現**及**驗證**等功能。
## 核心概念
Forms 是 Fields的集合，可以選擇透過字典（dictionary）或屬性（attribute）的方式來接觸它們。
每個Fields都代表某一種資料類型，且Fields欄位限制使用者僅能夠輸入符合該資料類型的數據。
舉例 IntegerField 與 StringField 代表的是兩種不一樣的資料型態
此外，FieldS 其他屬性，例如label（標籤），description（描述）和錯誤驗證等。
每個Fields欄位都有一個widget實例。widget的工作是呈現該Fields欄位對應的HTML標籤。你可以指定Widget實例給每個特定的Fields欄位，不過在預設的情況下每個Fields欄位都有一個widget實例。
有一些Fields欄位設置的目的只是為了工程師使用上方便，舉例來說TextAreaField只是一個字串欄位（StringField），而預設的widget是TextArea。
為了提供各種驗證規則，Fields欄位包含了一系列的驗證方式。
- StringField
- BooleanField (可複選吧?)
- <img src = 'https://i.stack.imgur.com/YGNnE.png' width = 200>
- RadioField (單選)
- DateTimeField
- SelectField(label, choices=[(value, label),(),(),...], coerce=unicode, option_widget=None, validate_choice=True)
Select fields with static choice values:
- ![img](https://i.stack.imgur.com/lFiU0.png)
- TextField
- TextAreaField
- ![img](https://i.stack.imgur.com/JJ5Kw.png)
- SubmitField
## 安裝
```python
pip install WTForms
```

# Flask-WTF
- Python的函式庫
- 將 Flask 應用程式與 WTForms 整合。
- Flask-WTF這個套件包括檔案上傳、CSRF以及reCAPTCHA驗證等，解決了WTForms無法上傳檔案的問題。

## 安裝
```python
pip install Flask-WTF
```
# 新增一個form.py
- 裡面包含各種class(form)
- 
# 建立表單
建一個類別, 其繼承的是FlaskForm，而前面的wtforms繼承的是Form。
建立了RegForm的類別，透過這個類別來讓使用者登入。

# WTF Form 實作
## 匯入套件
```python
from flask import Flask, render_template, session, redirect, url_for

# 本次學習的套件
from flask_wtf import FlaskForm 

from wtforms import (StringField, BooleanField, RadioField, SelectField, TextField, TextAreaField, SubmitField)                     
from wtforms.validators import DataRequired
```


```python
app = Flask(__name__)
app.config['SECRET_KEY']='mykey' # 加上SECRET KEY的設定
```
## 設定FormField
透過Class 定義 Form, 每個 Field 的種類, 限制等等。
- validators attribute
    - DataRequired: 此欄位為必填欄位，在確認時會檢查使用者是否確實輸入這個欄位。
```python
class MyForm(FlaskForm): # 取名MyForm，繼承FlaskForm類別。
    name = StringField('名字', validators=[DataRequired()]) 
    gender = RadioField('性別', choices=[("man", '男生'), ("girl", '女生')])
    hobby = SelectField('興趣', choices=[('sports','運動'),('travel','旅遊'),('movie','電影')])
    agreed = BooleanField('加入組織？')
    submit = SubmitField("確認")
```
![formexp](https://user-images.githubusercontent.com/90739897/160233142-71d4ff4f-ca6a-4ab1-9edc-3713eb2aa83b.png)
## 設定 secret key
補充 為甚麼要設?
```python
app.config['SECRET_KEY']='mykey'
```

## 創建 MyForm 實例
補充 為甚麼在很多地方都會看到要創建實例?
- 才能使用class內的東西(變數, function, 等等)
```python
form = MyForm()
```
## 將 form 傳入 html
```python
return render_template("login.html", form = form)
```
## 1. 範例一 單純顯示form在網頁上(簡易版)
```python
# app.py
from flask import Flask, render_template
from form import MyForm
 
app = Flask(__name__) 

app.config['SECRET_KEY']='mykey'

@app.route("/")
def hello_world():
    form = MyForm()
    return render_template("login.html", form = form)
```
## 2. 範例二: 在home_page中，透過 session 方式 將user在表單填入的資料儲存，並導向至 result_page.
### 新建兩個route:
- 1. home: 讓使用者輸入表單
- 2. result: 顯示使用者輸入的資料
```python
from flask import Flask, render_template, session, redirect, url_for
from form import MyForm
from flask_wtf import FlaskForm 
from wtforms import (StringField, BooleanField, DateTimeField,
                     RadioField, SelectField, TextField,
                     TextAreaField, SubmitField)                     
from wtforms.validators import DataRequired

app = Flask(__name__)
app.config['SECRET_KEY']='mykey'

# home
@app.route('/',methods=['GET','POST'])
def home():
    form = MyForm()
    if form.validate_on_submit():
        session['name'] = form.name.data  # 把在表單輸入的資料存到session
        session['agreed'] = form.agreed.data
        session['gender'] = form.gender.data
        session['hobby'] = form.hobby.data

        return redirect(url_for('result'))
    # 如果沒有提交表單:   
    return render_template('home.html', form = form)
    
# result 
@app.route('/result')
def result():
    return render_template('result.html') 
```
validate_on_submit()
- 當 form submit 時，才會啟動 validate()。
- validate功能:
    - Check if it is a POST request and if it is valid.
## Template
### 1. home.html
```html
<html>
	<head>
		<title> 網頁標題 </title>
		<meta charset="utf-8">
	</head>

	<body>
		<form method="POST">
			{{form.hidden_tag()}}
  			{{form.name.label(class='form-label')}} 
  			{{form.name(class='form-control')}}  
  			<br>
			{{form.gender.label(class='form-label')}} 
  			{{form.gender()}}
  			<br>
  			{{form.hobby.label(class='form-label')}} 
  			{{form.hobby(class='form-control')}}
  			<br>
  			{{form.agreed.label(class='form-label')}} 
  			{{form.agreed(class='form-control')}} 
  			<br>
  			{{form.submit(class='btn btn-primary')}}
		</form>
	</body>
</html>
```
- 新增CSS: 在括弧裡加CSS的Class
```
{{form.name.label(class='form-label')}} 
```
### 2. result.html
```html
<html>
	<head>
		<title> Result </title>
		<meta charset="utf-8">
	</head>
	<body>
		<div>
			<p>以下為您填入的資料</p>
			<ul>
				<li>名字: {{session['name']}}</li>
				<li>性別: {{session['gender']}}</li>
				<li>興趣: {{session['hobby']}}</li>
				<li>是否加入: {{session['agreed']}}</li>
			</ul>
		</div>
	</body>
</html>
```
- 把存在 session 裡的資料顯示出來。
```
{{session['name']}}
```
### 示意圖
![form02](https://user-images.githubusercontent.com/90739897/160236482-e81ad795-62d8-4334-88a6-db3ed0c76b46.png)

![image](https://user-images.githubusercontent.com/90739897/160236470-9e19350f-be4f-4407-8950-1ae9d515a222.png)
