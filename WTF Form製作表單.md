先前我們曾經提到過使用request.form的方式獲得使用者在表單上輸入的資料。
# WTForms
- Python的函式庫
- 提供在開發網站時，需要的表單呈現以及表單的驗證等功能。
- https://wtforms.readthedocs.io/en/2.3.x/
- 補充
- 
## 核心概念
Forms表單是WTForms最主要的核心容器。Forms表單是Fields欄位的集合，我們可以選擇透過字典（dictionary）或屬性（attribute）的方式來接觸它們。
Fields則將大部分粗重的工作攬在身上。每個Fields欄位都代表某一種資料的類型，且Fields欄位限制使用者僅能夠輸入符合該資料類型的數據。舉例來說IntegerField與StringField代表的是兩種不一樣的資料型態，一個是數字而另一個則是字串。此外，Fields欄位除包含數據之外，還包含其他有用的屬性，例如label（標籤），description（描述）和錯誤驗證等。
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
## 匯入套件
```python
from wtforms import Form, BooleanField, StringField, validators
```
# Flask-WTF
- Python的函式庫
- 將 Flask 應用程式與 WTForms 整合。
- 由於WTForms不處理檔案的上傳功能：
- Flask-WTF這個套件包括了檔案上傳、CSRF以及reCAPTCHA驗證等，解決了WTForms無檔案上傳功能的問題。

## 安裝
```python
pip install Flask-WTF
```
## 匯入套件
```python
from flask_wtf import FlaskForm
```
除了匯入Flask-WTF外，還要匯入WTForms。因為Flask-WTF的功能是幫助把Flask應用程式與WTForms整合，不過我們只要匯入欄位（例如：StringField, SubmitField），如果需要欄位驗證的話也要匯入validators來驗證欄位。
```python
from wtforms import StringField, SubmitField
from wtforms.validators import DataRequired
```
# 建立表單
建一個類別, 其繼承的是FlaskForm，而前面的wtforms繼承的是Form。
建立了一個名為RegForm的類別，透過這個類別來讓使用者登入。
```python
class RegForm(FlaskForm):
    username  = StringField('username',validators=[DataRequired()])
    submit = SubmitField("Submit")
```
假設有個index首頁，並在該頁中放一個表單，我們可以如下方式建立，其中樣板的名稱為home.html：

```python
# 建立路由
@app.route('/', methods=['GET','POST'])
def index():
    """首頁"""
    username = False
    #form為類別的實體
    form = RegForm()
    if form.validate_on_submit():
        #取出username欄位的輸入值
        username = form.username.data
        #重設username欄位
        form.username.data = ''
    #將username與form帶入首頁home.html樣板中
    return render_template('home.html', form=form,username=username)
```
在home.html樣板中，可以透過下面方式把表單顯示出來：
<form method="POST">
    {{form.hidden_tag()}}
    {{form.username.label}} {{form.username()}}
    {{form.submit()}}
</form>
上面的程式碼中，有一個 validate_on_submit()，意思是當表單被submit時，才會啟動這個 validate()功能。這個validate功能是form.is_submitted()與form.validate()的縮寫。
# WTF Form表單實作

## 匯入套件
```python
from flask import Flask, render_template, session, redirect, url_for
from flask_wtf import FlaskForm
from wtforms import (StringField, BooleanField, DateTimeField,
RadioField, SelectField, TextField,
TextAreaField,SubmitField)
from wtforms.validators import DataRequired
```

## 實體化Flask App
匯入套件後，要實體化Flask，並且加上SECRET KEY的設定，在此我們隨意使用一組SECRET_KEY。

```python
app = Flask(__name__)
app.config['SECRET_KEY']='mykey'
...略...
if __name__ == '__main__':
    app.run(debug=True)
```
## 設定表單欄位
設定一個表單欄位的Class，取名為MyForm，這個Class係繼承FlaskForm的類別。
```python
class MyForm(FlaskForm):
    name = StringField('你的名字', validators=[DataRequired()])
    agreed = BooleanField('同意加入這個組織？')
    gender = RadioField('請輸入性別', choices=[('M','男生'),('F','女生')])
    hobby = SelectField('你的興趣', choices=[('sports','運動'),('travel','旅遊'),('movie','電影')])
    others= TextAreaField()
    submit = SubmitField("確認")
```
上面的程式碼，在MyForm類別中，我們設定了name為字串欄位（StringField），並且這個欄位設定了檢查的validators屬性。在這個屬性中放置了DataRequired，意指此欄位為必填欄位，在確認時會檢查使用者是否確實輸入這個欄位。
agreed為True或False的BooleanField欄位、gender為單選的Radio Button欄位（RadioField）choices為單選欄位的選項、hobby為下拉選單（SelectField），choices為下拉選單的選項、others則是文字區塊欄位。

## 設定路由
設定表單頁面的路由。在這裏我們只要使用兩個頁面，首頁接收表單資訊，thankyou頁面顯示使用者輸入的資料，由於簡化的關係，這裡使用session來儲存輸入的資料。
```python
＃建立首頁
# 在index函式中，透過session的方式將表單個欄位的資料儲存起來。並導向至 thankyou 的頁面。
@app.route('/',methods=['GET','POST'])
def index():
    """首頁"""
    form = MyForm()
    if form.validate_on_submit():
        session['name'] = form.name.data
        session['agreed'] = form.agreed.data
        session['gender'] = form.gender.data
        session['hobby'] = form.hobby.data
        session['others'] = form.others.data
        return redirect(url_for('thankyou'))
    return render_template('home.html', form=form)
    
# thank you頁面
@app.route('/thankyou')
def thankyou():
    """thankyou頁"""
    return render_template('thankyou.html')
   
```
## 樣板製作
最後，需要製作對應的頁面。在templates資料夾裡面，需要建立home.html與thankyou.html兩個樣板。下面是home.html的內容：
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
使用表單非常方便，只要在{{}}裡面加上form.name.label()，就會顯示表單name欄位的label，在裡面加上form.name()，就會顯示name欄位。如果需要調整CSS的話，只要在括弧裡面加上CSS的Class，例如class=’form-label’。
至於填完表單後，路由導向的thankyou.html結果頁面，我們要在這裏把存在session裡面的資料顯示出來。例如{{session[‘name’]}}可以顯示出name的值，{{session[‘agreed’]}}則可以顯示出agreed的值。

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
