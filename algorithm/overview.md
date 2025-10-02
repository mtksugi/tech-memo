---
title: アルゴリズム メモ
lang: ja
---

# 2分探索
- Binary search
- ソート済の配列に対して求める値が中間値より大きいか小さいかを判断していく手法
- 先頭から順に見ていくとO(n)かかるところ、O(log(n))の計算量で済む
- ”ソート済の配列に対して”というと応用が効かないように思えるが、最大値を求める問題などでは、求める答えを2分探索で決め打ちしていく手法にも使える

> https://qiita.com/drken/items/97e37dd6143e33a64c8c#2-%E4%B8%80%E8%88%AC%E5%8C%96%E3%81%97%E3%81%9F%E4%BA%8C%E5%88%86%E6%8E%A2%E7%B4%A2%E6%B3%95

- 実装では添字を間違えやすいので注意...
> - left は「常に」条件を満たさない
> - right は「常に」条件を満たす
> - right - left = 1 になるまで範囲を狭める (最後は right が条件を満たす最小)

とすると汎用的に使えるらしい...  

- 疑似コード

```javascript
const binary_search = (value) => {
  let left = -1
  let right = max	//←条件を満たさない値. 例えば配列の場合、最大の添字の+1＝配列の数とする
  
  while(right - left > 1) {
    let mid = left + (right - left) / 2
    
    if (isOK(mid, value) {	//←ココは感覚的に逆かもしれないので注意. あくまで条件を満たすのはrightの最小値. isOKは中間値midが条件を満たす側にいる(true)のか、満たさない側にいる(false)のかを判定する. 満たさない側(まだvalueの方が大きい）ならfalseで次のサーチでは左leftがmidになる.
      right = mid
    } else {
      left = mid
    }
  }
  return right	//leftは条件を満たさない最大値. rightは条件を満たす最小値
}
```
もしくは↓とすればleft, rightのどちらが大きいかも気にすることがない

```javascript
const binary_search = (value) => {
  let ng = -1
  let ok = max	//←条件を満たさない値. 例えば配列の場合、最大の添字の+1＝配列の数とする
  
  while(abs(ok - ng) > 1) {
    let mid = (ok + ng) / 2
    
    if (isOK(mid, value) {	
      ok = mid
    } else {
      ng = mid
    }
  }
  return ok
}
```
# 尺取り法
> https://qiita.com/drken/items/ecd1a472d3a0e7db8dce

例えば、`数列a1, a2, ... , anの部分区間の総和がx以下のものを求めよ`
という問題を解く場合、愚直に先頭から全探索するとO(n^2)の計算量になる.  
これを、先頭から探索はするが、a2〜a4の総和を求めるときは、a1〜a3の総和からa1を引き、a4を足すという感じで、左と右を一つずつ動かしながら総和を取っていく手法.  
こうすると計算量はO(n)にできる！

- 疑似コード

```javascript
let right = 0;
for (let left = 0; left < n; left++) {
    while (right < n && (right を 1 個進めたときに条件を満たす)) {
        /* 実際に right を 1 進める */
        // ex: sum += a[right];
        ++right;
    }

    /* break した状態で right は条件を満たす最大なので、何かする */
    // ex: res += (right - left);

    /* left をインクリメントする準備 */
    // ex: if (right == left) ++right;
    // ex: else sum -= a[left];
}
```

# 深さ優先探索と幅優先探索
- Depth-First-Search and Breadth-First-Search
- DFSとBFS
> http://www.sd.is.uec.ac.jp/koga/lecture/FSkiso1/kiso1_04.pdf の43〜47ページ

## 深さ優先探索
> まだ訪れていない頂点を優先的に探索
> （未訪問の頂点を見つけたら即座に訪問）
- 疑似コード

```javascript
const dfs = (x) => {
  for (const u of xの隣接点集合) {
    if (!訪問済集合.includes(u)) {
      訪問済集合.push(u)
      dfs(u)
    }
  }
}
```

## 幅優先探索
> まずは, 現在の頂点に隣接する頂点を全て訪問
> 終わったら, “次の”頂点へ探索を進める
- 疑似コード

```javascript
const bfs = (s) => {
  Q.push(s)
  while (!Q) {	//Qが空になるまで
    x = Q.shift()
    for (const u of xの隣接点集合) {
      if (!訪問済集合.includes(u)) {
        訪問済集合.push(u)
        Q.push(u)
      }
	}
  }
}
```
# ダイクストラ法 - Dijkstra
> http://www.sd.is.uec.ac.jp/koga/lecture/FSkiso1/kiso1_04.pdf

> 部分の最小が、その近接点における最小距離
> →「 𝑣𝑖, 𝑣𝑗間の最短経路は𝑣𝑖, 𝑢間の最短経路を含む」

> グラフ G の全頂点を次の2つのグループに分けて
> - 始点 s からの最短経路が決まった頂点（S と書く）
> - そうでない頂点（Q と書く）
> 
> 始点 s からの最短経路が決まった頂点を１つずつ増やす

- アルゴリズム

```javascript
v ∈ V
v: 全頂点
s: 始点
d(v): sからvへの経路長
p(v): sまでの経路
w(x, y): x, y間の距離

//初期化
d(s) = 0
S = sの集合
Q = {V - S}
d(v ∈ Q) = ∞

while (!Q) {	//Qが空になるまで
  Qからd(x)が最小となる頂点xを抽出
  Qからxを除去
  S.push(x)
  for xの隣接点y
    if d(y) > d(x) + w(x, y) {
      d(y) = d(x) + w(x, y)
      p(y).push(x)	//これをすることでsまでの経路を保存できる
    }
}
```

# 動的計画法

全体の問題を部分の問題にして解く方法.  
ダイクストラも動的計画法の一つ.  
ある状態Sと次または前のS'の関係を表すのがコツ.  


# メモ化再帰

以下のようにxの前または次をx'で表すような漸化式で解くときに、

> f(x) = f(x')

x1, x2,...のf(x1), f(x2)を保存（メモ）しておき、再呼び出し時にメモを使うことを言う.  


# 期待値の算出

コイントスで合計Nになるまで何回なげればよいか、的あてゲームで全ての的がなくなるまで何回玉を投げればよいか、などの期待値を求めるには、到達状態S(n)の期待値は0、到達一つ前の状態S(n-1)として、  

> S(n) = f(S(n-1))

の漸化式で解くとよい.  
状態Sをどう表現するかがカギ.

# Tips
## 剰余を0ではなくて序数にしたい場合
11%3=2  
10%3=1  
9%3=0→3としたい場合  

`x%y`を
`(x - 1)%y + 1`とする

##  半開区間
区間を考えるときは半開区間 [left, right) で考えるとよい

例: 区間[3, 7) とは 3, 4, 5, 6を表し、7は含まない



