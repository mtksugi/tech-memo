---
title: 脆弱性対応メモ
---



# 脆弱性対策

## 2024/07/02 openSSH CVE-2024-6387/regreSSHion 対応
https://gihyo.jp/article/2024/07/daily-linux-240702

### openSSHのバージョン確認
```bash
ssh -V
```

### 脆弱性バージョン

8.5p1から9.8p1までのバージョン（9.8p1は含まない⁠）で脆弱性あり


### パッケージアップデート
```bash
sudo apt-get update
```
### アップデートの確認
```bash
apt-cache policy openssh-server openssh-client
```
installedでインストールされているバージョン、candidateでアップデートバージョンが確認できる

### アップデートの実行（openSSHのみ）
```bash
sudo apt-get install --only-upgrade openssh-server openssh-client
```




