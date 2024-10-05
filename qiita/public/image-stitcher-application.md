---
title: 画像結合のためのアルゴリズムを考えてみた
tags:
  - Python
  - アルゴリズム
  - 画像処理
private: false
updated_at: '2024-10-04T14:26:19+09:00'
id: bf973b041709ad0cd635
organization_url_name: null
slide: false
ignorePublish: false
---

## はじめに
撮りたい対象が非常に大きい時、一枚の写真では収まりきらないケースというのはよくあります。そのような時に、複数の写真を結合して一枚の写真にしなければ、画像として視認性が低くなってしまいます。この記事では、そのようなケースに対応するための画像結合のためのアルゴリズムを考え、Pythonで実装してみました。提案アルゴリズムは、「入力画像の共通部分を事前情報なしで検出し、自動的に結合する」ことが可能です。

作成したソースコードについてはGitHubにて公開しています。興味のある方は以下のリンクからご覧ください。

https://github.com/C-Naoki/image-stitcher

## 出力例
以下に、アルゴリズムを用いて結合した出力例を示します。初めに、アルゴリズムに入力する画像の例を添付します。具体的には、以下に示す２枚の画像の結合を行います。画像については、[CIFAR-10データセット](https://www.cs.toronto.edu/~kriz/cifar.html)の中から選択しました。

![](https://raw.githubusercontent.com/C-Naoki/zenn-archive/main/images/image-stitcher-application/input.png)

図中の赤いエリアは、データが存在しない部分を表しています。また、右側の画像は、正解画像に比べて90度回転しています。開発したアルゴリズムは、この回転を加味して画像を結合することが可能です。

![](https://raw.githubusercontent.com/C-Naoki/zenn-archive/main/images/image-stitcher-application/rotated.png)

続いて、右画像の回転を補正し、左右の向きを揃えた結果が上図の通りです。緑色の枠で囲まれている部分が、アルゴリズムが自動的に検知した共通部分です。また、タイトル中に記載されている角度（上図では270°）が、アルゴリズムが検出した回転すべき最適な角度を意味しています。入力画像は、元データを90°回転させていることから、最適な角度を検出できていることがわかると思います。

![](https://raw.githubusercontent.com/C-Naoki/zenn-archive/main/images/image-stitcher-application/result.png)

そして、最終的に結合した結果が上図の通りです。アルゴリズムは、入力画像の共通部分を事前情報なしで検出し、自動的に結合することが可能であることが、この例から直感的に理解していただけると思います。

なお、他の実験結果例については、ここでは省略しますが、GitHubの [tutorial.ipynb](https://github.com/C-Naoki/image-stitcher/blob/main/notebooks/tutorial.ipynb) にて公開しているので、興味のある方はそちらをご覧ください。

## アルゴリズムの概要
以下では、提案アルゴリズムの概要を説明します。

### 1. 共通部分探索
![](https://raw.githubusercontent.com/C-Naoki/zenn-archive/main/images/image-stitcher-application/case1.png)

概要図は上記のとおりです。提案アルゴリズムは、$h_c$を共通部分の高さ、$w_c$を共通部分の幅とし、これらを基準とした全探索を行います。共通部分のサイズが $h_c \times w_c$であると仮定すると、共通部分は以下の**4箇所にのみ**存在することがわかります。

1. Image1の左上とImage2の右下の重複部分
2. Image1の右上とImage2の左下の重複部分
3. Image1の左下とImage2の右上の重複部分
4. Image1の右下とImage2の左上の重複部分（上図に対応）

これらにより、特定の共通部分に対して、4箇所を探索することで、過不足なく共通部分を検索することが可能です。

```python
for hc in range(min_h, max(h1, h2) + 1):
    for wc in range(min_w, max(w1, w2) + 1):
        for ix in range(2):
            for iy in range(2):
```

上記のコードの`ix`と`iy`の組み合わせが、上記の4箇所に対応しています。

#### 局所解に対する対策

画像結合において、局所解への収束は、品質の低い画像提供を意味します。このような問題を防ぐために、提案アルゴリズムは共通部分の最小値を設定することが可能です。共通部分のサイズをベースとした全探索を用いたことで、このような問題に簡単に対応可能です。

#### 画像の回転に対する対策

画像の回転に対しては、探索アルゴリズムを適用する前に片方の入力画像 (Image2) を回転させるというシンプルな方法で対処しています。

```python
for angle in [0, 90, 180, 270]:
    # 画像を回転
    _image2 = rotate(image2, angle, resize=True, preserve_range=True).astype(np.uint8)
```

### 2. 共通部分の拡張
![](https://raw.githubusercontent.com/C-Naoki/zenn-archive/main/images/image-stitcher-application/case2.png)

上記で説明したアルゴリズムは、共通部分のサイズが $h_c \times w_c$であると仮定しています。しかし、このアルゴリズムは、$h_c<\min(h_1, h_2)$ かつ $w_c<\min(w_1, w_2)$ という暗黙の仮定に基づいています。すなわち、任意の入力サイズに対応することはできません。このような問題に対応するために、上図のように、$h_c$と$w_c$を拡張することで、任意のサイズの入力画像に対して、提案アルゴリズムを適用することが可能になりました。

## まとめ
本記事では、画像結合のためのアルゴリズムを提案し、Pythonで実装してみました。提案アルゴリズムは、入力画像の共通部分を事前情報なしで検出し、自動的に結合することが可能です。また、提案アルゴリズムは、局所解への収束や画像の回転に対しても対処可能です。最後に、提案アルゴリズムの概要を説明し、その有効性を出力例を基に明らかにしました。

### 追記
2024/10/01: 200+ GitHub Stars🌟 を頂きました。ありがとうございます！