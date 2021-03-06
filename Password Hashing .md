# 前言
若直接將使用者密碼以「明碼」的形式存在資料庫中，若網站被駭，密碼就會被洩漏出去。因此基於安全性理由，要透過某種方式將密碼加密後才存入資料庫中。

# 一. Hash function
雜湊演算法所計算出來的 Hash Value 具有**不可逆**的性質，也就是說無法逆向演算回原本的數值，因此可有效的保護密碼。

# 二. Becrypt 
### 1. 安裝 flask-bcrypt
```
pip install flask-bcrypt
```

### 2. 引入及建立Bcrypt_instance
```python
# app.py
from flask_bcrypt import Bcrypt
bcrypt = Bcrypt()
```
### 3. 假設一組密碼
```python
pd = 'thispassword'
```
### 4. generate_password_hash(password)
- 加密密碼
- 每次都產生新的加密後的密碼
- 結果的資料型態是bytes (b'')
- 「b」表示「bytes」，表示內容是bytes，而非字串（str）
```python
hashed_password = bcrypt.generate_password_hash(password=pd)
print(hashed_password)
```
![image](https://user-images.githubusercontent.com/90739897/164190861-782aac06-8fbc-468d-b157-9981b946ce48.png)
```python
# 把b去掉
# decode('utf-8')
hashed_password = bcrypt.generate_password_hash(password=pd).decode('utf-8')
```
![image](https://user-images.githubusercontent.com/90739897/164191020-fe87cefe-5ab0-4b88-a7d3-73afbd43fe04.png)

### 5. check_password_hash(雜湊後的pd, 目前輸入的pd )
檢查所輸入的密碼是否與db中的密碼相同
```python
# 輸入正確密碼： return true
check_password = bcrypt.check_password_hash(hashed_password,'thispassword')

# 輸入錯誤密碼： return false 
check_password = bcrypt.check_password_hash(hashed_password, ‘incorrectpassword ')
```
