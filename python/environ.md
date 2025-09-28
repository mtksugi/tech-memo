---
title: Python 環境メモ
---

# Python 環境まわり

## python標準の仮想環境

- 開発環境から本番環境を構築するためにPythonのクリーンな環境を作る方法
  - requirements.txtなどを作成するために

python標準で仮想環境を作る方法を使用する
- 作成
```bash
python -m venv [folder name（よく使われるのは.venv）]
```
※仮想環境をつくるのはpython3から.  
単純にpythonだとpython2が使われて`-m venv`は効かないことがある.  
python3でコマンド登録されていることもあるので注意.  

- アクティベイト
```bash
. .venv/bin/activate
```
※windows の場合、\scripts\activate
- デアクティベイト
```bash
deactivate
```
- 削除
```bash
python -m venv --clear [folder name]
```
- 仮想環境を作成したらpipを最新化する
```bash
pip install -U pip
```


## anacondaの場合

- 環境作成
```bash
conda create -n [環境名] python=[バージョン番号(3.8など)]
```

- 環境確認
```bash
conda env list
```

- アクティベーション
```bash
conda activate [環境名]
```

- ディアクティベート
```bash
conda deactivate
```

- 環境削除
```bash
conda remove -n [環境名] --all
```

### python自体のバージョンアップ

既存の仮想環境で以下を実行。
```bash
conda install python=3.11
```
ただし、実行することで既存の中のライブラリが全部消えるので、新しい仮想環境を作ってからバージョンアップすること。


## requirements.txtの作り方
pip コマンド一発
```bash
pip freeze
```

## ライブラリの参照パスの優先順位を調べる
```bash
python -c "import sys; print(sys.path)"
```

## pipコマンド

- インストール
```bash
pip install [パッケージ名]
```
- バージョン指定インストール
```bash
pip install [パッケージ名]==[バージョン]
```
- requirements.txtで一括インストール
```bash
pip install -r requirements.txt
```
- 一括インストールでエラーを無視する
```bash
pip install -r requirements.txt --no-deps
```
- インストールせずにライブラリ関連をチェックする
```bash
pip install -r requirements.txt --dry-run
```

- アップグレード
```bash
pip install --upgrade [パッケージ名]
```
or
```bash
pip install -U [パッケージ名]
```
- アンインストール
```bash
pip uninstall [パッケージ名]
```
- 探す
```bash
pip search [パッケージ名]
```
実際は、https://pypi.org/ から探せばいい

- 特定ライブラリのバージョンリストを表示する
```bash
pip index versions [パッケージ名]
```

## プロセッサの確認
```python
import platform
platform.processor()
```
`i386`（x86）または`arm`が出力される
