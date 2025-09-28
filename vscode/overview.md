---
title: VS Code メモ
---


# VSCode

## キー操作

- 単語選択を1つずつ追加: cmd+D
- すべての単語選択: cmd+shift+L
- 検索: cmd+F
- 検索後、ヒットした単語の全選択：option+Enter
- 行削除: cmd+shift+K
- 行複製: option+shift+↑ or ↓

## コマンドパレット

- ファイル比較 : compare ... アクティブファイルか保存しているファイルか選択できる

## インデントの設定

- コマンドパレットでIndent Using Spaces（スペースによるインデント）を選択すると1〜8まで出てくるのでその数をクリックすることで設定できる

## 言語別の設定

- コマンドパレットから Preferences: Configure Language Specific Settings を選択して言語を選ぶと言語ごとに設定できる

## 正規表現で検索・置換

- 検索欄に正規表現を入力する（例：`^(.+)$`）
- 置換欄に `drop table $1;` のように `$1` でキャプチャした内容を参照できる
- 例：  
    - 検索：`^(.+)$`
    - 置換：`drop table $1;`
    - → 各行が `drop table テーブル名;` になる

## デバッグ

デバッグを起動すると.vscode/launch.jsonファイルができる

```json
{
  "version": "0.2.0",
  "configurations": [
    {
      "type": "pwa-node",
      "request": "launch",
      "name": "Launch Program",
      "skipFiles": [
        "<node_internals>/**"
      ],
      "program": "/Users/[name]/node/complete-nodejs-dev-course/notes-app/app.js",
      "args": ["read", "--title='how to write SQLie'"]
    }
  ]
}
```
`program`に実行ファイル、`args`に起動時パラメータを書く

pythonの仮想環境を指定するには、`python`にpythonのパス、ディレクトリを指定するには`cwd`を指定する。  
`${workspaceFolder}`とするとvscodeのワークスペースを指定できる。続けてサブディレクトリを指定することも可能。
ex.

```
"python": "/Users/[name]/opt/anaconda3/envs/summariaenv/bin/python" ,
"cwd": "${workspaceFolder}/summaria_app",
```

