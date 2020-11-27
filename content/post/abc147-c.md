---
title: "C - HonestOrUnkind2【ABC147】"
date: 2019-12-08T23:36:33+09:00
draft: false
summary: "12月8日(日)日本時間21:00~に実施されたABC147のC問題の自分の解答。問題のURLは https://atcoder.jp/contests/abc147/tasks/abc147_c 。正直者と正直でない人の証言がある中で正直者の人数の最大値を求める問題。"
---

## 問題文

問題文は[こちら](https://atcoder.jp/contests/abc147/tasks/abc147_c) 。

## 第一印象
不親切な人というのがちょっとポイント。不親切な人はテキトーなことをいうので、そいつのいう情報は信用ならない。
やり方をいくつか考えられる。

1. 1が正直者であった時に、考えられる正直者の最大数を考える。2が正直者であった時、、、、Nが正直者であった時、それぞれの場合を考える。そしてそれらの最大数を比較し、最大数を返す。
2. 正直者の数で場合分けをする。正直者がk(1<=k<=N)人だった時にそのk人全てが正直者であるような組み合わせが考えられる時はok、考えられない場合はk-1を正直者の最大数として返す。
3. 正直者と多くの人に言われている人を正直者と設定する。

### 第一の方法
1のやり方はedge caseでまずいかもしれない。そのedge caseが

- N = 15
- 1: 15 → 0
- 2: 15 → 0
- 3: 15 → 0

...
...

- 14: 15 → 0
- 15: 1 → 0

(15人いて15以外の全ての人間が15を不親切な者と証言している場合。)

この場合の最大数はすぐに14人だとわかる(15だけが不親切な人間で、それ以外が正直者)。

第一の方法で考える。

1. 1を正直者とする。
2. 15が不親切な人と証言しているので、それ以外の人が正直な人である可能性がある。
3. 残り13人の中から正直者を一人選ぶ（2を選ぶ)。
4. 2は15が不親切な人間だと証言している。残り3~14の中に正直者がいる可能性がある。
5. 3~14の12人の中から1人を選ぶ。

...

この作業を矛盾しないように繰り返す。単純計算量は13!通り。
1を正直者としている場合なので、全体では13! $\times$ 14 $\plus$ 1= 14! \plus 1(通り)

この場合計算が制限時間内に終わらないので、第一の方法はダメそう。

### 第二の方法

k人が正直者だった場合の組み合わせを列挙し、彼らが全員正直者であるかどうかを確認する。面倒臭そう。。。。

## 解法

一番正直者と言われている人を正直者に設定してそこからどれくらいの人を加えていけるかを考えていくのが良さそう。

```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
  int N;
  vector<vector<int>> honest(N, vector<int>(N));
  vector<int> honestPoint(N);

  for(int i=0;i<N;i++) {
    int A;
    cin >> A;
    for(int j=0;j<A;j++) {
      int x, y;
      cin >> x >> y;
      if(y == 1) honestPoint[x]++;
      honest[i][x] = y;
    }
  }

  return 0;
}

```

まだ途中。

(November 27th, 2020)
ACした解法はこちら。
```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
  int n;
  cin >> n;
  vector<vector<pair<int, bool>>> hoge(n, vector<pair<int, bool>>(0));
  for(int i=0;i<n;i++) {
    int A;
    cin >> A;
    hoge[i].resize(A);
    for(int j=0;j<A;j++) cin >> hoge[i][j].first >> hoge[i][j].second;
  }
  int max = 0;
  for(int i=0;i<pow(2, n);i++) {
    bitset<32> kinder(i);
    bool ok = true;
    for(int j=0;j<n;j++) {
      if(kinder[j]) {
        for(int k=0;k<hoge[j].size();k++) {
          int obj = hoge[j][k].first;
          bool honest = hoge[j][k].second;
          if((kinder[obj-1] == 1 && honest) || (kinder[obj-1] == 0 && !honest)) {
            continue;
          } else {
            ok = false;
            break;
          }
        }
      }

      if(!ok) {
        break;
      }

    }

    if(ok) {
      int num = 0;
      for(int j=0;j<kinder.size();j++) {
        if(kinder[j] == 1) num++;
      }

      if(num > max) max = num;
    }
  }

  cout << max << endl;

}
```
