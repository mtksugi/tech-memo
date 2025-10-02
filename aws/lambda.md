
---
title: AWS Lambda メモ
lang: ja
---

# AWS Lambda

## レイヤの作成

- pythonで外部ライブラリを使用するには、レイヤを使う
https://docs.aws.amazon.com/ja_jp/lambda/latest/dg/python-package.html#python-package-dependencies-layers
- lambdaが実行時に読み込むフォルダが決まっており、そのどこかにライブラリを置けばよい

例: openaiライブラリなら  
python/openai  
python/openai-1.5.0.dist-info  
というフォルダ構成で、zip化して、lambdaサービスにレイヤを作成するときにアップロードする.    
そのレイヤを個々のlambda関数に適用する.    

openaiを使用する場合、もちろんその他のライブラリも必要のため、ローカルで作成した仮想環境の`.venv/lib/python3.8/site-packages`の下を`python`フォルダの中に入れてzip化する.  
site-packageの下の`__pycache__`は含めない

- ローカルでanacondaを使っているとそれの標準ライブラリは.venvには入らないので注意
- 今回は、`pydantic-core`というライブラリが含まれておらず、lambda側で実行できなかった。追加するにはローカルのpythonバージョンがあっていないので、pipでコンパイルしながらダウンロードした。

```bash
pip install \
--platform manylinux2014_x86_64 \
--target=python \
--implementation cp \
--python-version 3.12 \
--only-binary=:all: --upgrade \
pydantic-core
```
`--target`は保存ディレクトリを指定できる.  

## 関数URLをPOSTで呼び出す場合

```python
def lambda_handler(event, context):
    body = json.loads(event.get('body'))
    order_text = body.get('orderText')
```
で、`body`をjson化してから中身のjsonを取る

## 関数URLのCORS

- 許可オリジンの設定と、許可ヘッダに`content-type`が必要。

## タイムアウト

- デフォルト3秒は少し短いので5秒以上にする。
