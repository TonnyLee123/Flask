
# 前言
若直接將使用者密碼以「明碼」的形式存在資料庫中，若網站被駭，密碼就會被洩漏出去。因此基於安全性理由，要透過某種方式將密碼加密後才存入資料庫中。

## 一. Hash function
雜湊演算法所計算出來的 Hash Value 具有**不可逆**的性質，也就是說無法逆向演算回原本的數值，因此可有效的保護密碼。
# Becrypt 
## 一. Bcrypt的安裝
```
pip install flask-bcrypt
```

## 二. Bcrypt的使用
### 1. 引入及建立Bcrypt_instance
```python
# app.py
from flask_bcrypt import Bcrypt
bcrypt = Bcrypt()
```
### 2. 假設一組密碼
```python
pd = 'thispassword'
```
### 3. generate_password_hash(password)
加密密碼
```python
hashed_password = bcrypt.generate_password_hash(password=pd)
print(hashed_password)
```
### 4. check_password_hash(雜湊後的pd, 目前輸入的pd )
檢查所輸入的密碼是否與db中的密碼相同
```python
# 輸入正確密碼： return true
check_password = bcrypt.check_password_hash(hashed_password,'thispassword')

# 輸入錯誤密碼： return false 
check_password = bcrypt.check_password_hash(hashed_password, ‘incorrectpassword ')
```
