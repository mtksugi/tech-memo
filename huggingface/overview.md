---
title: Hugging Face メモ
lang: ja
---

# hugging Face / AWS SageMaker

## SageMaker

- jumpStartで使えるモデルはリージョンによって違う

## hugging faceに公開されているモデルをSageMakerで使う方法

- hugging faceのモデルのページに[Deploy]ボタンがある。クリックして[Amazon SageMaker]を開くとスクリプトが表示される
- それをSageMakerのnotebookで実行するだけ。
- インスタンスタイプが`ml.g5.2xlarge`になっている場合、モデルによってはもっと高性能なものを使わなければオチる。
- llama-2 7bでも2xlargeでは動かない。12xlargeにする必要がある。12xlargeはquotaの解除申請を行う必要がある。

