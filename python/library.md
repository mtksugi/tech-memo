---
title: Python ライブラリメモ
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


