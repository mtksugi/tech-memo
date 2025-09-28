---
title: Python + Django (Udemy) メモ
---

# Python + Django Udemy

このメモは[Udemy講座](https://www.udemy.com/share/103OHY/)を受講して学んだ記録です。  
作者には感謝します。


## 環境整備

- Anacondaのインストール
https://www.anaconda.com/products/individual#Downloads

Anaconda：Pythonのローカル開発環境。Pythonで動く機械学習用ライブラリをまとめたもの

- インストール後

環境作成
```bash
conda create -n pythonenv python=3.8
```

環境確認
```bash
conda env list
```

アクティベーション
```bash
conda activate pythonenv
```

djangoインストール
```bash
pip install django
```
※環境削除
```bash
conda remove -n [環境名] --all
```

### VSCodeによる統合環境

vscodeのsettings.jsonに以下を入れる
指定する値はアナコンダの仮想環境のパスで、conda env listで表示されるパス＋python.exe
```json
    "python.pythonpath": "C:\\Users\\sugiura\\.conda\\envs\\pythonenv\\python.exe",
```

sample.pyを作成し、
```python
print('helloworld')
```
として、runするとターミナルで実行できる

VSCodeのターミナルで
```bash
activate pythonenv
django-admin startproject [プロジェクト名]
```
とするとpythonプロジェクトが作成される

さらに
```bash
cd [↑のプロジェクト名フォルダ]
python manage.py runserver
```
とすると、ローカルサーバ http://127.0.0.1:8000/ が立ち上がる




## Django

- MTV（Model, Template, View）モデル

```
user -> 1 -> View -> 2 -> Model
       <- 6 <-         <- 3 <-
                 5 ^ v 4
               Template
```


### プロジェクト作成
```bash
django-admin startproject [プロジェクト名]
```

### アプリケーション起動
```bash
cd [↑のプロジェクト名フォルダ]
python manage.py runserver <ポート番号指定（未指定は8000）>
```
- ↑は127.0.0.1が起動する. 他からアクセスしたい場合は、0:ポート番号
```bash
python manage.py runserver 0:8000
```

### フォルダファイル構成

```
first_prj/                   # プロジェクトフォルダ
├── manage.py                # アプリケーションの作成、マイグレーション様々な操作を行う
└── first_prj/              # プロジェクト設定フォルダ
    ├── asgi.py             # ASGI（Asynmetry Server Gateway Interface）としてデプロイされる時に使用される（Django3.0から）
    ├── settings.py         # プロジェクト設定ファイル
    ├── urls.py             # URLのディスパッチを設定する
    ├── wsgi.py             # WSGI（Web Server Gateway Interface）としてデプロイされる時に使用される
    └── __init__.py         # Pythonのパッケージであることを知らせるファイル（空でよい）
```

### WSGI, ASGI

- **WSGI**：同期呼び出し、WebSocket HTTP/2.0に非対応
- **ASGI**：非同期呼び出し、WebSocket HTTP/2.0に対応

### setting.py
```python
# Oracle/MySQLなどの指定が可能
DATABASES = {   
    'default': {
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': BASE_DIR / 'db.sqlite3',
    }
}
# 言語の指定
LANGUAGE_CODE = 'ja'

# タイムゾーン.  UTCはUTC時間
TIME_ZONE = 'Asia/Tokyo'
```

### 初期データベース/ テーブル作成

```bash
python manage.py migrate
```
→dbにテーブルが作成される

- auth_group
- auth_group_permissions
- auth_user
- django_admin_log
- django_content_type

### アプリケーション作成
```bash
python manage.py startapp [アプリケーション名]
```

プロジェクトフォルダにfirst_appフォルダが作成される
setting.pyの[INSTALLED_APPS]にアプリケーション名 first_app を追加する

### URLディスパッチ

リクエスト http://127.0.0.1:8000/first_app/hello を叩くと

```python:first_prj/urls.py
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('first_app/', include('first_app.urls'))   # ...<- first_app.urlsをインクルードしている. １つ目の引数がURLのパスに相当する
]
```
```python:first_app/urls.py
from . import views

urlpatterns = [
    path('hello', views.index, name = 'index')  # ...<- helloとviews.indexの紐付けがされている
]
```

```python:first_app/views.py
from django.http import HttpResponse

def index(request):     # <- "index"関数が定義されている
    return HttpResponse('<h1>Hello</h1>')   # ...<- これがレスポンスになる
```

### URLディスパッチと引数

```python:first_app/urls.py
from . import views

urlpatterns = [
    path('hello', views.index, name = 'index'), 
    path('page/<str:user_name>', views.user_page, name = 'user_page'),      # ... <- <str:___> として引数を定義
    path('number_page/<str:user_name>/<int:number>', views.number_page, name = 'number'),   # ...<- 引数は複数入れることも可
]
```
```python:first_app/views.py
from django.http import HttpResponse

def user_page(request, user_name):  # ... <- urls.pyで指定した名前を引数として使える
    return HttpResponse(f'<h1>{user_name}\'s page </h1>')

def number_page(request, user_name, number):    # ...<- 複数の場合は複数引数を定義する
    return HttpResponse(f'<h1>{user_name}\'s page page number = {number} </h1>')
```

### templatesの使用

```
├─TemplateApp
│  │  admin.py
│  │  apps.py
│  │  models.py
│  │  tests.py
│  │  urls.py
│  │  views.py
│  │  __init__.py
│  │
│  └─templates  # templatesフォルダを作成して中にhtmlファイルを置く
│         index.html
│
└─template_prj
    │  asgi.py
    │  settings.py
    │  urls.py
    │  wsgi.py
    │  __init__.py
```

```python:views.py
from django.shortcuts import render

# Create your views here.

def index(request):
    return render(request, 'index.html')    # HttpResponseではなくrenderとすると、指定したhtmlファイルを表示できる
```

### templateフォルダの変更

setting.pyの中のTEMPLATESで変更

```python
TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [TEMPLATE_DIR,],    # ココにフルパスで指定する. フルパスなので、BASE_DIRを使用した変数にする
        'APP_DIRS': True,
#...
```
```python
import os   # ...PATHの結合用にimport

# Build paths inside the project like this: BASE_DIR / 'subdir'.
BASE_DIR = Path(__file__).resolve().parent.parent
TEMPLATE_DIR = os.path.join(BASE_DIR, 'templates2') # ...TEMPLATE_DIRで変更先のtemplateフォルダを指定する

```
### htmlの内容を動的にする

```python:views.py
def index(request):
    return render(request, 'index.html', context={'value':'Hello'}) # ...context=で変数:値を設定
```

```html
<p>{{ value }}</p>  # ...{{}} で括って変数名を指定すれば、htmlに値を表示させることができる
```

### DTL（Django template laguage）
pythonのテンプレートエンジン. pythonでHTMLを利用するためのツール.

```python:views.py

def home(request):
    my_name = 'John'
    favourite_band = ['Beatles', 'Oasis', 'Red Hot Chili Peppers']  # ...リスト型で渡す
    my_info = {'name':'John', 'age': 28}    # ...辞書型で渡す

    return render(request, 'home.html', context={
        'my_name':my_name, 
        'favourite_band':favourite_band,
        'my_info':my_info
    })
```


```html
<h1>Name:{{ my_name}}</h1>
<h2>バンド：{{ favourite_band }}</h2>   # ...リスト型をそのまま表示する
<h2>バンド2：{{ favourite_band.2 }}</h2>    # ...リスト型で要素番号を指定する
<h3>Age: {{ my_info.age }}</h3> # ...辞書型でキーを指定する
```
- 表示
Name:John
バンド：['Beatles', 'Oasis', 'Red Hot Chili Peppers'] 
バンド2：Red Hot Chili Peppers
Age: 28


### DTLの制御文
- Visual Code のプラグイン [Django Templates and Backend snippets]

- html
{% raw %}
```html
<ul>
    {% for band in favourite_band %}    <!-- for文 -->
        <li>{{ band}}</li>
    {% endfor %}        <!-- endfor必要 -->
</ul>

{% if 'beatles' in favourite_band %}    <!-- if文 -->
    <p>Beatles here! </p>
{% elif 'Oasis' in favourite_band %}
    <p>Oasis here! </p>
{% endif %}     <!-- endif 必要 -->

{# コメント #}  <!-- コメント -->
```
{% endraw %}

### templateの継承

- 継承元（親/ベース）
{% raw %}
```html
<!DOCTYPE html>
<html>
    <head>
            <meta charset='utf-8'>
            <title>{% block title %}My Page{% endblock %}</title>   <!-- 継承先で定義するブロックを指定 block [ブロック名] -->
    </head>
    <body>
        <h1> My Page </h1>
        {% block content %}{% endblock %}   <!-- 継承先で定義するブロックを指定 -->
    </body>
</html>
```
{% endraw %}
- 継承先
{% raw %}
```html
{%  extends "base.html" %}      <!-- 継承元を指定  extends [継承元] -->
{% block title %}Sample1 {{ block.super }}{% endblock %}    <!-- blockの中に書き換える内容を書く block.superで継承元の内容を利用できる -->
{% block content %}
<p>Sample1</p>
{% endblock %}
```
{% endraw %}

### template のフィルター
- html
変数名の後に | （パイプ）でコマンドをつなげると変数を編集できる
{% raw %}
```html
<p>{{name|upper}}</p>   
<p>{{page_url|urlize}}</p>  <!-- aタグのリンクにする. 値は ": https://----" コロンとスペースの後にurlとなっている必要がある -->
<p>BMI : {{bmi|floatformat:2}}</p>
<p>給料 : {{salary|default:'秘密'}}</p>
<p>バンド : {{band|random|lower}}</p>
<p>{{band|join:', '}}</p>
<p>{{msg|linebreaks}}</p>   <!-- 改行含む値を改行入で表示 -->
<p>{{msg2|truncatechars:4}}</p> <!-- 長い文字を消す（4文字目以降を...で表示） -->

{% filter upper %}  <!-- 複数行をまとめてフィルタする -->
<p>aaa</p>
<p>bbb</p>
{% endfilter %}
```
{% endraw %}

### フィルターの自作

- Appフォルダの下にtemplatetagsフォルダを作る
```
TemplateApp
├─templatetags
│  │  event_tags.py     # 自作フィルタを記述するファイル
│  │  __init__.py   # 必ず必要だが空ファイルでよい
```

- フィルタを定義するソース（↑のevent_tags.py）
```python
from django import template
register = template.Library()

@register.filter(name='status_to_string')   # フィルタの名前
def convert_status_to_string(status, val):  # 関数名は任意. 引数を一つだけ持てる
    print(f'val={val}')
    if status == 10:
        return 'Success'
    elif status == 20:
        return 'Error'
    elif status == 30:
        return 'Pending'
    else:
        return 'Unknown'
```

    - html
{% raw %}
```html
{% load event_tags %}   <!-- フィルタを定義したファイルのロードを記述 -->

<p>{{status|status_to_string:'aaa' }}</p>   <!-- 自作フィルタ -->
```
{% endraw %}
※templatetagsが有効になるにはアプリの再起動が必要

- templateの画面遷移

    - 遷移元html
    {% raw %}`{%url  'app名:遷移先ページ名'%}`{% endraw %}をつかう
    後ろに遷移先にわたす引数を書ける
{% raw %}
```html
<a href="{% url 'template_app:home'  first_name='Saburo' last_name='Ito' %}">home</a>   
```
{% endraw %}

    - urls.py
    引数を受ける側
```python
urlpatterns = [
    path('', views.index, name = 'index'),
    path('home/<first_name>/<last_name>', views.home, name = 'home'),   # 引数名を指定する
]
```

    - views.py
    引数をうける側のviews.py
```python
def home(request, first_name, last_name):   # 関数の引数を指定する
    my_name = f'{first_name} {last_name}'
```

- templateでstaticの利用

    - settings.py
    識別子ありの場合
```python
STATIC_DIR = os.path.join(BASE_DIR, 'static')
STATIC_DIR_2 = os.path.join(BASE_DIR, 'css')

STATICFILES_DIRS = [
    ('download', STATIC_DIR),
    ('css', STATIC_DIR_2)
]
```
    識別子なしの場合
```python
STATIC_DIR = os.path.join(BASE_DIR, 'static')

STATICFILES_DIRS = [
    STATIC_DIR
]
```

    - htmlの記述
    識別子ありの場合
{% raw %}
```html
{% load static %}
<link rel="stylesheet" type="text/css" href="{% static 'css/home.css' %}">

<img src="{% static 'download/sample.png' %}">
<img src="{% static 'download/aaa.jpg' %}">
```
{% endraw %}

- インスタンスプロパティの使用

    - views.py
```python
class Country:  # クラスを定義してプロパティを持つ

    def __init__(self, name, population, capital) -> None:
        self.name = name
        self.population = population
        self.capital = capital
    
def sample3(request):
    country = Country('Japan', 10000000, 'Tokyo')
    return render(request, 'sample3.html', context={    # render contextでクラスを渡す
        'country' : country
    })
```

    - html側
    クラス名.プロパティ名で使用できる
```html
国名：{{ country.name}} <br>
人口：{{ country.population}} <br>
首都：{{ country.capital}} <br>
```


### Modelの使用

- テーブルの作成

  - models.py
```python
from django.db import models

# Create your models here.

class Person(models.Model): # テーブル名
    first_name = models.CharField(max_length=30)    # カラム名. pkを指定しないと自動的にpkのidカラムが作成される
    last_name = models.CharField(max_length=30)

class Sales(models.Model):
    fee = models.IntegerField()
    create_at = models.DateTimeField()
    update_at = models.DateTimeField()
```

- classに指定したテーブルの反映...マイグレーションという

```bash
python manage.py makemigrations ModelApp --name add_person
```
add_person→マイグレーション対象の名前

- フォルダ構成
```
アプリケーションフォルダ\migrations
│  0001_add_person.py   # makemaigraions で生成されるファイル
│  __init__.py
```

- マイグレーションの実行
```bash
python manage.py migrate ModelApp
```
データーベースにテーブルができる  
前回のmigrate後、makemaigrationsを実行したものを適用する

- マイグレーションの適用状況の確認
```bash
python manage.py showmigrations ModelApp
```

ModelApp
```
 [X] 0001_add_person    ←実行されているものがXマーク
 [X] 0002_add_sales
```

- マイグレーションを戻す
  ↑の状態で0002_add_salesを戻すとき
```bash
python manage.py migrate ModelApp 0001_add_person
```
とすると、0002がなかったことになる。テーブルも消える.  
migrationsフォルダのなかのファイルは手動で削除する

- すべてのマイグレーションを戻す
```bash
python manage.py migrate ModelApp zero
```

- カラムの制約
  - primary_key
  - unique
  - null
  - db_index
  - default
  - blank

- django管理画面
```bash
python manage.py createsuperuser
```

http://127.0.0.1:8000/admin
でデータの登録などが可能

- Meta属性（model の継承）
    - abstract属性でModelの継承（共通のカラムの定義など）が可能
    - models.py
```python
class BaseMeta(models.Model):
    create_at = models.DateTimeField(timezone.datetime.now)
    update_at = models.DateTimeField(timezone.datetime.now)

    class Meta:
        abstract = True     # 抽象クラス＝マイグレーションしてもテーブルが作成されない

class Person(BaseMeta): # このテーブルにはcreate_at/update_atが追加される
   first_name = models.CharField(max_length=30)
 ```
このままマイグレーションするとcreate_at/update_atは実テーブルでは先頭に来る
カラム構成の並び順を変える場合はマイグレーションファイルを直接書き換える

- models.py
```python
    class Meta:
        db_table = 'person'     # 実テーブル名を書き換える場合. 指定しないと[アプリケーション名_クラス名]という名前になる
        index_together = [['first_name', 'last_name']]  # 複合インデックス
        ordering = ['salary']   # デフォルト並び順
```

- レコードの追加
    - main.pyなど
```python
import os

from django.db.models.fields import EmailField
os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'ModelProject.settings')    # settings.pyを環境変数に入れる
from django import setup

setup()

from ModelApp.models import Person

# 方法その1. models.pyに定義したクラスをインスタンスで生成してsave()メソッドを実行する
p = Person(first_name = 'Taro', last_name = 'Sato',
    birthday = '2000-12-10', email = 'hogehoge@mail.com', salary = 20000, memo = 'memomemo',
    web_site = 'https://hogehoge.com'
)

p.save()

# 方法その2. クラス名.objects.create()メソッドで追加する
Person.objects.create(
    first_name = 'Jiro', last_name = 'Ito', 
    birthday = '2000-12-10', email = 'hogehoge@mail.com', salary = None, memo = 'memomemo',
    web_site = ''
)

# 方法その3. get_or_create()メソッドで追加する
# キーが同じレコードがあれば取得、なければ追加して取得
# idがプライマリーキーの場合はすべての項目が同じ値なら追加されない
obj, created = Person.objects.get_or_create(
    first_name = 'Saburo', last_name = 'Ito3', 
    email = 'hogehoge@mail.com', salary = None, memo = 'memomemo',
    web_site = ''
)

# objには追加した内容が、createdはTrue/Falseが返る
print(obj)
print(created)
```

- レコードの取得
    - main.pyなど
```python
# 全件取得
persons = Person.objects.all()

for p in persons:
    print(p.id, p)


# 条件指定して取得 get() メソッドは1件のみ取得. 複数件でもエラー、レコード無しでもエラー
person = Person.objects.get(first_name = 'Taro')
person = Person.objects.get(first_name = 'taro')
# pkを指定する
person = Person.objects.get(pk = 1)

# filter()メソッド. filter内に条件を書いてall()メソッド
persons = Person.objects.filter(first_name = 'Taro').all()

# 配列で取得も可
print(persons[0].email)
```

- レコードの更新
    - main.pyなど
```python
# 1件取得してインスタンスの内容を書き換えてsave()メソッドを実行
p = Person.objects.get(id=1)

p.birthday = '2001-01-01'
p.update_at = timezone.datetime.now(pytz.timezone('Asia/Tokyo'))
p.save()

# objects.update()メソッドで一括更新
Person.objects.filter(first_name = 'Saburo').update(
    web_site = 'http://sample.com',
    update_at = timezone.datetime.now(pytz.timezone('Asia/Tokyo'))
)
```

- レコードの削除
```python
# 条件入れて削除
Person.objects.filter(first_name = 'Saburo').delete()
# 全レコード削除
Person.objects.all().delete()
```

- 外部キーの作成...1対多のリレーション
    - models.py
      - models.ForeignKey()で参照先テーブル名を指定すると、そのプライマリーキーがテーブルに項目として追加される
```python
class Students(models.Model):
    name = models.CharField(max_length=20)
    age = models.IntegerField
    major = models.CharField(max_length=20)
    school = models.ForeignKey(         # この書き方でdbテーブルのカラムはschool_idとなりschool.idの外部キーとなる
        'Schools',      # 参照先テーブル名
        on_delete=models.CASCADE        # 参照先が削除された時の挙動を表す
    )

    class Meta:
        db_table = 'students'
```
- on_deleteの種類
  - **CASCADE**：参照先が削除されると親レコードも強制削除
  - **PROTECT**：参照先が削除されると例外発生（保護する）
  - **SET_NULL**：（null=Trueが必要）値にNULLがセットされる
  - **RESTRICT**：参照先の参照先が削除されたら削除を有効にする？
  - **SET_DEFAULT/SET()**

- 外部キーのレコード設定
  - ↑の続き
  - main.pyなど
```python
from ModelApp.models import Students, Schools, Prefectures

prefectures  =['東京', '大阪']
schools = ['東高校', '西高校', '北高校', '南高校']
students = ['太郎', '次郎', '三郎']

def insert_records():
    for prefecture_name in prefectures:
        prefecture = Prefectures(
            name = prefecture_name
        )
        prefecture.save()
        for school_name in schools:
            school = Schools(
                name = school_name,
                prefecture = prefecture     # 外部キー項目には外部キーのインスタンスを割り当てる
            )
            school.save()
            for student_name in students:
                student = Students(
                    name = student_name,
                    age = 17,
                    major = '物理', 
                    school = school     # 外部キー項目には外部キーのインスタンスを割り当てる
                )
                student.save()
```

- 外部キー項目のselect
  - main.pyなど
```python
def select_students():
    students = Students.objects.all()
    for student in students:
        print(student.id, student.name, student.school.id, student.school.name, student.school.prefecture.id, student.school.prefecture.name)
        # 外部キーインスタンス.項目名で外部キーテーブルの項目名を参照する
```

- 1対1のリレーション
  - models.py
```python
class Places(models.Model):
    name = models.CharField(max_length=50)
    address = models.CharField(max_length=80)

    class Meta:
        db_table = 'places'
    
class Restaurants(models.Model):

    places = models.OneToOneField(      # 1対1のリレーションを設定
        Places,
        on_delete=models.CASCADE, primary_key=True
    )
    name = models.CharField(max_length=50)

    class Meta:
        db_table = 'restaurants'
```
ForeignkeyField()は参照先をクラス名の文字列で指定。OneToOneFieldは参照先をクラスオブジェクトで指定.



- 多対多のリレーション
  - models.py
```python
class Authors(models.Model):
    name = models.CharField(max_length=50)

    class Meta:
        db_table = 'authors'

class Books(models.Model):
    name = models.CharField(max_length=50)
    authors = ManyToManyField(Authors)      

    class Meta:
        db_table = 'books'
```
- ManyToManyField...　これを追加することで上の例では、実テーブルはbooks, authors, books_authors(id, books_id, authors_id)の3テーブルが作成される
  
    - main.pyなど
```python
book1 = Books(name = 'book1')
book1.save()

author1 = Authors(name = 'author1')
author2 = Authors(name = 'author2')
author1.save()
author2.save()

book1 = Books.objects.get(pk=1)
author1 = Authors.objects.get(pk=1)
author2 = Authors.objects.get(pk=2)
book1.authors.add(author1, author2)     # これでbooks_authorsにレコードが2件できる. addメソッドの引数はいくつでもよい.
```

- リレーション先のデータの取得方法
    - main.pyなど
```python
from ModelApp.models import Students, Schools, Prefectures

s = Schools.objects.first()

print(s.prefecture.name)    # 参照先はインスタンス名で取る
print(s.students_set)   # 参照されていてForeignKeyがない側は、項目名_setで取得できる
    # ↑RelatedManagerという型

for ss in s.students_set.all(): # 項目名_set.all()でレコードセット
    print(ss.name)

from ModelApp.models import Places, Restaurants

p = Places.objects.first()
print(p.restaurants.name)   # onetoone結合はインスタンス名で取得

r = Restaurants.objects.first()
print(r.places.name)

from ModelApp.models import Books, Authors

b = Books.objects.first()
print(b.authors.all())      # manytomanyはmanytomanyがある側はインスタンス名

a = Authors.objects.first()
print(a.books_set.all())    # ない側は項目名_setで取る
```

- 条件付きデータの取得
    - main.pyなど
```python
print(Students.objects.all())   # 全件取得

print(Students.objects.all()[:5])   # 0から始まって、5より小さい件数なので先頭5件

print(Students.objects.all()[5:])   # 5件目以降

print(Students.objects.all()[5:8])      # 5,6,7件目を取得

print(Students.objects.all()[5:8].query)    # 発行しているsqlを取得
#  LIMIT 3 OFFSET 5

print(Students.objects.first())   # 先頭1件

print(Students.objects.filter(name = '太郎'))   

print(Students.objects.filter(age = 17))

print(Students.objects.filter(name = '太郎', pk__gt=13))    # カンマ区切りで and 条件. __gt→greater than

print(Students.objects.filter(name = '太郎', pk__lt=20))    # less than

print(Students.objects.filter(name__startswith = '太'))     # で始まる

print(Students.objects.filter(name__endswith = '郎'))   # で終わる

from django.db.models import Q  # OR条件の場合はQをimport

print(Students.objects.filter(Q(name = '太郎') | Q(pk__gt=19)))     # Q() とパイプを指定

print(Students.objects.filter(pk__in=[13,14]))  # in 句

print(Students.objects.filter(name__contains='三')) # を含む

print(Person.objects.filter(salary__isnull=True))   # 項目名__isnull= null/ is not null を抽出

print(Person.objects.exclude(salary__isnull=False))   # 一致しないものの抽出 exclude

print(Students.objects.values('name', 'age').all())   # 項目の指定

print(Students.objects.order_by('name', 'age').all())   # 並び替え
print(Students.objects.order_by('-name').all())   # 並び替え（降順）


for student in Students.objects.filter(school__name = '南高校'):    # フィルターにも外部キーを使える [外部キーインスタンス名]__[項目名]
    print(student.name, student.school.name, student.school.prefecture.name)

for student in Students.objects.exclude(school__name = '南高校'):    # エクスクルードも同様
    print(student.name, student.school.name, student.school.prefecture.name)

print (Students.objects.filter(school__name = '南高校').query)  # 実際のSQLはinner join している

for student in Students.objects.order_by('school__name'):    # order by も同様
    print(student.name, student.school.name, student.school.prefecture.name)

print(Students.objects.values('school__name').annotate(     # group by も同様
    Max('id'), Min('id')
))
```

- values の group by 句について
    - models.py
```python
class Students(models.Model):
    class1 = models.ForeignKey(
        'Classes', on_delete=models.CASCADE
    )
    name = models.CharField(max_length=50)
    grade = models.IntegerField()

    class Meta:
        db_table = 'students'

class Classes(models.Model):
    name = models.CharField(max_length=50)

    class Meta:
        db_table = 'classes'
```
↑の場合、
- main.pyなど
```python
print(Classes.objects.values('name').annotate().query)
```
結果は
```sql
SELECT "classes"."name" FROM "classes"
```
```python
print(Classes.objects.values('name', 'students__id').annotate().query)
```
結果は
```sql
SELECT "classes"."name", "students"."id" FROM "classes" LEFT OUTER JOIN "students" ON ("classes"."id" = "students"."class1_id")
```
つまり  
Classes.objectsのvaluesに  
student__id（クラス名__項目名）を記述するだけで、自動的にjoin（left join）される

普段SQLを書く時は取得したい値が格納されているテーブルをメインで書いて、  
属性を持つものを結合して書いていくが、  
modelsの場合は、最終系から書いていく感じ  


### Formの使用

HTMLフォームを簡単に作成できる  
フォームの値を簡単にチェックできる（バリデーション）

- 基本的な使い方
  - forms.py
```python
from django import forms
from django.forms.forms import Form

class UserInfo(forms.Form):     # HTML1フォームのクラスを定義
    name = forms.CharField()        # 項目を定義
    age = forms.IntegerField()
    mail = forms.EmailField()
```
    - views.py
```python
from . import forms

def form_page(requet):      # templateでやった1ページごとのクラス
    form = forms.UserInfo()     # フォームクラス
    if requet.method == 'POST':     # POSTの場合
        form = forms.UserInfo(requet.POST)      # request.POSTでフォームの値を取得できる
        if form.is_valid():             # is_valid()でバリデーション
            print('バリデーション成功')
            print(
                f"name:{form.cleaned_data['name']} mail:{form.cleaned_data['mail']} age:{form.cleaned_data['age']}"     # フォームの値の取得 form.cleaned_data[] でトル
            )
    return render(
        requet, 'formapp/form_page.html', context={     # formクラスを渡す(返す)
            'form' : form
        }
    )
```
  - html側
{% raw %}
```html
    <body>
    <form method="POST">
        {% csrf_token %}        <!-- クロスサイトリクエストフォージェリ対策のため、formを使う場合は必ず入れる -->
        <table>
        {{form.as_table}}       <!-- form.as_p, as_ul, as_table などで形式化できる -->
        </table>
        <input type="submit" value="送信">
    </form>
    </body>
```
{% endraw %}

- formクラスの様々な入力形式
  - forms.py
```python
from django import forms
from django.forms.forms import Form

class UserInfo(forms.Form):     # HTML1フォームのクラスを定義
    name = forms.CharField()        # 値の形式によって定義する
    age = forms.IntegerField()      
    mail = forms.EmailField()
    is_married = forms.BooleanField()
    birthday = forms.DateField()
    salary = forms.DecimalField()
    job = forms.ChoiceField(
        choices=((1, '正社員'), (2, '自営業'), (3, '学生'), (4, '無職'))
    )
    hobbies = forms.MultipleChoiceField(
        choices=((1, 'スポーツ'), (2, '読書'), (3, '映画鑑賞'), (4, 'その他'))
    )
    homepage = forms.URLField()
```

- formクラスの項目のカスタマイズ
  - forms.py
```python
class UserInfo(forms.Form):
    name = forms.CharField(label='名前', max_length=10, min_length=5)   # ラベルの追加、項目長の指定
    age = forms.IntegerField(label='年齢')
    mail = forms.EmailField(
        label='メールアドレス'
        , widget=forms.TextInput(attrs={'placeholder': 'sample@mail.com'})) # プレースホルダの指定. attrs＝attributeの意味？この変数名でないとダメ
    is_married = forms.BooleanField(initial=True)       # 初期値
    birthday = forms.DateField(initial='2010-01-01')
    salary = forms.DecimalField()
    job = forms.ChoiceField(
        choices=((1, '正社員'), (2, '自営業'), (3, '学生'), (4, '無職'))
        , widget=forms.RadioSelect      # ラジオボタンに変更
    )
    hobbies = forms.MultipleChoiceField(
        choices=((1, 'スポーツ'), (2, '読書'), (3, '映画鑑賞'), (4, 'その他'))
        , widget=forms.CheckboxSelectMultiple           # チェックボックスに変更
    )
    homepage = forms.URLField(required=False)       # 必須項目から変更
    memo = forms.CharField(label='メモ' ,widget=forms.Textarea)     # テキストエリアに変更
```

- formクラスのid/classの付与
  - forms.py
```python
# 方法1...widgetのattrsを使う方法
class UserInfo(forms.Form):
    name = forms.CharField(label='名前', max_length=10, min_length=5)
    age = forms.IntegerField(label='年齢')
    mail = forms.EmailField(
        label='メールアドレス'
        , widget=forms.TextInput(attrs={'placeholder': 'sample@mail.com', 'class':'mail-class'}))       

# 方法2...コンストラクタで付与する方法
class UserInfo(forms.Form):
    def __init__(self, *args, **kwargs) -> None:
        super(UserInfo, self).__init__(*args, **kwargs)
        self.fields['job'].widget.attrs['id'] = 'id_job'
        self.fields['hobbies'].widget.attrs['class'] = 'hobbies_class'
```

- formのバリデーション
エラーにするには、forms.ValidationError('メッセージ')
  - forms.py
    - 方法1 clean_[項目名](self)の関数を作って値をチェックする
```python
class UserInfo(forms.Form):
    hobbies = forms.MultipleChoiceField(
        choices=((1, 'スポーツ'), (2, '読書'), (3, '映画鑑賞'), (4, 'その他'))
        , widget=forms.CheckboxSelectMultiple
    )
    homepage = forms.URLField(required=False)
    memo = forms.CharField(label='メモ' ,widget=forms.Textarea)
        
    def clean_homepage(self):   # 関数を追加する
        homepage = self.cleaned_data['homepage']
        if not homepage.startswith('https'):
            raise forms.ValidationError('httpsではありません')
```
  - 方法2 項目定義のパラメータvalidators=[]を設定する. 中にはcore.validatorsを使うこともできるし、自作関数を設定することもできる.
```python
from django.core import validators

def check_name(value):      # 自作チェック関数
    if value == 'あああああ':
        raise forms.ValidationError('この名前は登録できません')

class UserInfo(forms.Form):
    name = forms.CharField(label='名前', max_length=10, min_length=5, validators=[check_name])
    age = forms.IntegerField(label='年齢', validators=[validators.MinValueValidator(20, message='20以上でなければなりません')])     # validatorsを使う
```
  - 方法3 clean(self)関数を使う
```python
class UserInfo(forms.Form):
    ...
    ...
    def clean(self):
        cleaned_data = super().clean()      # 継承して入力データを取得
        mail = cleaned_data['mail']
        verify_mail = cleaned_data['verify_mail']
        if mail != verify_mail:
            raise forms.ValidationError('メールアドレスが一致しません')     # チェック
```

- ModelForm
  ModelとFormを合体して一つで簡単に扱う
    - models.py
```python
from django.db import models

class Post(models.Model):
    name = models.CharField(max_length=50)
    title = models.CharField(max_length=255)
    memo = models.CharField(max_length=255)
```
    - forms.py
```python
from .models import Post

class PostModelForm(forms.ModelForm):       # forms.ModelFormを継承したクラス
    # ココにはフィールドの装飾を書く（必須ではない）
    memo = forms.CharField(
        widget=forms.Textarea(attrs={'rows':30, 'cols':20})
    )
    class Meta:
        model = Post        # どのmodelを使うか  
        # fields = '__all__'        # modelの中のどの項目を使うか←すべて
        # fields = ['name', 'title']        # ←項目指定
        exclude = ['title']     # 除外項目
```

  - views.py
```python
def form_post(request):
    form = forms.PostModelForm()        # formsのクラス
    if request.method =='POST':
        form = forms.PostModelForm(request.POST)
        if form.is_valid():
            form.save()     # saveで更新
    return render(
        request, 'formapp/form_post.html', context={
            'form' : form
        }
    )
```

- formの各要素を表示
    - html
{% raw %}
```html
    <form method="POST">
        {% csrf_token %}
        {{form.name.label}}:{{form.name}}   <!-- formのname要素とnameのラベルを表示 -->
        {% if form.name.errors %}           <!-- nameに対するエラーを表示 -->
            <div style="color:red;">{{ form.name.errors.as_text}}</div>
        {% endif %}
        <input type="submit" value="送信">
    </form>
```
{% endraw %}

- formのエラーを表示
  - html
{% raw %}
```html
    <form method="POST">
        {% csrf_token %}
        {{ form.non_field_errors }}     <!-- フィールドに関わらないエラーを表示 -->

        {% if form.errors %}        <!-- エラーをループで取り出して表示...ページの一番上にまとめて表示など -->
            {% for key, value in form.errors.items %}
                <p>{{key}}: {{value.as_text}}</p>
            {% endfor %}
        {% endif %}
```
{% endraw %}


- formの外だし
    - html
        - 大本ファイル
    includeで差し替えるhtmlファイルを指定する
    with句で引数を渡す
{% raw %}
```html
    <body>
        {% include "formapp/form_template2.html" with as_table=True %}
    </body>
```
{% endraw %}
- 差し替えるhtmlファイル

差し替える部分だけを書く
{% raw %}
```html
    <form method="POST">
        {% csrf_token %}
        {% if as_table %}       <!-- 引数で表示を切り替える例 -->
            <table>
             {{ form.as_table }}
            </table>
        {% elif as_ul %}
            {{ form.as_ul }}
        {% else %}
            {{ form.as_p }}
        {% endif %}
        <input type="submit" value="送信">
    </form>
```
{% endraw %}

- formset...複数のformを扱う
  - forms.py
    通常のフォームクラス
```python
class FormSetPost(forms.Form):
    title = forms.CharField(label='タイトル')
    memo = forms.CharField(label='メモ')
```

- views.py

formset_factoryを使う
```python
from django.forms import formset_factory

def form_set_post(request):
    TestFormset = formset_factory(forms.FormSetPost, extra=3)   # extra=でformの数を指定
    formset = TestFormset(request.POST or None)
    if formset.is_valid():
        for form in formset:
            print(form.cleaned_data)

    return render(
        request,'formapp/form_set_post.html', context={'formset':formset}
    )
```
- html

formset.management_formの指定が必要
{% raw %}
```html
    <form method="POST">
        {% csrf_token %}
        {{ formset.management_form }}   <!-- ←これ必要 -->
        {% for form in formset %}   <!-- formを繰り返す...formset.as_pでもよいが繰り返すことで区切りタグを入れることができる -->
            {{form.as_p}}
            <hr>
        {% endfor%}
        <input type="submit" value="送信">
    </form>
```
{% endraw %}

- modelSetFormで複数のフォームを一括で扱う
    - forms.py
```python
class ModelFormSetPost(forms.ModelForm):
    title = forms.CharField(label='タイトル')
    memo = forms.CharField(label='メモ')

    class Meta:
        model = ModelSetPost
        fields = '__all__'
```

- models.py
```python
class ModelSetPost(models.Model):
    title = models.CharField(max_length=255)
    memo = models.CharField(max_length=255)
```

- html
{% raw %}
```html
    <form method="POST">
        {% csrf_token %}
        {{ formset.as_p }}
        <input type="submit" value="送信">
    </form>
```
{% endraw %}

- views.py
```python
from django.forms importmodelformset_factory
from .models import ModelSetPost

def modelform_set_post(request):
    # TestFormset = modelformset_factory(ModelSetPost,  fields='__all__', extra=3)
    TestFormset = modelformset_factory(ModelSetPost,  form=forms.ModelFormSetPost, extra=3)
    formset = TestFormset(request.POST or None, queryset=ModelSetPost.objects.filter(id__gt=3))     # queryset=でmodelの条件を指定できる
    if formset.is_valid():
        formset.save()

    return render(
        request,'formapp/modelform_set_post.html', context={'formset':formset}
    )
```

- ファイルのアップロード
    - settings.py
    ファイルのアップロード先を指定
```python
MEDIA_URL = '/media/'
MEDIA_ROOT = os.path.join(BASE_DIR, 'media')
```
    - views.py
```python
from django.core.files.storage import FileSystemStorage
import os

def upload_sample(request):
    if request.method == 'POST' and request.FILES['upload_file']:
        upload_file = request.FILES['upload_file']  # ファイルの取り出し
        fs = FileSystemStorage()
        file_path = os.path.join('upload', upload_file.name)
        file = fs.save(file_path, upload_file)  # ファイルの保存
        uploaded_file_url = fs.url(file)

        return render(
            request,'formapp/upload_sample.html', context={'uploaded_file_url':uploaded_file_url}
        )
    
    return render(
        request,'formapp/upload_sample.html'
    )
```

- html
{% raw %}
```html
    <form method="POST" enctype="multipart/form-data">  <!-- enctypeにmultipart/form-dataを指定して、ファイル（バイナリデータ）を扱えるようにする -->
        {% csrf_token %}
        <input type="file" name="upload_file"><br>
        <input type="submit" value="保存">
    </form>
{% endraw %}

    {% if uploaded_file_url %}
    <p>保存先： <a href="{{ uploaded_file_url }}"> {{ uploaded_file_url}} </a></p>
    {% endif %}
```
- アップロードされたファイルをmodelと紐つける
    - model.py
```python
class User(models.Model):
    name = models.CharField(max_length=50)
    age = models.IntegerField()
    picture = models.FileField(upload_to='picture/%Y/%m/%d')    # FileFiled型にして、upload_to=で保存フォルダを指定する. /%Y/%m/%dは日付のフォルダに格納する方法
```
- forms.py
```python
class UserForm(forms.ModelForm):

    class Meta:
        model = User
        fields = '__all__'
```
- views.py
```python
def upload_model_form(request):
    user = None
    if request.method == 'POST' :
        form = forms.UserForm(request.POST, request.FILES)  # formからデータを受け取り、
        if form.is_valid():
            user = form.save()      # 正常ならsaveする
    else:
        form = forms.UserForm()
    return render(
        request,'formapp/upload_model_form.html', context={'form':form, 'user':user}
    )
```
- html
{% raw %}
```html
    <form method="POST" enctype="multipart/form-data">
        {% csrf_token %}
        {{ form.as_p }}     <!-- フォームを表示 -->
        <input type="submit" value="保存">
    </form>
{% endraw %}

    {% if user %}
    <p>name： {{ user.name }}</p>
    <p>age： {{ user.age }}</p>
    <img src="{{ user.picture.url }}">  <!-- picture.urlでパスが返る -->
    {% endif %}
    </body>
```
- プロジェクトフォルダ/urls.py

実運用ではWebサーバでファイルのパスを通す. 以下はpython開発環境のserverで画像を取り扱うときの例
```python
from django.conf import settings
from django.conf.urls.static import static

urlpatterns = [
    path('admin/', admin.site.urls),
    path('form_app/', include('FormApp.urls')),
]

if settings.DEBUG:
    urlpatterns += static(settings.MEDIA_URL, document_root = settings.MEDIA_ROOT)
```

- formとmodelformをつかったinsert/update/delete
    - forms.py
```python
# insert/update/deleteそれぞれ専用のFormを用意する

# insertはModelForm
class StudentsModelForm(forms.ModelForm):
    name = forms.CharField(label='名前')
    age = forms.IntegerField(label='年齢')
    grade = forms.IntegerField(label='学年')
    picture = forms.FileField(label='写真', required=False)

    class Meta:
        model = Students
        fields = '__all__'

# updateはForm...view側でmodelに更新するため
class StudentsUpdateForm(forms.Form):
    name = forms.CharField(label='名前')
    age = forms.IntegerField(label='年齢')
    grade = forms.IntegerField(label='学年')
    picture = forms.FileField(label='写真', required=False)

# deleteはFormで、キーのみ
class StudentsDeleteForm(forms.Form):
    id = forms.IntegerField(widget=forms.HiddenInput)
```

- views.py
```python
# insert は ModelFormを使用する
def form_students(request):
    student = None
    form = forms.StudentsModelForm(request.POST or None, request.FILES or None)
    if request.method == 'POST':
        if form.is_valid():
            student = form.save()

    return render(request, 'formapp/form_students.html', context={
        'form' : form, 'student' : student
    })

# updateはメソッドパラメータにキーを追加する
# modelに項目ごとにセットする
# ModelFormにinstance=modelを指定して項目を記述しないやり方もあるが、この方がわかりやすいかもしれない. ファイルを扱うときなどは特に.
def update_student(request, id):
    student = Students.objects.get(id=id)
    update_form = forms.StudentsUpdateForm(
        initial={
            'name' : student.name,
            'age' : student.age,
            'grade' : student.grade,
            'picture' : student.picture
        }
    )
    if request.method == 'POST':
        update_form = forms.StudentsUpdateForm(request.POST or None, request.FILES or None)
        if update_form.is_valid():
            student.name = update_form.cleaned_data['name']
            student.age= update_form.cleaned_data['age']
            student.grade = update_form.cleaned_data['grade']
            picture = update_form.cleaned_data['picture']
            print(picture)
            if picture:
                fs = FileSystemStorage()
                file_name = fs.save(os.path.join('picture', picture.name), picture)
                print(file_name)
                student.picture = file_name

            student.save()
    return render(request, 'formapp/update_student.html', context={
        'update_form' : update_form,
        'student' : student
    })

# deleteはパラメータのキーでmodelに対して削除する
def delete_student(request, id):
    delete_form = forms.StudentsDeleteForm(
        initial={
            'id' : id
        }
    )
    if request.method == 'POST':
        delete_form = forms.StudentsDeleteForm(request.POST or None)
        if delete_form.is_valid():
            Students.objects.get(id=delete_form.cleaned_data['id']).delete()

    return render(request, 'formapp/delete_student.html', context={
        'delete_form' : delete_form
    })
```
- urls.py
```python
# updateとinsertはurl引数にキーを設定する
urlpatterns = [
    path('form_students', views.form_students, name = 'form_students'),
    path('update_student/<int:id>', views.update_student, name = 'update_student'),
    path('delete_student/<int:id>', views.delete_student, name = 'delete_student'),
]
```

### viewの応用

- リダイレクト
  - views.py
```python
def to_google(request):
    return redirect('https://www.google.jp')     # googleに飛ばす

def one_item(request):
    return redirect('store:item_detail', id=1)      # パラメータ付きで別のページに飛ばす

def item_detail(request, id):
    item = Items.objects.filter(id=id).first()
    if item is None:
        return redirect('store:item_list')   # 存在しないデータで来た場合に別のページにリダイレクト
```
- リダイレクトされたときのログ...302が出る
```
[21/Nov/2021 10:17:00] "GET /store/item_detail/10 HTTP/1.1" 302 0   # ステータスコード302がリダイレクト
[21/Nov/2021 10:17:00] "GET /store/item_list/ HTTP/1.1" 200 510
```


- エラーハンドリング
    - settings.py
      - デバッグモードでない場合にエラーハンドリングが有効
```python
DEBUG = False       # Falseに設定
ALLOWED_HOSTS = ['*']   # Falseに設定したらALLOWED_HOSTSにも値が必要
```

- views.py
  - viewsに専用関数を用意
```python
def page_not_found(request, exception):
    return render(request, 'store/404.html', status=404)
    # return redirect('store:item_list')

def server_error(request):
    return render(request, 'store/500.html', status=500)
```
- プロジェクト/urls.py
  - handler404, handler500を指定
```python
from store import views

handler404 = views.page_not_found   # httpステータスコード404（ページが存在しない）のときに飛ばす定義
handler500 = views.server_error  # httpステータスコード500（サーバ内部エラー）のときに飛ばす定義
```

- viewの中で404を発生させたい場合
  - views.py
```python
def item_detail(request, id):
    if id == 0:
        raise Http404
```

- modelの取得と同時に404を発生させたい場合
  - views.py
    - get_list_or_404, get_object_or_404
```python
from django.shortcuts import redirect, render, get_list_or_404, get_object_or_404

def item_detail(request, id):
    item = get_object_or_404(Items, id=id)  # この時点でデータがなければ404が発生. listも同じ
```

- パスワード設定
  - パスワードのハッシュ関数
    - settings.py
```python
# https://docs.djangoproject.com/en/3.2/topics/auth/passwords/ のページに従って追加する.
# 上から順に使われる
PASSWORD_HASHERS = [
    'django.contrib.auth.hashers.PBKDF2PasswordHasher',
    'django.contrib.auth.hashers.PBKDF2SHA1PasswordHasher',
    'django.contrib.auth.hashers.Argon2PasswordHasher',
    'django.contrib.auth.hashers.BCryptSHA256PasswordHasher',
]
```

- argon2はインストールが必要
```bash
pip install django[argon2]
```

- ログインの基本
  - django.contrib.auth.models.userを使う
  - userに追加項目をもたせるときはOneToOne関係でmodelを追加する
    - models.py
```python
class Profile(models.Model):
    user = models.OneToOneField(User, on_delete=models.CASCADE)
    website = models.URLField(blank=True)
    picture = models.FileField(blank=True, upload_to='user/')

    def __str__(self) -> str:
        return self.user.username
```
- formにはwidget=PasswordInput()を使用する
    - forms.py
```python
class LoginForm(forms.Form):
    username = forms.CharField(label='名前', max_length=50)
    password = forms.CharField(label='パスワード', widget=PasswordInput())
    confirm_password = forms.CharField(label='パスワード再入力', widget=PasswordInput())
    
    def clean(self):        # 確認用パスワードのチェックを追加
        cleaned_data = super().clean()
        password = cleaned_data['password']
        confirm_password = cleaned_data['confirm_password']
        if password != confirm_password:
            raise forms.ValidationError('パスワードが一致しません')
```
- 管理画面に表示させるにはadmin.pyに追加
  - admin.py
```python
from user.models import Profile

admin.site.register(Profile)
```

- ユーザー新規登録時、パスワードの設定はset_passwordを使う
  - views.py
```python
def register(request):
    user_form = UserForm(request.POST or None)
    profile_form = ProfileForm(request.POST or None, request.FILES or None)
    
    if user_form.is_valid() and profile_form.is_valid():
        user = user_form.save()
        user.set_password(user.password)    # ←ココ...なぜuser_form.save()で保存されないのか...
        user.save()
        profile = profile_form.save(commit=False)   # commit=False？
        profile.user = user     # リレーションを追加
        profile.save()
```
- ログイン時、django.contrib.auth.authenticateで検証
- okならdjango.contrib.auth.loginをする
    - views.py
```python
from django.contrib.auth import authenticate, login, logout
from django.contrib.auth.decorators import login_required

def user_login(request):
    login_form = LoginForm(request.POST or None)
    
    if login_form.is_valid():
        username = login_form.cleaned_data["username"]
        password = login_form.cleaned_data["password"]

        user = authenticate(username=username, password=password)   # ←ログイン検証
        if user:
            if user.is_active:
                login(request, user)        # ←ログイン
                return redirect('user:index')
            else:
                return HttpResponse('アカウントがアクティブではありません')
        else:
            return HttpResponse('ユーザーが存在しません')

    return render(request, 'user/login.html', context={
        'login_form' : login_form
    })
```
- ログアウト
  - django.contrib.auth.logout
```python
@login_required         # ←これでログイン中しか行えないメソッドになる
def user_logout(request):
    logout(request)
    return redirect('user:index')
```

- htmlでログイン中か判断する
  - html側
```html
        {% if user.is_authenticated %}
```

- パスワードのvalidation
  - パスワードのチェック方法はsettings.pyに記載
    - settings.py
```python
AUTH_PASSWORD_VALIDATORS = [
    {
        'NAME': 'django.contrib.auth.password_validation.UserAttributeSimilarityValidator',
    },
    {
        'NAME': 'django.contrib.auth.password_validation.MinimumLengthValidator',
        'OPTIONS':{     # オプションで桁数を指定するなど
            'min_length':8, 
        }
    },
    {
        'NAME': 'django.contrib.auth.password_validation.CommonPasswordValidator',
    },
    {
        'NAME': 'django.contrib.auth.password_validation.NumericPasswordValidator',
    },
    {   # ↓これはカスタムバリデーション
        'NAME': 'utils.validators.CustomPasswordValidators',
    }
]
```
- パスワードバリデーションの実装
  - views.py
```python
from django.contrib.auth.password_validation import validate_password
from django.core.exceptions import ValidationError
# （省略）
    if user_form.is_valid() and profile_form.is_valid():
        user = user_form.save(commit=False)     # コミットする前
        try:
            validate_password(user_form.cleaned_data['password'], user)     # ココ
        except ValidationError as e:
            user_form.add_error('password', e)      # エラーならform にadd_errorを追加する
            return render(request, 'user/registration.html', context={
                'user_form' : user_form,
                'profile_form' : profile_form
            })
```
- パスワードカスタムバリデーション
  - utilsを追加
    - utils.validators.pyなど
      - ↓のクラスをsettings.pyに入れる
```python
from django.core.exceptions import ValidationError
import re       # 正規表現を使えば楽

class CustomPasswordValidators():

    def __init__(self) -> None:
        pass

    # validateメソッドでチェックし、ValidationErrorを発生させる
    def validate(self, password, user=None):
        if all((re.search('[0-9]', password), re.search('[a-z]', password), re.search('[A-Z]', password))):
            return
        else:
            raise ValidationError('パスワードには数字と大文字英字と小文字の英字を含めてください')
```

- userのカスタマイズ
  - https://docs.djangoproject.com/ja/3.2/topics/auth/customizing/
    - デフォルトのauth.userに項目を追加したり、管理画面の項目を変えたりするのに使用する
      - ログインをusernameからemailに変更したり、など

    - models.py
      - AbstractBaseUserを継承して、ユーザークラスを、
      BaseUserManagerを継承して、ユーザーマネージャークラスをそれぞれカスタマイズする
```python
from django.db import models
from django.contrib.auth.models import (
    BaseUserManager, AbstractBaseUser, PermissionsMixin
)

class UserManager(BaseUserManager):     # ユーザーマネージャークラス

    def create_user(self, username, email, password=None):
        if not email:
            raise ValueError('Enter Email!')
        user = self.model(
            username = username,
            email = email
        )
        user.set_password(password)
        user.save(using=self._db)
        return user
    
    def create_superuser(self, username, email, password=None): # python manage.py createsuperuserコマンドに使用する設定を定義
        user = self.model(
            username = username,
            email = email
        )
        user.set_password(password)
        user.is_staff = True
        user.is_active = True
        user.is_superuser = True
        user.save(using=self._db)
        return user

class User(AbstractBaseUser, PermissionsMixin):     # ユーザークラス
    # password, last_loginはAbstractBaseUserに定義済
    username = models.CharField(max_length=50)
    email = models.EmailField(max_length=255, unique=True)  # usernameからemailをユニークキーに変更
    is_active = models.BooleanField(default=False)
    is_staff = models.BooleanField(default=False)
    website = models.URLField(null=True)
    picture = models.FileField(null=True)
    
    USERNAME_FIELD = 'email'    # このテーブルのレコードを一意に識別する項目...usernameからemailに変更したい場合
    REQUIRED_FIELDS = ['username']  # スーパーユーザー作成時に入力する

    objects = UserManager()     # マネージャークラスの指定

    def __str__(self) -> str:
        return self.email       # 管理画面の表示に使用される
```

- settings.py
    ROOT_URLCONFを追加
```python
ROOT_URLCONF = 'CustomizedUser.urls'
```

- 管理画面を変更するにはforms.pyを定義
  - forms.py
```python
from django import forms
from django.contrib.auth.forms import ReadOnlyPasswordHashField
from django.contrib.auth import get_user_model
from django.core.exceptions import ValidationError
from django.db.models import fields

User = get_user_model()     # settings.pyで指定したユーザーモデル

class UserCreationForm(forms.ModelForm):        # ユーザー追加時の設定
    password = forms.CharField(label='パスワード', widget=forms.PasswordInput)
    confirm_password = forms.CharField(label='パスワード再入力', widget=forms.PasswordInput)
    
    class Meta:
        model = User
        fields = ('username', 'email', 'password')

    def clean(self):
        cleaned_data = super().clean()
        password = cleaned_data['password']
        confirm_password = cleaned_data['confirm_password']
        if password != confirm_password:
            raise forms.ValidationError('パスワードが一致しません')

    def save(self, commit=False):
        user = super().save(commit=False)
        user.set_password(self.cleaned_data["password"])
        user.save()
        return user

class UserChangeForm(forms.ModelForm):        # ユーザー変更時の設定
    password = forms.CharField(label='パスワード', widget=forms.PasswordInput)
    website = forms.URLField(required=False)
    picture = forms.FileField(required=False)

    class Meta:
        model = User
        fields = ('username', 'email', 'password', 'is_staff', 'is_active', 'is_superuser', 'website', 'picture')

    def clean_password(self):
        return self.initial['password'] # すでに登録されているパスワードを返す
```

- 管理画面の定義
  - admin.py
```python
from django.contrib.auth.admin import UserAdmin
from django.contrib.auth import  get_user_model
from .forms import User, UserChangeForm, UserCreationForm

User = get_user_model()

class CustomizeUserAdmin(UserAdmin):        # UserAdminを継承
    form = UserChangeForm       # ユーザ編集画面で使用するフォーム
    add_form = UserCreationForm     # ユーザー追加画面で使用するフォーム

    # 一覧画面で表示
    list_display = ('username', 'email', 'is_staff')

    # 編集画面で表示
    fieldsets = (
        ('ユーザー情報', {'fields':('username', 'email', 'password', 'website', 'picture')}),
        ('パーミッション', {'fields':('is_staff', 'is_active', 'is_superuser')}),
    )

    # ユーザー追加時に表示
    add_fieldsets = (
        ('ユーザー情報', {'fields':('username', 'email', 'password', 'confirm_password')}),
    )

admin.site.register(User, CustomizeUserAdmin)      # 管理画面に登録
```

- 管理画面のカスタマイズ
  - models.py
```python
class Students(models.Model):
    name = models.CharField(max_length=20, verbose_name='名前')     # verbose_nameで項目名の表示を変更
    age = models.IntegerField()
    score = models.IntegerField()
    school = models.ForeignKey(
        'Schools', on_delete=models.CASCADE
    )

    def __str__(self) -> str:       # 一覧表示の表示方法
        return f'{self.name} : {self.age}'

    class Meta:
        db_table = 'students'
        verbose_name_plural = '生徒'        # verbose_name_pluralでテーブル表示名の変更
        ordering = ('-score', )     # 一覧表示のデフォルト並び順

class Schools(models.Model):
    name = models.CharField(max_length=20, verbose_name='学校名')

    def __str__(self) -> str:
        return f'{self.name}'

    class Meta:
        db_table  ='schools'
        verbose_name_plural = '学校'
```

- admin.py
```python
class StudentsAdmin(admin.ModelAdmin):
    # 追加項目の並び順の変更
    fields = ('name', 'score', 'age', 'school')
    # 一覧画面の表示変更
    list_display = ('id', 'name', 'score', 'age', 'school')
    # 一覧画面のリンクの項目
    list_display_links = ('name', )
    # 一覧画面の検索
    search_fields = ('name', )
    # 一覧画面にフィルター
    list_filter = ('school', 'age')
    # 一覧画面で編集
    list_editable = ('age', 'score', 'school')

admin.site.register(Students, StudentsAdmin)    # 管理画面に追加するmodel. 引数2に管理クラスを指定する

@admin.register(Schools)        # もしくは@admin.registerを管理クラスの前に指定する
class SchoolsAdmin(admin.ModelAdmin):
    # 外部キー先の件数
    list_display = ('name', 'student_count')
    
    def student_count(self, obj):       # 外部キーの場合の_set項目を設定できる
        count = obj.students_set.count()
        return count

    student_count.short_description = '生徒数'      # 関数のshort_descriptionで項目タイトルを変更
```

- 管理画面のhtml, cssをカスタマイズ
    - gitから本体のファイルをコピーして変更

- templates/admin/base_site.html

https://github.com/django/django/blob/main/django/contrib/admin/templates/admin/base_site.html  
サイトタイトルを変更
{% raw %}
```html
{% extends "admin/base.html" %}

{% block title %}{% if subtitle %}{{ subtitle }} | {% endif %}{{ title }} | {{ site_title|default:_('Django site admin') }}{% endblock %}

{% block branding %}
<h1 id="site-name"><a href="{% url 'admin:index' %}">サイト名を変更</a></h1>    <!-- ←ココ -->
{% endblock %}

{% block nav-global %}{% endblock %}
```
{% endraw %}
- cssは、static/admin/css/base.cs

https://github.com/django/django/blob/main/django/contrib/admin/static/admin/css/base.css  
※cssをブラウザで反映させるにはctrl+F5


### ユーザの本登録

- ユーザ登録して、メール認証をもって本登録などをする場合は、user.is_activeを最初はFalseで登録して、認証後にTrueにする仕組みにする
- そのとき、UUIDというトークンを発行し、userと外部結合する（UUID, 有効期限）をもったテーブル（model）を準備する

    - models.py
```python
from uuid import uuid4
class UserActivateTokens(models.Model):

    token = models.UUIDField(db_index=True) # UUIDField, インデックスつける
    expired_at = models.DateTimeField()
    user = models.ForeignKey(
        'Users', on_delete=models.CASCADE
    )

    class Meta:
        db_table = 'user_activate_tokens'
```

- シグナルとreceiverの利用
    - models.py

    receiverの第1引数は post_save:save後 / pre_save / pre_delete / post_delete などがある
    第2引数に対象modelをトル
```python
from django.db.models.signals import post_save
from django.dispatch import receiver
from datetime import datetime, timedelta

@receiver(post_save, sender=Users)      # デコレータ関数のreceiver
def publish_token(sender, instance, **kwargs):  # ココの引数は既定
    user_activate_token = UserActivateTokens.objects.create(        # userがsaveされたらUserActivateTokensをcreateする処理
        user=instance, token=str(uuid4()), expired_at=datetime.now() + timedelta(days=1)
    )
    # メールでURLを送る方がよい     # そしてメールを送る　実際にメールするときはmodelに書く？？
    print(f'http://127.0.0.1:8000/accounts/activate_user/{user_activate_token.token}')
```
- models.Manager

    （ストアドのようなものか？）model.Modelにテーブルの定義を記述するのと、model.Managerに対象テーブルのデータの操作を記述する
    - models.py
```python
class UserActivateTokensManager(models.Manager):    # models.Managerを継承

    def activate_user_by_token(self, token):    # 関数を追加 userのis_activeをTrueにする処理
        user_activate_token = self.filter(
            token=token,
            expired_at__gte=datetime.now()
        ).first()
        user = user_activate_token.user
        user.is_active = True
        user.save()

class UserActivateTokens(models.Model):

    token = models.UUIDField(db_index=True)
    expired_at = models.DateTimeField()
    user = models.ForeignKey(
        'Users', on_delete=models.CASCADE
    )

    objects = UserActivateTokensManager()   # objects=でMangerを定義

    class Meta:
        db_table = 'user_activate_tokens'
```

- ページ遷移でメッセージの受け渡し
    - 例えばログイン後に遷移先のページでログイン成功を見せるなど

    →messagesを使用する  
    messages.debug / info / success / warning / error  
    レベルはsettings.py  MESSAGE_LEVEL を使用する  
    - views.py
```python
from django.contrib import messages

# ログイン認証後
        if user:
            if user.is_active:
                login(request, user)
                messages.success(request, 'ログイン完了しました。')
                return redirect('accounts:home')
            else:
                messages.warning(request, 'ユーザがアクティブでありません')
        else:
            messages.warning(request, 'ユーザかパスワードが間違っています')
```
- html側

messagesがあれば表示する
```html
{% if messages %}
  {% for message in messages %}
    <div>{{ message.message }}</div>
  {% endfor %}
{% endif %}
```

- ModelFormで更新
  - forms.py
```python
class UserEditForm(forms.ModelForm):    # ModelFormを継承
    username = forms.CharField(label='名前')
    ...
```
- views.py
```python
@login_required
def user_edit(request):
    user_edit_form = forms.UserEditForm(request.POST or None, request.FILES or None, instance=request.user)     # instance=でmodelのインスタンスを渡す. userはrequest.userで取れる
    if user_edit_form.is_valid():
        messages.success(request, '更新完了しました。')
        user_edit_form.save()
    return render(request, 'accounts/user_edit.html', context={
        'user_edit_form': user_edit_form,
    })
```

- パスワード変更した場合

update_session_auth_hashでセッションを更新する必要がある
  - views.py
```python
from django.contrib.auth import update_session_auth_hash

@login_required
def change_password(request):
    password_change_form = forms.PasswordChangeForm(request.POST or None, instance=request.user)
    if password_change_form.is_valid():
        try:
            password_change_form.save()
            messages.success(request, 'パスワード更新完了しました。')   
            update_session_auth_hash(request, request.user)     # ココ
        except ValidationError as e:
            password_change_form.add_error('password', e)
```


### 掲示板作成に見るアプリの作り方

- Model操作はModelクラスにobject=models.manager（を継承したクラス）を追加し、views側でmodelの操作を行わないようにする
- 追加・更新・削除はview / form / html をそれぞれ対応するように分けて、シンプルに

  formを追加と更新で共有することはありうる  
  削除用のformはfieldが何もないformになる（modelformだからMetaでmodel=を指定するだけ）htmlはsubmitボタンだけ
- URL引数で操作するページ（掲示版でいえば掲示板idを引数とした編集ページ） は、直接入力されたら404を発生させるなどの考慮を入れること

  パラメータチェックの基本
- forms.ModelForm.instance という使い方
    - models.py
    modelにはtiltle / userという項目がある
```python
class Themes(models.Model):

    title = models.CharField(max_length=255)
    user = models.ForeignKey(
        'accounts.Users', on_delete=models.CASCADE
    )
```
- forms.py

formには、user はないがModelFormとしてmodelをThemesで指定する
```python
class CreateThemeForm(forms.ModelForm):
    title = forms.CharField(label='タイトル')

    class Meta:
        model = Themes
        fields = ('title',)
```
- views.py

    このとき、ModelFormのuserに設定するときは、modelform.instance.項目名　としてsave() 前に設定する
```python
def create_theme(request):
    create_theme_form = forms.CreateThemeForm(request.POST or None)
    if create_theme_form.is_valid():
        create_theme_form.instance.user = request.user      # ←ココ
        create_theme_form.save()
```

- bootstrapによるカラム的配置

https://www.tohoho-web.com/bootstrap/grid.html  
.col-{n} は、画面の横幅を12個のカラムに分割し、そのうちの何個分を使用するかを指定します。
  - html
```html
<div class="col-1 offset-1">        <!-- カラム1つ分が写真 -->
  {% if comment.user.picture %}
   <img style="float:left;" width="50px" height="50px" src="{{ comment.user.picture.url }}">
  {% endif %}
</div>
<div class="col-8 offset-2">    <!-- カラム8コ分が名前 -->
  <p>名前: {{ comment.user.username }}</p>
  <p>{{ comment.comment | linebreaks }}</p>
</div>
<div class="col-10 offset-1">   <!-- カラム10コ分が区切り行 -->
<hr>
</div>
```

- template で for を使うときに連番が振れる
    - html
```html
  {% for theme in themes %}
    <tr>
        <td>{{ forloop.counter }}</td>  <!-- ←コレ -->
        <td>{{ theme.user }}</td>
    </tr>
  {% endfor %}
```

- クラスベースのView
  - これまでは関数ベースのView
  - クラスベースの方が一般的？
  - viewにたくさんコードを記述しなくて済む
  - 用途に応じて継承するViewが用意されている
```python
from django.views.generic.base import View, TemplateView, RedirectView
from django.views.generic.detail import DetailView
from django.views.generic.list import ListView
from django.views.generic.edit import CreateView, FormView, UpdateView, DeleteView, FormView
```
- 以降で使う共通のもの
  - models.py
```python
from django.db import models
from django.urls import reverse_lazy

class BaseModel(models.Model):
    create_at = models.DateTimeField()
    update_at = models.DateTimeField()    

    class Meta:
        abstract = True

class Books(BaseModel):
    name = models.CharField(max_length=255)
    description = models.CharField(max_length=1000)
    price = models.IntegerField()

    class Meta:
        db_table = 'books'

    # def get_absolute_url(self):       # ←modelが更新された後の遷移先をmodelにも書ける
    #     return reverse_lazy('store:detail_book', kwargs={'pk':self.pk})
```
        - urls.py
```python
from django.urls import path
from . import views
# from django.views.generic.base import TemplateView
from django.views.generic.base import RedirectView

app_name = 'store'

urlpatterns = [
    path('index/', views.IndexView.as_view(), name='index'),
    # path('home/', TemplateView.as_view(template_name='home.html'), name='home'),
    path('home/<name>', views.HomeView.as_view(), name='home'),
    path('detail_book/<int:pk>', views.BookDetailView.as_view(), name='detail_book'),
    path('list_books/', views.BookListView.as_view(), name='list_books'),
    path('list_books/<name>', views.BookListView.as_view(), name='list_books'),
    path('add_book/', views.BookCreateView.as_view(), name='add_book'),
    path('edit_book/<int:pk>', views.BookUpdateView.as_view(), name='edit_book'),
    path('delete_book/<int:pk>', views.BookDeleteView.as_view(), name='delete_book'),
    path('form_book/', views.BookFormView.as_view(), name='form_book'),
    path('google', RedirectView.as_view(url='https://google.com')),     # リダイレクトはviewに書かなくてもurls.pyで完結する書き方もある
    path('book_redirect_view/', views.BookRedirectView.as_view() , name='book_redirect_view')
]
```
        - forms.py
```python
from django import forms
from django.db.models import fields
from .models import Books
from datetime import datetime

class BookForm(forms.ModelForm):

    class Meta:
        model = Books
        fields = ['name', 'description', 'price']

    def save(self, *args, **kwargs):        # テーブル共通項目をもつような場合は、modelでrecieverを書くか、このようにFormに書く
        obj = super(BookForm, self).save(commit=False)
        obj.create_at = datetime.now()
        obj.update_at = datetime.now()
        obj.save()
        return obj

class BookUpdateForm(forms.ModelForm):

    class Meta:
        model = Books
        fields = ['name', 'description', 'price']

    def save(self, *args, **kwargs):
        obj = super(BookUpdateForm, self).save(commit=False)
        # obj.create_at = datetime.now()
        obj.update_at = datetime.now()
        obj.save()
        return obj
```

- View（クラス）
```python
from django.views.generic.base import View, TemplateView, RedirectView

class IndexView(View):
    def get(self, request, *args, **kwargs):        # GETとPOSTのメソッドを書く
        book_form = forms.BookForm()
        return render(request, 'index.html', context={
            'book_form': book_form,
        })

    def post(self, request, *args, **kwargs):
        book_form = forms.BookForm(request.POST or None)
        if book_form.is_valid():
            book_form.save()
        return render(request, 'index.html', context={
            'book_form': book_form,
        })
```

- TemplateView

固定ページの表示に利用
```python
class HomeView(TemplateView):
    template_name = 'home.html'

    def get_context_data(self, **kwargs):       # 一部動的ページにしたい場合に利用
        context = super().get_context_data(**kwargs)
        context['name'] = kwargs.get('name')
        context['time'] = datetime.now()
        return context
```

    - DetailView
```python
from django.views.generic.detail import DetailView

class BookDetailView(DetailView):
    model = Books
    template_name = 'book.html'     

    def get_context_data(self, **kwargs):
        context = super().get_context_data(**kwargs)
        return context
```
        - html
```html
<p>{{ object.name }}</p>        <!-- ←objectにmodelの値が格納されている -->
<p>{{ object.description}}</p>
<p>{{ object.price}}円</p>
```

    - ListView
```python
from django.views.generic.list import ListView

class BookListView(ListView):
    model = Books
    template_name = 'book_list.html'

    def get_queryset(self) :        # 一覧情報を動的に変える. レコードの抽出 / 並び順 など
        qs = super(BookListView, self).get_queryset()
        if 'name' in self.kwargs:
            qs = qs.filter(name__startswith = self.kwargs['name'])
        qs = qs.order_by('-id')
        return qs
```
        - html
{% raw %}
```html
<table>
    <tbody>
    {% for object in object_list %}     <!-- object_listにデータが格納されている -->
    <tr>
    <td>名前：<a href="{{ object.get_absolute_url }}"> {{ object.name }} </a></td>
    <td>説明：{{ object.description}}</td>
    <td><a href="{% url 'store:edit_book' object.id %}">編集</a></td>
    <td><a href="{% url 'store:delete_book' object.id %}">削除</a></td>
    </tr>
    {% endfor %}
    </tbody>
</table>
```
{% endraw %}

- CreateView / UpdateView / DeleteView
```python
from django.contrib.messages.views import SuccessMessageMixin
from django.views.generic.edit import CreateView, UpdateView, DeleteView, FormView

class BookCreateView(CreateView):
    model = Books
    fields = ['name', 'description', 'price']       # 項目を指定するか、form_class = で formを指定する
    template_name = 'add_book.html'

    # success_url = reverse_lazy('store:list_books')        # 更新後の遷移先を指定. ←完全固定の場合

    def get_success_url(self):          # ←動的に遷移先を変える場合
        return reverse_lazy('store:detail_book', kwargs={'pk':self.object.pk})

    def form_valid(self, form):
        form.instance.create_at = datetime.now()
        form.instance.update_at = datetime.now()
        return super(BookCreateView, self).form_valid(form)

    def get_initial(self, **kwargs):
        initial = super(BookCreateView, self).get_initial(**kwargs)
        initial['name'] = 'sample'
        return initial


class BookUpdateView(SuccessMessageMixin, UpdateView):
    model = Books
    template_name = 'update_book.html'
    form_class = forms.BookUpdateForm       # formを指定するパターン
    success_message = '更新に成功しました'

    def get_success_url(self):
        return reverse_lazy('store:edit_book', kwargs={'pk':self.object.pk})

    def get_success_message(self, cleaned_data):        # メッセージの受け渡し方 SuccessMessageMixinを使用する
        # return super().get_success_message(cleaned_data)
        return cleaned_data.get('name') + 'を更新しました'

    def post(self, request, *args, **kwargs):
        book = self.get_object()        # get_object() で自分のmodelを返す
        print(self.get_object())        # printでいろいろ試すしかない
        return super(BookUpdateView, self).post(request, *args, **kwargs)

class BookDeleteView(DeleteView):
    model = Books
    template_name = 'delete_book.html'

    def get_success_url(self):
        return reverse_lazy('store:list_books')

```
- html
{% raw %}
```html
{% if messages %}       <!-- メッセージはmessages -->
{% for message in messages %}
    <p> {{ message.message }} </p>
{% endfor %}
{% endif %}
<form method="POST">
{% csrf_token %}
{{ form.as_p }}     <!-- データはform -->
<input type="submit" value="更新">
</form>
{% endraw %}

```

- FormView

    フォームを使う場合（CreateView, UpdateView, DeleteViewと何がちがう？全部FormViewでいい気がする...）
```python
from django.views.generic.edit import CreateView, FormView, UpdateView, DeleteView

class BookFormView(FormView):
    form_class = forms.BookForm
    template_name = 'form_book.html'

    def get_success_url(self):
        return reverse_lazy('store:list_books')

    def form_valid(self, form):
        if form.is_valid():
            form.save()
        return super(FormView, self).form_valid(form)

    def get_initial(self):
        initial = super(BookFormView, self).get_initial()
        initial['name'] = 'form sample'
        return initial
```
  - html側
{% raw %}
```html
<form method="POST">
{% csrf_token %}
{{ form.as_p }}
<input type="submit" value="保存">
</form>
{% endraw %}
```

- RedirectView
```python
class BookRedirectView(RedirectView):
    url = 'https://google.com'      # 固定の方法

    def get_redirect_url(self, *args, **kwargs):        # 動的に変えたい方法.
        book = Books.objects.first()
        return reverse_lazy('store:edit_book', kwargs={'pk':book.pk})
```


- クラスベースビューでログイン/ログアウトビュー

これを使うとほとんど自作する必要がない
- models.py

    これはユーザー情報のカスタマイズのところと同じ
```python
from django.contrib.auth.models import (
    BaseUserManager, AbstractBaseUser, PermissionsMixin
)
from django.urls import reverse_lazy

class UserManager(BaseUserManager):

    def create_user(self, username, email, password=None):
        if not email:
            raise ValueError('Enter Email!')
        user = self.model(
            username = username,
            email = email
        )
        user.set_password(password)
        user.save(using=self._db)
        return user
    

class Users(AbstractBaseUser, PermissionsMixin):
    username = models.CharField(max_length=50)
    email = models.EmailField(max_length=255, unique=True)
    is_active = models.BooleanField(default=True)
    is_staff = models.BooleanField(default=False)
    # website = models.URLField(null=True)
    # picture = models.FileField(null=True)
    
    USERNAME_FIELD = 'email'    # このテーブルのレコードを一意に識別する項目
    REQUIRED_FIELDS = ['username']  # スーパーユーザー作成時に入力する

    objects = UserManager()

    def get_absolute_url(self):
        return reverse_lazy('accounts:home')
```

- forms.py

    これも自作する場合と同じ
```python
from django.contrib.auth.forms import AuthenticationForm, UsernameField

class UserLoginForm(AuthenticationForm):
    username = forms.EmailField(label="メールアドレス")
    password = forms.CharField(label="パスワード", widget=forms.PasswordInput())
    remember = forms.BooleanField(label='ログイン状態を保持する', required=False)
```
- views.py

    これがたった↓だけ
```python
from django.contrib.auth.mixins import LoginRequiredMixin
from django.contrib.auth.views import LoginView, LogoutView

class UserLoginView(LoginView):
    template_name = 'user_login.html'
    authentication_form = UserLoginForm

    def form_valid(self, form):
        remember = form.cleaned_data.get('remember')    # ログイン状態を保持するチェックを追加した場合のセッション期間延長を追加する処理
        if remember:
            self.request.session.set_expiry(1200000)
        return super().form_valid(form)

class UserLogoutView(LogoutView):
    pass

class UserView(LoginRequiredMixin, TemplateView):   # ログイン要求するviewクラスにはLoginRequiredMixinを入れる

    template_name = 'user.html'
```
- settings.py
```python
LOGIN_URL = '/accounts/user_login'      # ログイン必須のviewを開かれた場合にリダイレクトするページを指定
LOGIN_REDIRECT_URL = '/accounts/home'   # ログイン後のページを指定
LOGOUT_REDIRECT_URL = '/accounts/user_login'    # ログアウト後のページを指定

SESSION_COOKIE_AGE = 5      # ログイン状態保持　を作りたい場合のデフォルト有効期間（秒）
```


### ECサイトアプリに見る応用内容メモ

- modelはメンテナンスできるように 管理画面に表示
- cacheの利用
- modelで正の数値の定義
  - `models.PositiveIntegerField()`
- modelでユニークかどうかのチェック
  - `Model.validate_unique(exclude=None)`
- 外部キーオブジェクトを参照 _set の利用
  - `for item in cart.cartitems_set.all():`
- bootstrapのボタン
  - https://www.tohoho-web.com/bootstrap/buttons.html
- modelへのインサートはmodelにmodels.manager()に書く
- get_object_or_404の引数は、object, 引数1, 引数2, ...
- メソッド全体でトランザクションを保つ
  - `@transaction.atomic`

## 環境関連

### gunicorn

- install
```bash
pip install gunicorn
```

- start
```bash
gunicorn [プロジェクト名].wsgi
```

### secret_key 生成
```bash
python -c 'from django.core.management.utils import get_random_secret_key; print(get_random_secret_key())'
```

## その他

### スタティックファイルのプロダクション
```bash
python manage.py collectstatic
```

### migrateが失敗する
- dbを直接作ったりして、djangoのmigrationsとずれていると
```bash
python manage.py migrate AppName
```
が失敗する. ↓こんなメッセージ.
```
duplicate key value violates unique constraint "auth_permission_pkey"
DETAIL:  Key (id)=(1) already exists.
```

- db側にmigrationした状態を持っている模様.
	- postgresの場合、以下のテーブルのidを更新する
```sql
SELECT setval('django_migrations_id_seq', (SELECT MAX(id) FROM django_migrations));
SELECT setval('django_content_type_id_seq', (SELECT MAX(id) FROM django_content_type));
SELECT setval('auth_permission_id_seq', (SELECT MAX(id) FROM auth_permission));
```

### migrateで生成するテーブル（postgresqlの場合）
- idはsequence型だから、デプロイ環境などでmigrateせず、sqlで直接データベースを作成する場合、sequenceの生成も忘れない
```sql
create sequence next_movies_id_seq start with 1;
CREATE TABLE IF NOT EXISTS public.next_movies (
id bigint NOT NULL DEFAULT nextval('next_movies_id_seq'::regclass),
...
```
