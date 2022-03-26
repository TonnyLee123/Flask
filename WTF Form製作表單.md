先前使用 request.form 獲得使用者在表單上輸入的資料。
# WTForms
- Python的library
- 提供**表單呈現**及**驗證**等功能。
- https://wtforms.readthedocs.io/en/2.3.x/
## 核心概念
Forms 是 WTForms 最主要的核心容器。
Forms是Fields欄位的集合，可以選擇透過字典（dictionary）或屬性（attribute）的方式來接觸它們。
每個Fields都代表某一種資料類型，且Fields欄位限制使用者僅能夠輸入符合該資料類型的數據。
舉例 IntegerField 與 StringField 代表的是兩種不一樣的資料型態，一個是數字而另一個則是字串。
此外，FieldS 其他屬性，例如label（標籤），description（描述）和錯誤驗證等。
每個Fields欄位都有一個widget實例。widget的工作是呈現該Fields欄位對應的HTML標籤。你可以指定Widget實例給每個特定的Fields欄位，不過在預設的情況下每個Fields欄位都有一個widget實例。
有一些Fields欄位設置的目的只是為了工程師使用上方便，舉例來說TextAreaField只是一個字串欄位（StringField），而預設的widget是TextArea。
為了提供各種驗證規則，Fields欄位包含了一系列的驗證方式。
- StringField
- BooleanField (可複選吧?)
- <img src = 'https://i.stack.imgur.com/YGNnE.png' width = 200>
- RadioField (單選)
- DateTimeField
- SelectField
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

# 建立表單
建一個類別, 其繼承的是FlaskForm，而前面的wtforms繼承的是Form。
建立了RegForm的類別，透過這個類別來讓使用者登入。

# WTF Form 實作
## 匯入套件
```python
from flask import Flask, render_template, session, redirect, url_for

# 本次學習的套件
from flask_wtf import FlaskForm 

from wtforms import (StringField, BooleanField, DateTimeField,
                     RadioField, SelectField, TextField,
                     TextAreaField, SubmitField)                     
from wtforms.validators import DataRequired
```

## 補充secret key功能

```python
app = Flask(__name__)
app.config['SECRET_KEY']='mykey' # 加上SECRET KEY的設定
```
## 設定FormField
設定一個表單欄位的Class，取名為MyForm，這個Class係繼承FlaskForm的類別。
```python
class MyForm(FlaskForm):
    name = StringField('你的名字', validators=[DataRequired()]) # 你的名字是 label
    agreed = BooleanField('同意加入這個組織？')
    gender = RadioField('請輸入性別', choices=[('M','男生'),('F','女生')])
    hobby = SelectField('你的興趣', choices=[('sports','運動'),('travel','旅遊'),('movie','電影')])
    others= TextAreaField()
    submit = SubmitField("確認")
```
validators屬性。屬性中放置DataRequired，代表此欄位為必填欄位，在確認時會檢查使用者是否確實輸入這個欄位。

## 設定路由
兩個頁面，首頁接收表單資訊，thankyou頁面顯示使用者輸入的資料
```python
＃建立首頁
# 在index函式中，透過session的方式將表單個欄位的資料儲存起來。並導向至 thankyou 的頁面。
@app.route('/',methods=['GET','POST'])
def index():
    form = MyForm() # 建立instance
    if form.validate_on_submit():
        session['name'] = form.name.data  # 把在表單輸入的資料存到session
        session['agreed'] = form.agreed.data
        session['gender'] = form.gender.data
        session['hobby'] = form.hobby.data
        session['others'] = form.others.data
        return redirect(url_for('thankyou'))
    return render_template('home.html', form=form)
    
# thank you頁面
@app.route('/thankyou')
def thankyou():
    return render_template('thankyou.html') 
```
validate_on_submit()
- 當表單被submit時，才會啟動 validate() 功能。validate功能是form.is_submitted()與form.validate()的縮寫。
- Check if it is a POST request and if it is valid.
- 
## 樣板製作
home.html
.label 欄位名稱
.neme 框框
```html
{% block content %}
<div class="container">
<form method="POST"> <!-- form 表單 -->
  {{form.hidden_tag()}}
  {{form.name.label(class='form-label')}} 
  {{form.name(class='form-control')}} 
  <br>
  {{form.agreed.label(class='form-label')}} 
  {{form.agreed(class='form-control')}} 
  <br>
  {{form.gender.label(class='form-label')}} 
  {{form.gender()}}
  <br>
  {{form.hobby.label(class='form-label')}} 
  {{form.hobby(class='form-control')}}
  <br>
  {{form.others.label(class='form-label')}} 
  {{form.others(class='form-control')}}
  <br>
  {{form.submit(class='btn btn-primary')}}
</form>
</div>
{% endblock %}
```
如果需要調整CSS的話，只要在括弧裡面加上CSS的Class
把存在session裡面的資料顯示出來。
```html
{% block content %}
<div class="container">
<p>感謝填寫，以下是您提供的訊息</p>
<ul>
<li>名字: {{session['name']}}</li>
<li>同意加入這個組織: {{session['agreed']}}</li>
<li>性別: {{session['gender']}}</li>
<li>興趣: {{session['hobby']}}</li>
<li>其他: {{session['others']}}</li>
</ul>
</div>
{% endblock %}
```
https://medium.com/seaniap/python-web-flask-%E7%94%A8wtf-form%E8%A3%BD%E4%BD%9C%E8%A1%A8%E5%96%AE-1f4af213ea88
