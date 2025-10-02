---
title: Ollama メモ
lang: ja
---

# ollama

https://chatgpt.com/share/680eca06-6414-8012-8963-f4799a8a664d

Ollamaは、異なるLLMを共通のインターフェースで動かせるフレームワーク です。
これは、Dockerのような「LLMのコンテナランタイム」 と考えると分かりやすいです。

# 使い方

## 実行

```bash
ollama run gemma3
```
googleの軽量LLMである、gemma3をローカルで動かす

叩くとコマンドで対話型で試せる

ないモデルはそのままダウンロードしにいき、ロードする。

```bash
ollama run phi4
```

ダウンロードするだけの場合

```bash
ollama pull phi4
```

## REST API利用

すでにollamaでwebサーバが建っている。ポート: 11434
以下を実行できる

```bash
curl http://localhost:11434/api/generate -d '{
  "model": "gemma3",
  "prompt": "日本の天皇について教えてください。",
  "stream": false
}'
```

stream: falseを入れることで、全体を返す。trueの場合、トークンごとに返ってくるので確認するのは面倒。
他のパラメータ一覧（chatGPTそのままなので、要確認）

パラメータ | 型 | 必須 | 意味・用途
|---| ---| ---| ---| 
model | 文字列 | ✅ 必須 | 使用するモデル名（例: "gemma3"、"mistral"）
prompt | 文字列 | ✅ 必須 | ユーザーから送るプロンプト内容（質問や命令文）
stream | 真偽値（boolean） | 任意 | ストリーミング（true）か一括応答（false）か。デフォルトはtrue
temperature | 数値（0.0〜1.0） | 任意 | 出力のランダム性。低いと堅い答え、高いと自由な答え
top_p | 数値（0.0〜1.0） | 任意 | トークン選択の確率分布カットオフ（nucleus sampling）
top_k | 整数 | 任意 | トークン選択対象を上位K個に絞る
presence_penalty | 数値 | 任意 | 同じ単語を繰り返さないようにするペナルティ
frequency_penalty | 数値 | 任意 | 出現頻度が高い単語を抑制するペナルティ
system | 文字列 | 任意 | システムプロンプト（キャラクター設定などに使う）
stop | 配列（文字列のリスト） | 任意 | 出力を強制的に止めるトークン（例：["\n"]）
context | 配列（数値） | 任意 | 継続会話用にコンテキスト（過去のやり取り）を渡す
options | オブジェクト | 任意 | モデル固有の追加設定（高度な使い方向け）

## ダウンロードしたLLMリスト

```bash
ollama list
```
参考のサイズ

```
NAME                  ID              SIZE      MODIFIED          
deepseek-r1:latest    0a8c26691023    4.7 GB    5 minutes ago        
gemma3:latest         a2af6cc3eb7f    3.3 GB    41 minutes ago       
phi4:latest           ac896e5b8b34    9.1 GB    59 minutes ago       
llama3.2:latest       a80c4f17acd5    2.0 GB    About an hour ago    
```

- ダウンロード可能リスト
https://ollama.com/library

## LLMを消す

```bash
ollama rm phi4
```

# 各LLMの印象（2025/04/27時点）

- deepseek-r1: 日本語ダメ
- phi4: 日本語okだけど日本語だと応答が遅い
- gemma3: 最高. アプリに使えるレベル
- llama3.2: 日本語ダメ
- mistral: 日本語ダメ
