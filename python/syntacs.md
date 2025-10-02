---
title: Python 文法メモ
lang: ja
---

# Python文法

このメモは[Udemy講座](https://www.udemy.com/share/103OHY/)を受講して学んだ記録です。  
作者には感謝します。


## コメント
- コメント `#`
- 複数行コメント `"""    """` （ダブルクォート3つ）
- 変数代入

```python
a = b = 'aaa'
```
とするとaにもbにも'aaa'が入る

## プリント

```python
print('test {}'.format(a))  # {}の順で変数を.format()に入れる
print(f'{val}')  # f''で{}に直接変数を入れる
```
### プリントで前ゼロ編集

```python
number = 42
padded_number = f"{number:05}"
print(padded_number)  # 出力: 00042
```

## 標準入力

```python
a = input('入力してください')
```

## コマンドライン引数

```python
import sys
print(sys.argv)
# python demo.py one two threeで実行すると['demo.py', 'one', 'two', 'three']
```
↑1つ目はpythonファイル名なので注意！

## 定数
- 定数という概念はない　全部大文字で定義するコーディング規約　`KING_OF_ANIMAL`など

- if文は最後にコロン`:`を入れる

```python
if age < 20:
    print('aaa')
else:
    print('bbb')
```

## 型
- 数値型は`int`=整数型と`float`=浮動小数点型の2種類しかない

## 演算
- 加：`+`   減：`-`    乗：`*`  除：`/`
- 除算結果は浮動小数点になる。`//`で小数点以下切り捨ての除算になる

## 文字列型
- `val * n`：n回くりかえし
- `val[3]`：先頭3文字目を取り出す
- `val[-1]`：後ろから1文字目を取り出す
- `""" """`：改行入れた文字列を格納
- `val + val`：文字列連結
- `val[1:]`：1文字目以降
- `val[:3]`：3文字目まで
- `val[1:10:2]`：1文字目から10文字目までを1つ飛ばしで
- `val.find`：サーチ
- `val.rfind`：逆サーチ
- `val.index`：サーチ（みつからない場合に例外）
- `val.rindex`：逆サーチ（みつからない場合に例外）

### トリム
- `str.strip()`

### 置換
- `str.replace()`

### 前ゼロ編集
- `str.zfill()`

```python
number = 42
padded_number = str(number).zfill(5)
print(padded_number)  # 出力: 00042
```

- `str.rjust()`

```python
number = 42
padded_number = str(number).rjust(5, '0')
print(padded_number)  # 出力: 00042
```

紛らわしい気がするが、`rjust`が左に文字埋め。`ljust`が右に文字埋め。

### 数値のカンマ表示

```python
number = 1234567890
formatted_number = f"{number:,}"
print(formatted_number)  # 出力: 1,234,567,890
```

## 型変換
- `str()`
- `int()`
- `float()`

## リスト型
- 初期値：`list_ = [1,2,3,4]`
- 参照：`list_[1]`
- 参照（後ろから）：`list_[-1]`
- 参照（途中）：`list_[1:2]`
- メソッド：`append`（値の追加）/`extend`（複数の値の追加）/`insert`/`clear`/`remove`/`pop`/`count`（引数の値に一致する数）/`sort`/`reverse`
- 長さ：`len(list_)`

- `extend`はjavascriptでいうところの `...` のスプレッド構文

```python
l = [1, 2, 3]
ll = [4, 5, 6]
l.append(ll)
# -> [1, 2, 3, [4, 5, 6]]

l.extend(ll)
# -> [1, 2, 3, 4, 5, 6]
```

## 辞書型
- 初期値：`dic = {'key':1, 'key2':2}`
- 参照：`dic['key']` / `dic.get('key')` / `dic.get('key', 'ない場合の値')`
- メソッド：`keys` / `values` / `items` / `update` / `popitem` / `pop` / `clear` 
- 削除：`del dic`

### 辞書リストのソート
参考：[Python公式ドキュメント - ソート HOW TO](https://docs.python.org/ja/3/howto/sorting.html#sortinghowto)

例：

```python
post_views_count_list = [
    {'id': 1850, 'post_views_count': '2'}, 
    {'id': 1772, 'post_views_count': '2'}, 
    {'id': 1852, 'post_views_count': '1'}
]
```

これを`post_views_count`の降順でソートするには：

```python
sorted_list = sorted(post_views_count_list, key=lambda x: int(x['post_views_count']), reverse=True)
```

### 辞書型の展開
`dic = {'key':1, 'key2':2}`の`**dic`は、`key1=1, key2=2`という関係の展開になるので、`def func(key, key2)`という関数に、`func(**dic)`と渡せたり、`"key1の値は{key1}、key2の値は{key2}".format(**dic)`という使い方ができる。

## タプル型
- 値の変更できないリスト
- 定数がないので、その代用になる
- 初期値：`tup = ('a', 'b', 'c')`
- 型変換：`tuple()`
- メソッド：`count` / `index` ...引数の値の数 / index

## セット型
- リストとタプルに似ているが、ユニーク。順序を保持しない
- 初期値：`st = {'a', 'b', 'c'}`
- 判定：`in` / `not in`
- 値操作のメソッド：`add` / `remove` / `discard` / `pop`
- セット自体の操作のメソッド：`union` / `intersection` / `difference` / `symmetric_difference`
- 演算子表現では `+`, `|`, `-`, `^`

- `update(*others)` / `set |= other | ...`：全ての other の要素を追加し、 set を更新します。

- `intersection_update(*others)` / `set &= other & ...`：元の set と全ての other に共通する要素だけを残して set を更新します。

- `difference_update(*others)` / `set -= other | ...`：other に含まれる要素を取り除き、 set を更新します。

- `symmetric_difference_update(other)` / `set ^= other`：どちらかにのみ含まれて、共通には持たない要素のみで set を更新します。


## if
- `if` / `if elif` / `if else`
- `if not`
- `if all` / `if any`

## 論理演算子
- `==` / `!=` / `>` / `<` / `<=` / `>=`


## for
- `for i in range()`
  - `range(5)`：0..4
  - `range(2, 5)`：2..4
  - `range(2, 10, 2)`：2, 4, 6, 8

- `for _ in range()`：単にrangeの回数分繰り返す
- `for x in list / tuple / dictionary`
- `for else`：elseのあとが繰り返し処理後に実行される。breakで抜けたときは実行されない


## enumerate
- インデックスと値を同時に取得

```python
for index, value in enumerate(list):
    pass
```

## zip
- 複数のリストを取得する

```python
for v1, v2 in zip(list1, list2):
    pass
```

## while

```python
count = 0
while count < 10:
    print(count)
    count += 1
```
- `while else`：elseのあとが繰り返し処理後に実行される。breakで抜けたときは実行されない

## ループの脱出
- `continue` / `break`

## セイウチ演算子
変数の代入と評価を同時に実行する

```python
# 通常の方法
num = 10
if num > 5:
    print(num)

# セイウチ演算子を使用した場合
if (num := 10) > 5:
    print(num)
```


## 例外

```python
try:
    pass
except Exception:
    pass
else:
    pass
finally:
    pass
```
exceptの種類：`FileNotFoundError` / `ZeroDivisionError` / `IndexError` / `Exception`...

```python
raise  # 例外を起こす
```

## 正規表現
`re`モジュールを使う

```python
import re
```

正規表現検索

```python
test = 'hoge....'
match = re.search(r'id=(.+)', text)
if match:
    match_str = match.group(1)
```
↑で、"."に改行含めて検索させたい場合は、`re.search(r'', text, re.DOTALL)`と、DOTALLを指定する


## 関数

```python
def name():
    pass
```

- パラメータの型を明示：`def name(param: type)`
- 可変長引数　タプル：`def name(*param)`
- 可変長引数　辞書：`def name(**param)`
  - 呼び出し側：`name(dic1=1, dic2=2)` → `param={'dic1':1, 'dic2':2}`
  - 複数のタプル、複数の辞書は引数に定義できないが、タプルと辞書を1つずつは定義できる
- 複数返り値：`return a, b`→呼び出し側はタプルで返る。`a, b = name()` という書き方も可。 

## グローバル変数

```python
global val
val = 'hoge'
```
- 関数`id()`：メモリアドレスが確認できる

## inner関数/nonlocal変数

```python
def outer_func():
    def inner_func():  # 外からアクセスできない関数
        nonlocal var   # 外から書き換え可能な変数
        pass
```

## 文字列からクラスを取得

```python
target_class = globals().get('クラス名')
```

## ジェネレータ関数

```python
def generator_func():
    for n in range(5):
        yield n
        # yield までで実行が止まる
```
- `next` で実行する　またはfor の繰り返しで実行する
- `send()`：yieldで停止している箇所に値を送る
- `throw()`：例外を発生させる
- `close()`：ジェネレータを終了させる

何につかうか？...リストに大量のデータを格納したい場合に、yieldで停止しながら必要分だけメモリに格納するとき

```python
import sys
sys.getsizeof()  # メモリ使用量の表示
```

## サブジェネレータ関数
ジェネレータの中にジェネレータを作成すると、一番最初の呼び出し元のyieldが中のジェネレータをストップする

## 高階関数
関数をオブジェクトとして扱う

## ラムダ式
1行で関数を書く。無名関数

```python
l = lambda x: x * x
```
↓

```python
def func(x):
    return x * x
l = func(x)
```
と同義
引数は複数、引数のデフォルト値の指定も可

## 三項演算子
- ifを1行で書く

```python
x = 0 if y > 10 else 1
```

## 再帰
例：

```python
def sample(a):
    if a < 0:
        return
    else:
        print(a)
        sample(a - 1)
```

## リスト内包表記

```python
list_a = (1,2,3,'a',4)
list_b = [x*2 for x in list_a]  # list_aから取り出した x に2をかけた値をlist_bに格納 ...javascriptのArray.map()に相当
list_b = [x*2 for x in list_a if type(x) == int]  # list_aから取り出した値が数値ならlist_bに格納...javascriptのArray.filter()に相当
list_b = next((x for x in list_a if x == y), None)  # list_aから取り出した値がyと一致するものを返す...javascriptのArray.find()に相当
```
- 内包表記は連続できる

```python
data = [{'label': 'A', 'prob': 0.9}, {'label': 'B', 'prob': 0.8}]
flat_list = [item for d in data for item in (d['label'], d['prob'])]
print(flat_list)
# -> ["A", 0.9, "B", 0.8]
```
外側のループ`for d in data`に対して、さらに`for item in (d['label'], d['prob'])`でループして、先頭の`item`を返す

## デコレータ関数
例：

```python
def mydec(func):
    def wrapper(*args, **kwargs):
        print('*' * 100)
        func(*args, **kwargs)
        print('*' * 100)
    return wrapper

@mydec
def func_a(*args, **kwargs):
    print('func_aを実行')
    print(args)

@mydec
def func_b(*args, **kwargs):
    print('func_bを実行')
    print(args)
```
- `mydec`という共通関数の中の関数を書き換えることができる
- 共通関数の方をデコレータ関数とよび、書き換えたい関数の前に`@`でデコレータ関数を記述して作成する

## map関数
関数をリスト型変数に一括実行すること

例：

```python
def func(x):
    return x * 2

list_a = [1,2,3]
map_a = map(func, list_a)  # map_aに[2,4,6]が格納される

map_a = map(lambda x : x * 2, list_a)  # これも同義。ラムダ関数を使う
```
↑このときmap_aはmapクラスになる（list型ではない）

mapの第2引数以降はリスト型で渡す。func(x,y,z)なら `map(func, [1,2,3], [1,2,3], [1,2,3])`のようになる

## クラス定義

```python
class Class_name:
    def method_name(self, ...):  # インスタンスメソッドにはselfの引数が必要
        pass
```
リストにいれたclassを生成できる

```python
l = ["Apple", "Banana", Class_name]
myclass = l[2]()
```
インスタンスもリストに格納できる

`help()`でディスクリプションとメソッド/プロパティ一覧も取得できる

## インスタンス変数とクラス変数

```python
class SampleA():
    class_val = 'class val'  # クラス変数→インスタンス間で共有される

    def set_val(self):
        self.instance_val = 'instance val'  # インスタンス変数

ins = SampleA()
ins.set_val()
print(ins.instance_val)  # インスタンス変数へのアクセス
print(SampleA.class_val)  # クラス変数へのアクセス　方法1
print(ins.__class__.class_val)  # クラス変数へのアクセス　方法2
```

## クラスのコンストラクタ

```python
class SampleA():
    class_val = 'class val'

    def __init__(self, msg, name=None):
        self.msg = msg
        self.name = name

ins = SampleA('hello', 'world')
```

## デコンストラクタ

```python
class SampleA():
    class_val = 'class val'

    def __del__(self):
        pass

ins = SampleA('hello', 'world')
del ins  # インスタンスの削除 del ...デコンストラクタが呼ばれる
```
## インスタンスメソッド、クラスメソッド、スタティックメソッド

```python
class Human():
    class_name = 'Human'

    def print_name_age(self):  # インスタンスメソッド　引数にselfが必要
        pass

    @classmethod  # クラスメソッドであることを明記
    def print_class_name(cls, msg):
        # クラスメソッド　引数にclsが必要
        # クラスメソッド内ではインスタンス変数にアクセスできない
        # インスタンスしなくても（クラスから）呼び出せる
        pass

    @staticmethod  # スタティックメソッドであることを明記
    def print_msg(msg):
        # クラス変数もインスタンス変数も使わないメソッド
        # インスタンスしなくても（クラスから）呼び出せる
        pass
```

## 特殊メソッド

```python
class Human():
    def __str__(self):  # 例えばprintは必ずstrが呼ばれるので、printのときに返す値を定義できる
        return self.name + ',' + str(self.age) + ',' + self.phone_number

    def __eq__(self, other):  # ==とする条件を定義できる
        return (self.name == other.name) and (self.phone_number == other.phone_number)
    
    def __hash__(self):  # hashに使う値を定義できる
        return hash(self.name + self.phone_number)
    
    def __bool__(self):  # if判定に使用する条件を定義できる
        return True if self.age >= 20 else False
    
    def __len__(self):  # lenに使う値を定義できる
        return len(self.name)
```

## クラスの継承

```python
class Person:  # 親クラス
    def __init__(self, name, age) -> None:
        self.name = name
        self.age = age
    
    def greeting(self):
        print(f'hello {self.name}')
    
    def say_age(self):
        print(f'{self.age} years old')

class Employee(Person):  # 継承クラス
    def __init__(self, name, age, number) -> None:
        super().__init__(name, age)  # 親クラスのコンストラクタ
        self.number = number
    
    def say_number(self):
        print(f'my number is {self.number}')
    
    def greeting(self):  # オーバーライド. pythonには親クラスの引数を変えて同じメソッドを定義するオーバーロードはできない. やるなら オーバーライドクラスに初期値指定の引数を追加する
        super().greeting() 
        print(f'I\'m employee phone number = {self.number}')
```

## クラスの多重継承

```python
class ClassA:  # 親クラス1
    def __init__(self, name) -> None:
        self.a_name = name
    
    def print_hi(self):
        print('ClassA hi')

class ClassB:  # 親クラス2
    def __init__(self, name) -> None:
        self.b_name = name
    
    def print_hi(self):
        print('ClassB hi')

class NewClass(ClassA, ClassB):  # 継承クラス ClassAとClassBから継承
    def __init__(self, a_name, b_name, name) -> None:
        ClassA.__init__(self, a_name)  # 両方のクラスのコンストラクタ
        ClassB.__init__(self, b_name)
        self.name = name
    
    def print_hi(self):
        ClassA.print_hi(self)  # 両方の親クラスオーバーライド
        ClassB.print_hi(self)
        print('NewClass hi')
```
## メタクラス
クラスを再定義するクラス
主に定義が妥当か、クラスを検証することに用いられる

```python
class Meta1(type):  # メタクラス
    def __new__(metacls, name, bases, class_dict):
        if 'var' not in class_dict.keys():  # 変数があるかどうかのチェック
            raise MetaException('var not found')
        for base in bases:    # 継承クラスのチェック ...javaなどでfinal指定をすることの代替
            if isinstance(base, Meta1):
                raise MetaException('継承できない')

        return super().__new__(metacls, name, bases, class_dict)

class ClassA(metaclass=Meta1):  # メタクラスの継承
    a = '123'
    pass
```

## ポリモーフィズム

```python
from abc import abstractclassmethod, ABCMeta  # ABC=Abstract Base Classesの略 . 抽象クラスのこと

class Human(metaclass=ABCMeta):  # 抽象クラスの定義 metaclass = はメタクラス定義と同じ
    def __init__(self, name) -> None:
        self.name = name
    
    @abstractclassmethod 
    def say_something(self):  # 抽象メソッド
        pass

    def say_name(self):
        print(self.name)

class Woman(Human):
    def say_something(self):  # 継承クラスで抽象メソッドを具体化
        print(f'Woman name = {self.name}')
        return super().say_something()
```

## プライベート変数

```python
class Human:
    __class_val = 'Human'  # クラスのプライベート変数　__で始める

    def __init__(self, name, age) -> None:
        self.__name = name  # インスタンスのプライベート変数　__で始める
        self.__age = age

human = Human('John', 28)
print(human.__name)  # アクセス不可
print(human._Human__name)  # こう書くとアクセスできるが通常しない
```

## カプセル化、setter/getter
- 方法①

```python
class Human:
    def __init__(self, name, age) -> None:
        self.__name = name
        self.__age = age

    def get_name(self):  # get_[変数名] でgetterを定義
        print(f'getter name')
        return self.__name
    
    def set_name(self, name):   # set_[変数名]でsetterを定義
        print(f'setter name')
        self.__name = name
    
    name = property(get_name, set_name)  # プロパティを定義
    
human = Human('John', 28)
human.name = 'Paul'  # setterが呼ばれる
print(human.name)  # getterが呼ばれる
```

- 方法②

```python
class Human:
    def __init__(self, name, age) -> None:
        self.__name = name
        self.__age = age

    @property  # getterをpropertyで定義. メソッド名は変数名
    def name(self):
        print(f'getter name')
        return self.__name
    
    @name.setter   # setter を[変数名].setterで定義. メソッド名は変数名
    def name(self, name):
        print(f'setter name')
        self.__name = name
```
## ファイル入力

```python
file_path = 'test_prj/resources/input.csv'
f = open(file_path, mode='r', encoding='utf-8')  # ファイルオープン

content = f.read()  # 全読み

lines = f.readlines()   # 全読み、行を配列展開

line = f.readline()  # ライン読み
while line:
    print(line.rstrip('\n'))
    line = f.readline()

while (line := f.readline()):  # ライン読みのセイウチ演算子
    print(line.rstrip('\n'))

f.close()

with open(file_path, mode='r', encoding='utf-8') as f:  # with（C#のuse句と同じ）句でオープン＆読み出し
    lines = f.readlines()
    print(lines)
```

## ファイル出力

```python
file_path = 'test_prj/resources/output.csv'

f = open(file_path, mode='w', encoding='utf-8', newline='\n')  # mode は w:書き込み（ファイルがなければ作成） a:追記 b:バイナリで書き込み
f.write('あああ\n')

with open(file_path, mode='w', encoding='utf-8', newline='\n') as f:
    l = ['1', '2', '3']
    f.writelines(l)  # リストを結合して書き込み
```

## with句

```python
with クラス名 as a:
    pass
```

クラスの`__init__`と`__enter__`が呼び出し時に呼ばれ、脱出時に`__exit__`が呼ばれる

```python
class WithTest:
    def __init__(self) -> None:
        print('init called')
    
    def __enter__(self):
        print('enter called')
    
    def __exit__(self, exc_type, exc_val, traceback):
        print('exit called')

with WithTest() as t:
    print('withの中')
```

これを実行すると：

```
init called
enter called
withの中
exit called
```


## パッケージ import

`sys.path`にある別ファイルを読み込める

```python
import sub  # 直接書いてあるソースがこの時点で実行される

from sub import sample_a  # 特定の関数のみインポート

from sub import sample_a as sa, ClassA as ca  # 関数名とクラス名

from sub import *   # 普通やらない
from sub2 import *   # 同じ名前の関数がある場合は2つ目にある関数名が上書き

import dir1.base1  # ディレクトリを指定してファイル名を読み込む
```

ディレクトリの中に`__init__.py`というファイルを配置しておき、

```python
from .base1 import print_msg as base1_msg
from .base2 import print_msg as base2_msg
```
などと書くとそのディレクトリの中の関数を別名で定義しておくことができる

読み込み側は、

```python
from dir1 import *
base1_msg()
```
となる

`__init__.py`の中には、`*` が指定されたときの読み込まれる関数を定義できる

```python
__all__ = ['base2_msg']  # *でimportしたときに読み込まれる関数リスト
```

## JSON

`import json`でjsonモジュールを読み込んで使う。

- オブジェクトのjson文字列化

```python
json.dumps(['foo', {'bar': ('baz', None, 1.0, 2)}])
```
日本語が含まれる場合、`\u3000`などの文字コードになるため、`ensure_ascii=False`を指定する

```python
json.dumps(['foo', {'bar': ('baz', None, 1.0, 2)}], ensure_ascii=False)
```

- json文字列のオブジェクト化

```python
json.loads('["foo", {"bar":["baz", null, 1.0, 2]}]')
```