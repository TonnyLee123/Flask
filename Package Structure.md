# 前言
隨著應用程式的開發規模越來越大，不可能把所有的程式碼都寫在同一份Python檔案中，因此可以透過模組化來使專案更加方便管理。 主程式再透過引用的方式來使用
# 一. Mdule(模組)
- 一個**py檔**
- 包含**相關性較高**的code
- **Reusable(重用性)**
    - 讓code能夠透過import的方式來重複使用 
## 範例 開發部落格，主程式為 app.py 
### 1. 模組化前:
全部的functions都方在同個py檔
```python
# app.py
def get_author():
    return "Mike"
def get_email():
    return "learncodewithmike@gmail.com"
def add_post(title):
    pass
def delete_post(title):
    pass
```
### 2. 模組化後:
把相關function放在同個py檔
#### 模組一 (about.py)
###
```python
def get_author():
    return "Mike"
def get_email():
    return "learncodewithmike@gmail.com"
```
#### 模組二 (post.py)
```python
def add_post(title):
    pass
def delete_post(title):
    pass
```
## Module 也可包含 Class
```python
class Post:
    def __init__(self):
        self.titles = []
    def add_post(self, title):
        self.titles.append(title)
    def delete_post(self, title):
        self.titles.remove(title)
```

# 二、Import Module
## 1. 引用模組中的特定物件
```
# app.py
from post import Post # 在 module import Post這個class
from about import get_author, get_email # 在 module import function
p = Post()
p.add_post("Python Programming")
author = get_author()
email = get_email()
print(p.titles)  #執行結果：['Python Programming']
print(author)  #執行結果：Mike
print(email)  #執行結果：learncodewithmike@gmail.com
from 之後加上模組(Module)的檔名，注意沒有 .py 副檔名，接著 import 引用所需的物件。
```
在 import 之後可以使用 * 來引用模組中的所有物件，但是這樣的寫法，可能會在引用的過程中，發生同名方法覆寫(Method Overriding)的風險，所以建議引用所需要的物件即可。另一種語法則是透過 import 語法，如下範例：
## 2. 引用整個 module
```python
import post
import about
p = post.Post()
p.add_post("Python Programming")
author = about.get_author()
email = about.get_email()
print(p.titles)  #執行結果：['Python Programming']
print(author)  #執行結果：Mike
print(email)  #執行結果：learncodewithmike@gmail.com
```
import Module_name
- 在主程式中須透過 Module_name 來存取其中的成員。

在主程式 app.py 中引用模組(Module)，並且執行後，會發現多了一個 pycache 資料夾，如下圖：
![image](https://user-images.githubusercontent.com/90739897/160456500-eaf0a241-d45e-431f-8499-1736ae28eec7.png)

這個資料夾中，可以看到包含了引用模組的已編譯檔案，當下一次執行主程式 app.py 時，Python編譯器看到已編譯的模組檔案，會直接載入該模組(Module)，而省略編譯的動作，藉此來加速載入模組(Module)的速度。

當然Python編譯器在每一次執行時，會檢查來源模組及已編譯檔案的時間，當來源模組的時間較新，則代表該模組(Module)有經過修改，則Python編譯器會再編譯一次，更新已編譯檔案。

各位有沒有覺得奇怪，那為什麼沒有 app.py 的已編譯檔案，因為在此範例中，我們將 app.py 當作程式的進入點，所以每一次執行python app.py 指令時，Python編譯器都要進行編譯，所以沒有將 app.py 進行快取的動作。
# 三、Package套件(套件)
- 一個 folider
- 包含一個或多個 Module
- 擁有__init__.py
    - 可以撰寫Package初始化的程式碼。

我們將程式碼模組化後，專案中的模組(Module)就會越來越多，這時候就可以再將相似的模組(Module)組織為套件(Package)

將專案中的 post.py 及 about.py 模組(Module)打包為部落格套件(Package)，首先，建立blog資料夾，接著在資料夾中新增__init__.py檔案，最後將 post.py 及 about.py 模組(Module)移至blog資料夾中，如下範例：
![image](https://user-images.githubusercontent.com/90739897/160456571-b4371669-fade-4bae-b1f3-1bf0ed5f09f2.png)

而在主程式 app.py 中引用套件(Package)的方式和模組(Module)大同小異，我們先來看 from-import語法，如下範例：
```python
# 從套件中引用模組
from blog import post
from blog import about
p = post.Post()
p.add_post("Python Programming")
author = about.get_author()
email = about.get_email()
```
另一個引用套件(Package)的 import 語法如下範例：
# 從套件中引用模組
```
import blog.post
import blog.about
p = blog.post.Post()
p.add_post("Python Programming")
author = blog.about.get_author()
email = blog.about.get_email()
```
# 四、dir()
- Python內建函式
- 顯示 Object 的 Attribute/ Method
- 我們利用此函式(Function)來看一下模組(Module)所擁有的屬性(Attribute)及方法(Method)，如下範例：
#從blog套件引用about模組
from blog import about
print(dir(about))
執行結果

從執行結果可以看到模組(Module)中有自建的get_author及get_email方法(Method)，其餘的則是Python自動幫我們產生的，我們來看幾個常用的屬性(Attribute)，如下範例：
# 從blog套件引用about模組
from blog import about
print(about.__name__)  # 模組名稱
print(about.__package__)  # 套件名稱
print(about.__file__)  # 模組的檔名及路徑
執行結果

# 五、(Executing a Module as a Script
## 範例，在 about Module 中加上以下程式碼，並且執行該Module
```
def get_author():
    return "Mike"
def get_email():
    return "learncodewithmike@gmail.com"
print("about module name: ", __name__) 
# 執行結果：about module name:  __main__
而這時候換成執行 app.py ，__name__屬性(Attribute)則為blog.about，我們就可以利用這個特性，撰寫腳本來彈性的控制當執行模組(Module)的檔案時，要進行哪些行為，而這些行為是在被其他模組(Module)引用時，不會被執行的，如下範例：
def get_author():
    return "Mike"
def get_email():
    return "learncodewithmike@gmail.com"
if __name__ == "__main__":
    print("about module initialized.")
    get_author()
```
範例中將about Module加上了判斷式，當執行about模組(Module)時，__name__屬性(Attribute)為__main__，所以會執行我們設定的任務，而這些任務是在執行主程式 app.py 時，不會被執行的，因為__name__屬性(Attribute)為blog.about。
