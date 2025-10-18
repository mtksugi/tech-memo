---
title: Python ライブラリメモ
lang: ja
---

# Pythonライブラリ関連

## Flask
- 起動

```bash
flask run
```
- ポート指定で起動

```bash
flask run --port 5001
```
デフォルトは5000。  
ちなみに5000は色々競合おおい。MacはAirPlayレシーバが5000がデフォルトなのでMacで開発は注意。  
競合しているとCORSにひっかかる。

## pprint
- 辞書型オブジェクトを改行、インデントして表示するのに便利

```python
from pprint import pprint

pprint(data)
# {'address': {'city': 'Wonderland', 'street': '123 Main St', 'zipcode': '12345'},
#  'age': 30,
# 'hobbies': ['reading', 'chess', 'gardening'],
# 'name': 'Alice'}
```
のように改行して表示される


## pandas DataFrame

### DataFrameの作成

```python
import pandas as pd

# 辞書からDataFrameを作成
data = {
    'name': ['Alice', 'Bob', 'Charlie'],
    'age': [25, 30, 35],
    'city': ['Tokyo', 'Osaka', 'Kyoto']
}
df = pd.DataFrame(data)

# CSVファイルから読み込み
df = pd.read_csv('data.csv')

# Excelファイルから読み込み
df = pd.read_excel('data.xlsx')

# JSONから読み込み
df = pd.read_json('data.json')
```

### DataFrameの基本操作

- 列の確認

```python
df.columns.tolist()  # 全列名をリストで取得
df.info()           # データ型と基本情報
df.dtypes           # 各列のデータ型
```

- データの確認

```python
df.head()           # 最初の5行
df.tail()           # 最後の5行
df.describe()       # 基本統計情報
df.shape            # 行数と列数
```

- データの選択・フィルタリング

```python
df['category']      # 特定の列を選択
df[['job_item_id', 'category']]  # 複数列を選択
df[df['category'] == '製造・加工技術']  # 条件でフィルタリング
```

- データの集計

```python
df['category'].value_counts()  # カテゴリ別の件数
df.groupby('category').size()  # グループ別集計
```

- データの操作

```python
df.drop_duplicates()  # 重複行を削除
df.sort_values('job_sort_index')  # ソート
df.reset_index(drop=True)  # インデックスをリセット
```

- 1行目を取得する

```python
df.iloc[0]          # 1行目を取得（Seriesとして）
df.iloc[0:1]       # 1行目を取得（DataFrameとして）
df.head(1)         # 1行目を取得（DataFrameとして）
```

- 1行目の特定の列を取得

```python
df.iloc[0]['category']        # 1行目のcategory列
df.iloc[0, 1]                # 1行目の2列目（インデックス1）
df.iloc[0]['job_item_id']    # 1行目のjob_item_id列
```

- 1行目を削除

```python
df.drop(0)                   # 1行目を削除
df.iloc[1:]                  # 1行目以降を取得（1行目を除外）
```

- 1行目を更新

```python
df.iloc[0, 1] = '新しい値'    # 1行目の2列目を更新
df.loc[0, 'category'] = '新しいカテゴリ'  # 1行目のcategory列を更新
```