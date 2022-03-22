```python
from flaskblog.models import User, Post    
# @website.route()允許我們寫入一些函式傳回網頁內容到指定的route(頁面)，如'/'表首頁
# decorators(@) 新增一些functions(index(), about(), ...)到 route()
# 建立首頁面
from flaskblog import web, db
# render_template(function, 用來引入html)
# url_for(function, find the exact location of routes, 引入css)
# flash (MESSAGE) is an easy way to send a one-time alert
# redirect # 當填完表單後到其他頁面
from flask import render_template, url_for, flash, redirect

# 把自己寫的 form 從 flaskblog(package) import 進來
from flaskblog.form import RegistrationForm, LoginForm, UpdateAccountForm

from flask_login import login_user, logout_user, current_user, login_required # (function*2) ( class 解決登入後還顯示登入按鈕) (必須登入才能..如留言等等)

# 用來為照片名稱取亂碼 
import secrets
import os # 抓file extension

@web.route("/")
# 建立網站回應內容
def index():    
    # 直接寫入html 
    return render_template('home.html', Posts = posts)

# 建立about頁面
@web.route("/about") 
def about():  
    # 引入html檔(與此py檔放在同個folder)
    return render_template('about.html', title = 'About')

# 建立註冊頁面
@web.route("/register", methods = ['GET', 'POST'])  # allow submit get, post(submit form) request
def register():
    # 如果已經登入， 擇點選登入鍵，會回到首頁
    if current_user.is_authenticated:
        return redirect(url_for('index'))
    form = RegistrationForm()  # 建立instance
    if form.validate_on_submit(): # 當提交表單, 並確認填入的資訊有沒有符合標準(true or false)
        hashed_pw = bcrypt.generate_password_hash('form.password.data').decode('utf-8') # 加密密碼
        # 把資料存到資料庫
        user = User(username=form.username.data, email=form.email.data, password = hashed_pw) # 建立要存放到資料庫的資料
        db.session.add(user) # 把資料加入到db
        db.session.commit() # 儲存資料到db
        flash("Your account has been created! you are now able to login in", "success") # 當填入的資訊符合標準則跳出訊息通知
        return redirect(url_for('login')) # 並且回到登入頁面 # success 是用來指定class
    return render_template('register.html', title = 'Register', form = form)

# 建立登入頁面
@web.route("/login", methods = ['GET', 'POST'])
def login():
    if current_user.is_authenticated:
        return redirect(url_for('index'))
    form = LoginForm() # 建立instance
    if form.validate_on_submit(): # 當提交表單, 並確認填入的資訊有沒有符合標準(true or false)
        # 透過email登入，所以要先確認此email是否存在在db
        user = User.query.filter_by(email = form.email.data).first() # 沒有就是none
        # 確認此用戶是否存在; 輸入的密碼是否和資料庫的密碼相同
        if user and bcrypt.check_password_hash(user.password, form.password.data):
                                             #  資料庫中的密碼,  輸入的密碼 
            login_user(user, remember = form.remember.data) # 登入帳號           
            return redirect(url_for('index'))
        else:
            flash('Login Unsuccessful. Please check email and password', 'danger')
    return render_template('login.html', title = 'Login', form = form)

# 建立登出頁面
@web.route("/logout")
def logout():
    logout_user() # 登出
    return redirect(url_for('index'))

# 處存照片到電腦    
def save_picture(form_picture):
    random_hex = secrets.token_hex(8) # 產生亂碼當新檔名
    _, f_ext = os.path.splitext(form_picture.filename) # test.jpg -> test, .jpg
    picture_fn = random_hex + f_ext
    # 儲存路徑                  (app的跟目錄 + 要存的地方 + 檔名)
    picture_path = os.path.join(app.root_path, 'static/profile_pics', picture_fn)

    # 減少儲存空間
    # resize 照片 open img in new tap 才看的出來變小張
    # pip install Pillow, from PIL import Image (class)
    output_size = (125, 125)
    i = Image.open(form_picture) # 打開照片
    i.thumbnail(output_size) # resize 照片
    i.save(picture_path) # 處存照片

    return picture_fn
    
# 建立個人檔案頁面
@web.route("/account", methods = ['GET', 'POST'])
@login_required     # 必須登入才能到此頁面 還需要告訴從哪登入
def account():
    form = UpdateAccountForm()
    #
    if form.validate_on_submit(): # 當按下submit且驗證成功(無同樣使用者等等)時
        if form.picture.data: # 如果有上傳要更新的照片
            picture_file = save_picture(form.picture.data)
            current_user.image_file = picture_file
        current_user.username = form.username.data # 更改名稱
        current_user.email = form.email.data # 改email
        db.session.commit() # 提交更改到db
        flash('Your account has been updated!', 'success')
        return redirect(url_for('account'))

    elif request.method == 'GET':
        # 顯示現在的用戶及email
        form.username.data = current_user.username
        form.email.data = current_user.email
    image_file = url_for('static', filename = 'profile_pics/' + current_user.img_file)
    return render_template('account.html', title = 'Account', img_file = image_file, form = form)
from flaskblog.models import User, Post    
# @website.route()允許我們寫入一些函式傳回網頁內容到指定的route(頁面)，如'/'表首頁
# decorators(@) 新增一些functions(index(), about(), ...)到 route()
# 建立首頁面
from flaskblog import web, db
# render_template(function, 用來引入html)
# url_for(function, find the exact location of routes, 引入css)
# flash (MESSAGE) is an easy way to send a one-time alert
# redirect # 當填完表單後到其他頁面
from flask import render_template, url_for, flash, redirect

# 把自己寫的 form 從 flaskblog(package) import 進來
from flaskblog.form import RegistrationForm, LoginForm, UpdateAccountForm

from flask_login import login_user, logout_user, current_user, login_required # (function*2) ( class 解決登入後還顯示登入按鈕) (必須登入才能..如留言等等)

# 用來為照片名稱取亂碼 
import secrets
import os # 抓file extension

@web.route("/")
# 建立網站回應內容
def index():    
    # 直接寫入html 
    return render_template('home.html', Posts = posts)

# 建立about頁面
@web.route("/about") 
def about():  
    # 引入html檔(與此py檔放在同個folder)
    return render_template('about.html', title = 'About')

# 建立註冊頁面
@web.route("/register", methods = ['GET', 'POST'])  # allow submit get, post(submit form) request
def register():
    # 如果已經登入， 擇點選登入鍵，會回到首頁
    if current_user.is_authenticated:
        return redirect(url_for('index'))
    form = RegistrationForm()  # 建立instance
    if form.validate_on_submit(): # 當提交表單, 並確認填入的資訊有沒有符合標準(true or false)
        hashed_pw = bcrypt.generate_password_hash('form.password.data').decode('utf-8') # 加密密碼
        # 把資料存到資料庫
        user = User(username=form.username.data, email=form.email.data, password = hashed_pw) # 建立要存放到資料庫的資料
        db.session.add(user) # 把資料加入到db
        db.session.commit() # 儲存資料到db
        flash("Your account has been created! you are now able to login in", "success") # 當填入的資訊符合標準則跳出訊息通知
        return redirect(url_for('login')) # 並且回到登入頁面 # success 是用來指定class
    return render_template('register.html', title = 'Register', form = form)

# 建立登入頁面
@web.route("/login", methods = ['GET', 'POST'])
def login():
    if current_user.is_authenticated:
        return redirect(url_for('index'))
    form = LoginForm() # 建立instance
    if form.validate_on_submit(): # 當提交表單, 並確認填入的資訊有沒有符合標準(true or false)
        # 透過email登入，所以要先確認此email是否存在在db
        user = User.query.filter_by(email = form.email.data).first() # 沒有就是none
        # 確認此用戶是否存在; 輸入的密碼是否和資料庫的密碼相同
        if user and bcrypt.check_password_hash(user.password, form.password.data):
                                             #  資料庫中的密碼,  輸入的密碼 
            login_user(user, remember = form.remember.data) # 登入帳號           
            return redirect(url_for('index'))
        else:
            flash('Login Unsuccessful. Please check email and password', 'danger')
    return render_template('login.html', title = 'Login', form = form)

# 建立登出頁面
@web.route("/logout")
def logout():
    logout_user() # 登出
    return redirect(url_for('index'))

# 處存照片到電腦    
def save_picture(form_picture):
    random_hex = secrets.token_hex(8) # 產生亂碼當新檔名
    _, f_ext = os.path.splitext(form_picture.filename) # test.jpg -> test, .jpg
    picture_fn = random_hex + f_ext
    # 儲存路徑                  (app的跟目錄 + 要存的地方 + 檔名)
    picture_path = os.path.join(app.root_path, 'static/profile_pics', picture_fn)

    # 減少儲存空間
    # resize 照片 open img in new tap 才看的出來變小張
    # pip install Pillow, from PIL import Image (class)
    output_size = (125, 125)
    i = Image.open(form_picture) # 打開照片
    i.thumbnail(output_size) # resize 照片
    i.save(picture_path) # 處存照片

    return picture_fn
    
# 建立個人檔案頁面
@web.route("/account", methods = ['GET', 'POST'])
@login_required     # 必須登入才能到此頁面 還需要告訴從哪登入
def account():
    form = UpdateAccountForm()
    #
    if form.validate_on_submit(): # 當按下submit且驗證成功(無同樣使用者等等)時
        if form.picture.data: # 如果有上傳要更新的照片
            picture_file = save_picture(form.picture.data)
            current_user.image_file = picture_file
        current_user.username = form.username.data # 更改名稱
        current_user.email = form.email.data # 改email
        db.session.commit() # 提交更改到db
        flash('Your account has been updated!', 'success')
        return redirect(url_for('account'))

    elif request.method == 'GET':
        # 顯示現在的用戶及email
        form.username.data = current_user.username
        form.email.data = current_user.email
    image_file = url_for('static', filename = 'profile_pics/' + current_user.img_file)
    return render_template('account.html', title = 'Account', img_file = image_file, form = form)
```
