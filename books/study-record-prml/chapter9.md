---
title: "第９章: 混合モデルとEM"
---
# 第９章: 混合モデルとEM

観測変数と潜在分布の同時分布を定義で、周辺化によって観測変数だけの分布（周辺分布）が得られる

- この方法で、複雑な観測変数の周辺分布を、扱いやすい観測変数と潜在変数の同時分布によって表現可能
- 言い換えるならば、複雑な分布を単純な分布から構成が可能

本章では、混合ガウス分布のような混合モデルが離散潜在変数の観点で解釈可能であることを議論する

- 連続潜在変数は第１２章の主題である

## 9.1: K-means クラスタリング

$D$ 次元ユークリッド空間上の確率変数 $\mathbf{x}$ の $N$ 個の観測展で構成されるデータ集合 $\{\mathbf{x}_1, ..., \mathbf{x}_N\}$ があると仮定する

- 目的としては、これらの $N$ 個のデータを $K$ 個のクラスタに分類すること
    - $K$ は既知であると考える
- 直感的な方法として、同一クラスタに属するデータ間の距離が他のクラスタに属するデータとの距離に比べて小さいと見做せる
    - プロトタイプと呼ばれる $K$ 個の $D$ 次元ベクトル $\boldsymbol{\mu}_k$ を導入し定式化する
    - $\boldsymbol{\mu}_k$ がクラスタの中心を表現する
    - 故に、各データ点から対応する $\boldsymbol{\mu}_k$ への二乗距離の総和を最小にすることである
- また、データ点のクラスターへの割り当てを表す記法を定義すると定量化可能
- 各データ $\mathbf{x}_n$ に対して、対応する２値指示変数 $r_{nk}\in\{0, 1\}$ を定める
    - もし $r_{nk}=1$ ならば、$\mathbf{x}_n$ がクラスタ $k$ に属していることを意味する
    - これは、**1-of-K 符号化法**(1-of-K coding scheme)として知られている
- 目的変数は以下のように与えられる

    $$
    J=\sum_{n=1}^N\sum_{k=1}^Kr_{nk}||\mathbf{x}_n-\boldsymbol{\mu}_k||^2
    $$

    - これは、**歪み尺度**(distortion measure)と呼ばれることもある
- この目的関数を最小化するための、$\{r_{nk}\}$ 及び $\{\boldsymbol{\mu}_k\}$ を求めるには、以下の２ステップを交互に繰り返す
    1. $\boldsymbol{\mu}_k$ を固定して、$r_{nk}$ について最小化する
        - $r_{nk}$ について目的関数は線形の関数であるため、代数的に解くことが可能
        - 感覚的には、$\boldsymbol{\mu}_k$ が固定されているので、それに一番近いものに割り当てているだけ

            $$
            r_{nk}=\left \{
            \begin{aligned}
            &1\quad (k=\argmin_j||\mathbf{x}_n-\boldsymbol{\mu}_j||^2)\\
            &0\quad(\text{それ以外})
            \end{aligned}
            \right .
            $$

    2. $r_{nk}$ を固定して、$\boldsymbol{\mu}_k$ について最小化する
        - 偏微分を解くことで得られる

            $$
            2\sum_{n=1}^Nr_{nk}(\mathbf{x}_n-\boldsymbol{\mu}_k)=0
            $$

        - 感覚的には、今割り振られているクラスタの重心っぽいところに $\boldsymbol{\mu}_k$ を置いている
- オンライン確率的アルゴリズムも導かれている

    $$
    \boldsymbol{\mu}_k^{new}=\boldsymbol{\mu}_k^{old}+\eta_k(\mathbf{x}_n-\boldsymbol{\mu}_k^{old})
    $$

- K-means はデータ点間の非類似度に二乗誤差を用いているが、これだと以下のような課題点がある
    1. データ変数の方が制限される（特に、カテゴリカル変数に対しては向かない）
    2. クラスター平均の決定が外れ値に対して頑健でなくなってしまう
    - 解決策としてより一般的な非類似度 $\mathcal{V}(\mathbf{x}, \mathbf{x}')$ を導入し、歪み尺度をより一般化する
        - これは、**K-medoids アルゴリズム** と呼ばれるアルゴリズムを与える
- K-means アルゴリズムの特筆すべき特徴の１つとしては、固定の割り当てが各繰り返しに対して行われる
    - 一般的にハード割り当てなどと呼ばれる
    - 各データ点に対してどのクラスタに属するか明らかでないようなものも存在し、それに対しても１つに固定する必要がある、このような割り当て方法が最適であるかどうかは明らかでない
    - これに対して、各クラスタに属する確率的なものを算出するものも存在する
        - これは、ハード割り当てに対してソフト割り当てなどと呼ばれる
        - 特徴としては、各データ点に対する不明瞭さ/曖昧性が加味される

### 9.1.1: 画像分割と画像圧縮

１つの画像を適度にただし同質な外見を持つ複数の領域、物体や部品に対応する複数の領域に分割するという目的をK-meansで解決可能

- 具体的には、画像の画素が 赤、青、緑 の３原色の輝度からなる3次元空間の1点であると扱った画像分割アルゴリズムとして考察する
- これは、画素の空間的な近さを考慮に入れていないという理由で、画像の分割の方法としては洗練されているとは言えない

クラスタリングのアルゴリズムをデータ圧縮に使う際、以下の２種類のパターンが存在する

1. **無歪みデータ圧縮** (可逆データ圧縮; lossless data compression)
    - 圧縮結果からデータを完全に復元できる必要がある
2. **歪みのあるデータ圧縮** (非可逆データ圧縮; lossy data compression)
    - 復元にある程度のエラーが含まれても良い
    - 多くの場合、高レベルの圧縮率を達成可能

また、K-meansのように各データ点を近いベクトル (今回は $\boldsymbol{\mu}_k$) で近似するような枠組みを、**ベクトル量子化** (vector quantization)、$\boldsymbol{\mu}_k$ のようなベクトルを**符号表ベクトル** (代表ベクトル; code-book vector) と呼ぶ

## 9.2: 混合ガウス分布 (Mixtures of Gaussians)

離散的な**潜在変数** (latent variable) を用いた混合ガウス分布の定式化を行う

- 復習として、混合ガウス分布は単一のガウス分布の線形重ね合わせでかけた

    $$
    p(\mathbf{x})=\sum_{k=1}^K\pi_k\mathcal{N}(\mathbf{x}|\boldsymbol{\mu}_k, \boldsymbol{\Sigma}_k)
    $$

- 上記の式に対して、$K$ 次元の２値確率変数 $\mathbf{z}$ を導入する
    - 1-of-$K$ 符号化法を取る (i.e., $z_k\in\{0, 1\}$ かつ $\sum_k z_k=1$)
    - $p(\mathbf{z})$ は以下のようにかける

        $$
        p(\mathbf{z})=\prod_{k=1}^K\pi_k^{z_k}
        $$

- この式に対して、$p(\mathbf{x}|\mathbf{z})$ を考えると、$p(\mathbf{x})$ と同じ形が得られることがわかる
- この式を導出したことで、$p(\mathbf{x}, \mathbf{z})$ という同時確率を用いた議論が可能となる

続いて、$p(z_k=1|\mathbf{x})$ について議論する

- この値は、以後重要な役割と果たす
- ベイズの定理を用いることで、以下のように書き下すことが可能

    $$
    \begin{align*}
    \gamma(z_k)\equiv p(z_k=1|\mathbf{x})&=\frac{p(z_k=1)p(\mathbf{x}|z_k=1)}{\sum_{j=1}^Kp(z_j=1)p(\mathbf{x}|z_j=1)}\\
    &=\frac{\pi_k\mathcal{N}(\mathbf{x}|\boldsymbol{\mu}_k, \boldsymbol{\Sigma}_k)}{\sum_{j=1}^K\pi_j\mathcal{N}(\mathbf{x}|\boldsymbol{\mu}_j, \boldsymbol{\Sigma}_j)}
    \end{align*}
    $$

    - ベイズの定理とは、$p(x|y)=p(x)p(y|x)/p(y)$
    - 分母の部分は、条件付き確率を周辺化したもの (i.e., $p(y)=\sum_x p(x)p(y|x)$)
        - 分母と分子に同じ形が出てくる
    - また、$\gamma(z_k)$ は**負担率** (responsibility) として解釈可能
    - データ点 $\mathbf{x}$ がどの程度クラスタに属しているかの割合を表現している
        - $\gamma(z_k)=1$ ならば、データ点 $\mathbf{x}$ は $k$ 番目のクラスタだけで説明されている

### 9.2.1: 最尤推定

観測データ集合 $\mathbf{X}=\{\mathbf{x}_1, ..., \mathbf{x}_N\}\in\R^{N\times D}$ に混合ガウス分布をあてはめる問題を考える

また、対応する潜在変数の集合 $\mathbf{Z}=\{\mathbf{z}_1, ..., \mathbf{z}_N\}\in\R^{N\times K}$ を考える

- 各データ点が分布から独立に生成されると仮定すると、対数尤度関数は以下のように表現される

    $$
    \ln p(\mathbf{X}|\boldsymbol{\pi}, \boldsymbol{\mu}, \boldsymbol{\Sigma})=\sum_{n=1}^N\ln \bigg\{ \sum_{k=1}^K\pi_k\mathcal{N}(\mathbf{x}_n|\mu_k, \Sigma_k) \bigg\}
    $$

- この関数の最大化において、特異性の存在に起因する重要な問題が存在する
    - 混合モデルの $j$ 番目の混合要素の平均 $\mu_j$ が１つのデータ点とちょうど等しい時、対数尤度関数が発散してしまうという、最大化問題として不良設定である
- また、任意の最適解に対して、$K$ この各混合要素の順番の入れ替えに対応して、同等な解が $K!$ こあるという事実は、最尤解を見つける際に別の問題を引き起こす（**識別可能性** (identifiability)）
    - モデル当てはめで得られたパラメータ値を解釈する際に重要である
    - しかし、良い密度モデルを見つける目的には関係ない
        - 等価な会はどれも同等に良いモデルであるため
- 混合ガウスモデルの最尤推定問題は、単一のガウスモデルのそれと比べてより複雑な問題である
    - 具体的には、対数の中に $K$ この要素についての和が存在する
    - これにより、対数尤度の微分を $0$ とおいても、陽な解が得られない
    - 解く方法の１つとして、勾配に基づく最適化手法の適用が挙げられる

### 9.2.2: 混合ガウス分布のEMアルゴリズム

潜在変数を持つモデルの最尤解を求めるための１つのエレガントかつ強力な方法として、**EMアルゴリズム** (expectation-maximization algorithm) と呼ばれるものがある

- EMアルゴリズムの一般化として、10.1 節に変分推論法の枠組みが得られる

対数尤度関数の平均 $\mu_k$ に関して微分して、$0$ と置くと次式が得られる

$$
0=\sum_{n=1}^N\frac{\pi_k\mathcal{N}(\mathbf{x}_n|\mu_k, \Sigma_k)}{\sum_j \pi_j\mathcal{N}(\mathbf{x}_n|\mu_j, \Sigma_j)}\Sigma_k^{-1}(\mathbf{x}_n-\mu_k) \\
\therefore\mu_k=\frac{1}{N_k}\sum_{n=1}^N\gamma(z_{nk})\mathbf{x}_n
$$

ただし、$N_k=\sum_n\gamma(z_{nk})$ を満たす

- これは、$N_k$ は $k$ 番目のクラスターに割り当てられる点の実行的な数と解釈できる
- すなわち、データ点 $\mathbf{x}_n$ が $k$ 番目のクラスターについてどの程度負担しているかについて、重み月平均を計算している

続いて、対数尤度関数の共分散行列 $\Sigma_k$ に関して微分して、$0$ と置き、単一のガウスモデルの共分散行列の最尤解を求めるときの動揺の推論を行うと次式が得られる

$$
\Sigma_k=\frac{1}{N_k}\sum_{n=1}^N\gamma(z_{nk})(\mathbf{x}_n-\mu_k)(\mathbf{x}_n-\mu_k)^\top
$$

- 単一のガウスモデルを同じデータ集合に当て嵌めた結果と同じ形をしている

     🧐  これはおそらく、単一のガウスモデルを最尤推定で解いた結果の話をしているはず

    $$
    \Sigma_{ML}=\frac{1}{N}\sum_{n=1}^N(\mathbf{x}_n-\mu_{ML})(\mathbf{x}_n-\mu_{ML})^\top
    $$

- 但し、平均と同様に対応する事後分布で重み付けられている

最後に、混合係数 $\pi_k$ について最大化する

- $\sum\pi_k=1$ という制限の下最大化するため、ラグランジュ未定係数法を用いる
- 具体的には、以下の式について $\pi_k$ で微分して $0$ で置く

    $$
    \ln p(\mathbf{X}|\boldsymbol{\pi}, \boldsymbol{\mu}, \boldsymbol{\Sigma})+\lambda \Bigg ( \sum_{k=1}^K\pi_k-1 \Bigg )
    $$

    - これを解くと、以下の結果が得られる

        $$
        \pi_k=\frac{N_k}{N}
        $$


これらの結果は、混合モデルのパラメータについて陽な解を得られていないことを強調すべきである

- なぜならば、負担率 $\gamma(z_{nk})$ という複雑な形でモデルのパラメータに依存するからである
- しかし、これは最尤推定問題の解を見出すための単純な繰り返し手続きの存在を示唆している
    - E-step においては事後確率すなわち負担率を計算するために現在のパラメータ値を用いる
    - M-step においてはこれらの事後確立に基づき、平均、分散、混合パラメータを再計算する

EMアルゴリズムについては以下のような特徴がある

- K-meansアルゴリズムに比べて、収束するまでに必要な繰り返し数と、一度の繰り返し回数の計算量がずっと多い
    - 故に、混合ガウスモデルの適切な初期値を見出すためにK-meansアルゴリズムを実行することがよくある
- また、一般的に対数尤度には多くの極大解が存在し、EMアルゴリズムはそれらのうちで最大のものに収束することは限らない

## 9.3: EMアルゴリズムのもう一つの解釈

EMアルゴリズムについて、潜在変数が果たす重要な役割を捉えた補足的な見方を提示する

EMアルゴリズムの目的は、潜在変数を持つモデルについて最尤解を見出すことである

- 対象の対数尤度関数を以下に再掲する

    $$
    \ln p(\mathbf{X}|\boldsymbol{\theta})=\ln \Bigg\{ \sum_{\mathbf{Z}}p(\mathbf{X}, \mathbf{Z}|\boldsymbol{\theta}) \Bigg\}
    $$

    - 連続潜在変数の場合について議論する場合は、和を単純に積分に置き換えることで出来る
- 鍵となる観点は、潜在変数に関する総和が対数の中にあること
    - 同時分布が指数型分布族に属する場合でも、周辺分布 $p(\mathbf{X}|\boldsymbol{\theta})$ は、$\mathbf{Z}$ に関する話の結果、普通は指数型分布族にならない
- また、データセットについても、観測データセット $\mathbf{X}$ のみが与えられており、**不完全** であるという
    - 対して、$\{\mathbf{X, Z}\}$ という組を**完全データ集合** (complete date set) と呼ぶ
    - 実際には、完全データ集合は与えられないことに注意する
    - $\mathbf{Z}$ に関する知識は、事後分布 $p(\mathbf{Z}|\mathbf{X}, \boldsymbol{\theta})$ によるものだけである
    - すなわち、完全データ対数尤度関数は用いることができない
        - 代わりに、潜在変数について事後確立に関する期待値だけ考える
        - これは、EMアルゴリズムのE-stepに対応する
            - ここで計算する期待値は $\mathcal{Q}(\theta, \theta^{old})$ と書かれ、次式で与えられる

                $$
                \mathcal{Q}(\theta, \theta^{old})=\sum_{\mathbf{Z}}p(\mathbf{Z}|\mathbf{X}, \theta^{old})\ln p(\mathbf{X}, \mathbf{Z}|\theta)
                $$

        - 一方、M-stepでは、上記で考えた期待値を最大化する
            - 具体的には、現在の推定値 $\theta^{old}$ に対して、新しい推定値 $\theta^{new}$ を得る
- EMアルゴリズムは、パラメータの事前分布が定義されたモデルについてMAP (最大事後確率推定; maximum posterior) 解を見出すのにも使える
    - M-step において最大化する量が、$\mathcal{Q}(\theta, \theta^{old})+\ln p(\theta)$ となる
    - 事前分布の適切な設定により、説明したような特異性を取り除くことができる
    - データ集合中の欠損値を非観測変数として見做してEMアルゴリズムを用いることもできる
        - しかしこれは、データの値が**ランダム欠損** (missing at random) している場合に有効である

### 9.3.1: 混合ガウス分布再訪

ここでは、潜在変数によるEMアルゴリズムの見方を、混合ガウスモデルの場合に適用して考察する

目的は、観測データ集合 $\mathbf{X}$ から計算される対数尤度関数の最大化である、加えて、対数の中に現れる $k$ に関する総和のために単一のガウス分布の場合に比べてずっと難しい

- ここでは、実際には見えない離散潜在変数 $\mathbf{Z}$ の値が与えられていると仮定する

完全データ集合 $\{\mathbf{X}, \mathbf{Z}\}$ に関する尤度を最大化する問題を考える

$$
p(\mathbf{X}, \mathbf{Z}|\boldsymbol{\mu}, \boldsymbol{\Sigma}, \boldsymbol{\pi})=\prod_{n=1}^N\prod_{k=1}^K\pi_k^{z_{nk}}\mathcal{N}(\mathbf{x}_n|\mu_k, \Sigma_k)^{z_{nk}}\\
\ln p(\mathbf{X}, \mathbf{Z}|\boldsymbol{\mu}, \boldsymbol{\Sigma}, \boldsymbol{\pi})=\sum_{n=1}^N\sum_{k=1}^Kz_{nk}\{\ln\pi_k+\ln\mathcal{N}(\mathbf{x}_n|\mu_k, \Sigma_k)\}
$$

- 式について、$k$ に関する総和と対数の順番が入れ替わっている
- 完全データ対数尤度関数は $K$ 個の独立な寄与の単純な話である
    - 故に、平均と共分散に関する最大化は、単一のガウス分布の場合と同じ
    - 各混合要素に「割り当てられた」データ点の部分集合のみを用いている点のみが異なる

また、混合係数に関する最大化については、以前と同様にラグランジュ未定乗数法で解くことができ、以下のような結果が得られる

$$
\pi_k=\frac{1}{N}\sum_{n=1}^Nz_{nk}
$$

- これは、対応する要素に割り当てられたデータ点数の割合に等しい
- よって、完全データ対数尤度関数の最大化は明らかに陽な形で解ける

しかし、実際には先に論じたように潜在変数の値はわからないので、潜在変数の事後分布に関して、完全データ尤度関数の”期待値”を考えることになる

- 本当は、完全データ尤度関数自体を最大化したいが、それが叶わないため、期待値を考える
    - 確か、この期待値が数式上適切な下限になることがイェンセンの不等式より示され、最大化する対象として適切であることが言える
- 潜在変数の事後確率分布をはベイズの定理とともに用いると、以下のように与えられる

    $$
    p(\mathbf{Z}|\mathbf{X}, \boldsymbol{\mu}, \boldsymbol{\Sigma}, \boldsymbol{\pi})\propto\prod_{n=1}^N\prod_{k=1}^K[\pi_k\mathcal{N}(\mathbf{x}_n|\mu_k, \Sigma_k)]^{z_{nk}}
    $$

    - この式の右辺は $n$ については積の形をしており、事後分布の下では $\{\mathbf{z}_n\}$ が独立である
- この事後分布に関する指示変数の期待値は以下のようになる

    $$
    \begin{align*}
    \mathbb{E}[z_{nk}]&=\frac{\sum_{\mathbf{z}_n} z_{nk}\prod_{k'}[\pi_{k'}\mathcal{N}(\mathbf{x}_n|\mu_{k'}, \Sigma_{k'})]^{z_{nk'}}}{\sum_{\mathbf{z}_n} \prod_{j}[\pi_{j}\mathcal{N}(\mathbf{x}_n|\mu_{j}, \Sigma_{j})]^{z_{nj}}}\\
    &=\frac{\pi_k\mathcal{N}(\mathbf{x}_n|\mu_{k}, \Sigma_{k})}{\sum_{j=1}^K\pi_j\mathcal{N}(\mathbf{x}_n|\mu_{j}, \Sigma_{j})}=\gamma(z_{nk})
    \end{align*}
    $$

    - この式は、$\mathbb{E}_{p(\mathbf{Z}|\mathbf{X})}[\ln p(\mathbf{X, Z}|\boldsymbol{\mu}, \boldsymbol{\Sigma}, \boldsymbol{\pi})]=\sum_{\mathbf{Z}}{p(\mathbf{Z}|\mathbf{X}, \boldsymbol{\mu}, \boldsymbol{\Sigma}, \boldsymbol{\pi})}\ln p(\mathbf{X}, \mathbf{Z}| \boldsymbol{\mu}, \boldsymbol{\Sigma}, \boldsymbol{\pi})$ を式変形することで得られる[^1]
[^1]: [9.3.1：混合ガウス分布のEMアルゴリズムの導出：その2【PRMLのノート】](https://www.anarchive-beta.com/entry/2021/05/27/164505)
- この式より、M-step で最大化する対象の完全データ対数尤度関数の期待値は

    $$
    \mathbb{E}_{p(\mathbf{Z}|\mathbf{X})}[\ln p(\mathbf{X, Z}|\boldsymbol{\mu}, \boldsymbol{\Sigma}, \boldsymbol{\pi})]=\sum_{n=1}^N\sum_{k=1}^K\gamma(z_{nk})\{\ln\pi_k+\ln\mathcal{N}(\mathbf{x}_n|\mu_k, \Sigma_k)\}
    $$

    として与えられる


### 9.3.2: K-meansとの関連

K-meansアルゴリズムと混合ガウス分布に関するEMアルゴリズムとを比較する

- K-meansアルゴリズムはデータ点を各クラスターにハードに割り当てるのに対して、EMアルゴリズムは事後確立に基づくソフトな割り当てを行う
- この性質を踏まえて、混合ガウス分布に関するEMアルゴリズムのある極限としてK-meansアルゴリズムを導出することができる

各ガウス要素の共分散行列が $\epsilon\mathbf{I}$ で与えられる場合の混合ガウスモデルを考える

$$
p(\mathbf{x}|\mu_k, \Sigma_k)=\frac{1}{(2\pi\epsilon)^{D/2}}\exp\bigg\{-\frac{1}{2\epsilon}||\mathbf{x}-\mu_k||^2\bigg\}
$$

上記の式に対して、$k$ 番目のクラスターの負担率は以下のように計算される

$$
\gamma(z_{nk})=\frac{\pi_k\exp\{-||\mathbf{x}_n-\mu_k||^2/2\epsilon\}}{\sum_j\pi_j\exp\{-||\mathbf{x}_n-\mu_j||^2/2\epsilon\}}
$$

- この式において、$\epsilon\rightarrow0$ の極限を考えると、$\gamma(z_{nk})\rightarrow r_{nk}$ であることがわかる
    - $r_{nk}$ はK-meansでの説明に出てきた割合を表現するためのベクトルの要素
    :::details イメージ詳細
      1. $||\mathbf{x}_n-\mu_k||^2$ が大きいほど、$-||\mathbf{x}_n-\mu_k||^2/2\epsilon$ は速く $\infin$ に発散する
          - $||\mathbf{x}_n-\mu_k||^2$ が大きいならば、データ点 $\mathbf{x}_n$ にとって $k$ 番目のクラスタが相応しくないことを意味する
      2. $||\mathbf{x}_n-\mu_k||^2$ が大きいほど、$\exp\{-||\mathbf{x}_n-\mu_k||^2/2\epsilon\}$ は速く $0$  に収束する
      3. 1-2 の議論により、 $\sum_j\pi_j\exp\{-||\mathbf{x}_n-\mu_j||^2/2\epsilon\} \approx \pi_{j^\star}\exp\{-||\mathbf{x}_n-\mu_{j^\star}||^2/2\epsilon\}$ と考えることができる
          - ただし、${j^\star}=\argmin_j||\mathbf{x}_n-\mu_j||^2$
          - 言い換えると、$k\in[K]/j^\star$ に対して $\pi_{k}\exp\{-||\mathbf{x}_n-\mu_{k}||^2/2\epsilon\}\ll\pi_{j^\star}\exp\{-||\mathbf{x}_n-\mu_{j^\star}||^2/2\epsilon\}$
      4. 1-3 までの議論を用いると、負担率は以下のようにかける
          1. $k\neq{j^\star}$ の時

              $$
              \begin{align*}
              \gamma(z_{nk})&=\frac{\pi_k\exp\{-||\mathbf{x}_n-\mu_k||^2/2\epsilon\}}{\sum_j\pi_j\exp\{-||\mathbf{x}_n-\mu_j||^2/2\epsilon\}} \\ &\approx\frac{\pi_{k}\exp\{-||\mathbf{x}_n-\mu_{k}||^2/2\epsilon\}}{\pi_{j^\star}\exp\{-||\mathbf{x}_n-\mu_{j^\star}||^2/2\epsilon\}}\rightarrow0\quad(\epsilon\rightarrow0)
              \end{align*}
              $$

          2. $k={j^\star}$ の時

              $$
              \begin{align*}
              \gamma(z_{nk})&=\frac{\pi_k\exp\{-||\mathbf{x}_n-\mu_k||^2/2\epsilon\}}{\sum_j\pi_j\exp\{-||\mathbf{x}_n-\mu_j||^2/2\epsilon\}} \\ &\approx\frac{\pi_{j^\star}\exp\{-||\mathbf{x}_n-\mu_{j^\star}||^2/2\epsilon\}}{\pi_{j^\star}\exp\{-||\mathbf{x}_n-\mu_{j^\star}||^2/2\epsilon\}}\rightarrow1\quad(\epsilon\rightarrow0)
              \end{align*}
              $$

          - これは、$\gamma(z_{nk})\rightarrow r_{nk}$ であることを意味する、言い換えるとハードな割り当てが実現する
    :::
- 対数尤度についても、K-meansの結果に帰着する
- K-means アルゴリズムはクラスターの分散を推定せず、平均のみを推定することに注意されたい
    - 一方で、一般の共分散行列を持つハード割り当て版の混合ガウスモデルに関するEMアルゴリズムは、**楕円K-meansアルゴリズム** (楕円 $K$ 平均アルゴリズム; elliptical K-means algorithm) と呼ばれる

### 9.3.3: 混合ベルヌーイ分布

混合ガウス分布以外の混合モデルにおけるEMアルゴリズムを議論する

- ここでは、ベルヌーイ分布で表される２値の変数の混合について議論する
    - このモデルは**潜在クラス分析** (latent class analysis) としても知られる
    - ここでの混合ベルヌーイ分布についての議論は、離散変数に関する隠れMarkovモデルを考察するための基礎も形成する

$D$ 個の２値変数 $x_i~(i=1, ..., D)$ を考えると、これらは以下のようなパラメータ $\mu_i$ を持つベルヌーイ分布に従うと仮定する

$$
p(\mathbf{x}|\boldsymbol{\mu})=\prod_{i=1}^D\mu_i^{x_i}(1-\mu_i)^{(1-x_i)}
$$

- ただし、$\mathbf{x}=(x_1, ..., x_D)^\top$,  $\boldsymbol{\mu}=(\mu_1, ..., \mu_D)^\top$ とする
- $\boldsymbol{\mu}$ が与えられている時、各変数 $x_i$ は独立である
- また、この分布の平均と共分散は以下のように与えられる

    $$
    \begin{align*}
    \mathbb{E}[\mathbf{x}]&=\boldsymbol{\mu}\\
    \text{cov}[\mathbf{x}]&=\text{diag}\{\mu_i(1-\mu_i)\}
    \end{align*}
    $$


以下では、これらの分布の有限混合分布を考える

$$
p(\mathbf{x}|\boldsymbol{\mu}, \boldsymbol{\pi})=\sum_{k=1}^Kp(\mathbf{x}|\boldsymbol{\mu}_k)
$$

- ただし、$\boldsymbol{\mu}=(\boldsymbol{\mu}_1, ..., \boldsymbol{\mu}_K)^\top$,  $\boldsymbol{\pi}=(\pi_1 ..., \pi_K)$ である
- 混合分布の平均及び共分散はそれぞれ次式で与えられる

    $$
    \begin{align*}
    \mathbb{E}[\mathbf{x}]&=\sum_{k=1}^K\pi_k\boldsymbol{\mu}_k\\
    \text{cov}[\mathbf{x}]&=\sum_{k=1}^K\pi_k\{\Sigma_k+\mu_k\mu_k^\top\}-\mathbb{E}[\mathbf{x}]\mathbb{E}[\mathbf{x}]^\top
    \end{align*}
    $$

    - ただし、$\Sigma_k=\text{diag}\{\mu_{ki}(1-\mu_{ki})\}$
    - $\text{cov}[\mathbf{x}]$ は対角行列ではなく、変数間の相関を捉えることが可能

このモデルの対数尤度関数は次式で与えられる

$$
\ln p(\mathbf{X}|\boldsymbol{\mu}, \boldsymbol{\pi})=\sum_{n=1}^N\ln\Bigg\{ \sum_{k=1}^K\pi_kp(\mathbf{x}_n|\boldsymbol{\mu}_k) \Bigg\}
$$

- ガウス分布の時と同様に、対数の中に和が現れており、最尤解は陽な形で得られない

混合係数を1-of-K符号法で書くことで、混合係数を潜在変数で表記する
それを用いて、完全データ対数尤度関数を書き下すと、以下の通り

$$
\ln p(\mathbf{X}, \mathbf{Z}|\boldsymbol{\mu}, \boldsymbol{\pi})=\sum_{n=1}^N\sum_{k=1}^Kz_{n,k}\Bigg\{\ln\pi_k+\sum_{i=1}^D[x_{ni}\ln\mu_{ki}+(1-x_{ni})\ln(1-\mu_{ki})  ]\Bigg\}
$$

この式に対して、潜在変数 $\mathbf{Z}$ に対する期待値を計算することで、E-step で最大化すべき尤度を定式化できる

- 🧐  繰り返しだが、潜在変数自体を完全に求めることができないことにより、完全データ対数尤度関数を計算に用いることは不可能であるため、期待値を考える
    - EM アルゴリズムは潜在変数の事後分布は計算できる仮定であることと混同してはNG

混合ガウス分布の場合と異なる点として、尤度関数が発散する特異性が存在しないことが挙げられる

- これは、尤度関数が上界であることに起因する
    - 下界はないため、如何なる条件下でも特異点にいかないと証明されているわけではないが、病的な初期値を選ばない限りは問題ない
- 混合ガウス分布の場合は、分散が0に近づくほど、大きくなってしまうため、特異点に行く

拡張として、共役事前分布であるベータ分布を用いることでMAP推定が可能である
また、多項変数のケースに拡張することも単純作業で済む

- 共役事前分布はディリクレ分布

### 9.3.4: ベイズ線形回帰に関するEMアルゴリズム

第３章を読みきり次第、勉強予定

## 9.4: 一般のEMアルゴリズム

EMアルゴリズムとは、潜在変数を持つモデルの最尤解を求めるための一般的手法である

- 以降EMアルゴリズムの一般的な議論を行うが、これは変ぶん推論の枠組みを導出するための基礎を成すものでもある

EMアルゴリズムを使用する目的としては、以下の式で与えられる尤度関数の最大化である

$$
p(\mathbf{X}|\boldsymbol{\theta})=\sum_{\mathbf{Z}}p(\mathbf{X}, \mathbf{Z}|\boldsymbol{\theta})
$$

ここで、EMアルゴリズムを適用する際の仮定として、「尤度の直接的な最適化は困難である」が「完全データ対数尤度関数の最適化は著しく容易である」ことを考える

潜在変数 $q(\mathbf{Z})$ を導入すると、任意の $q(\mathbf{Z})$ に対して以下の分解が成立する

$$
\ln p(\mathbf{X}|\boldsymbol{\theta})=\mathcal{L}(q, \boldsymbol{\theta})+\text{KL}(q||p)
$$

- E-stepでは、$\text{KL}(q||p)=0$ となるような $q(\mathbf{Z})~(=p(\mathbf{Z} | \mathbf{X}, \boldsymbol{\theta}^{(t)}))$ を見つける
- M-stepでは、期待値 $Q(\boldsymbol{\theta} | \boldsymbol{\theta}^{(t)}) = \mathbb{E}_{\mathbf{Z} | \mathbf{X}, \boldsymbol{\theta}^{(t)}} [\log p(\mathbf{X}, \mathbf{Z} | \boldsymbol{\theta})]$  を更新するような $\boldsymbol{\theta}^{new}$ を計算する
:::details 直感的な解釈
  - EM-algorithmを一言で改めて言い直すのであれば、「潜在変数をいい感じに捉えながら、尤度を最大化したいから、同時最適化っぽく解こう」となるはず
  1. $\text{KL}(q||p)$ に基づいて、$p$（の事後分布）に対してできる限り近似的かつ簡易的な分布 $q$ を生成する（E-step）
      - EM-algorithm の仮定下においては最適かつ唯一な解が得られる
      - ここでの考え方は変分推論で複雑な潜在変数の事後分布 $p(\mathbf{Z}|\mathbf{X}, \theta^{old})$ を $q(\mathbf{Z})$ で近似したいというモットーが含有されており、ことEM-algorithm を解くときに限っては、若干回りくどい説明になっている
      - 具体的には、E-stepは、「M-stepで更新した $\boldsymbol{\theta}$ に対して、潜在変数の事後分布 $p(\mathbf{Z}|\mathbf{X}, \theta^{old})$ を計算する（潜在変数の分布を最適化する）」くらいの感じ
          - くどいが、変分推論では、潜在変数の分布を最適化することが極めて困難であるケースを取り扱う
      - パラメータ $\boldsymbol{\theta}$ に対する潜在変数 $\mathbf{Z}$ の更新を、KL の最小化という問題に置き換えている？
  2. $\boldsymbol{\theta}$ を固定している条件下において、対数尤度 $\ln p(\mathbf{X}|\boldsymbol{\theta})$ は一定の値を取るため、$\text{KL}(q||p)$ の最小化は、$\mathcal{L}(q, \boldsymbol{\theta})$ の最大化につながる
      - ここで、$\mathcal{L}(q, \boldsymbol{\theta})$ はパラメータと潜在変数の分布の関係性を意味していることに注意
      - $\boldsymbol{\theta}$ が固定の環境であるため、現在のパラメータに対して最も適した分布 $q(\mathbf{Z})$ を引っ張ってきていることにつながる
  3. $q(\mathbf{Z})=p(\mathbf{Z}|\mathbf{X}, \theta^{old})$ の時、KLは0になるため、$\mathcal{L}(q, \boldsymbol{\theta})$ を更新するようなパラメータを計算すれば良い（M-step）
      - $\mathcal{L}(q, \boldsymbol{\theta})$ を整理すると、著しく簡単に計算できる完全データ対数尤度関数（と定数項）で構成されることがわかり、更新は解析的に行うことが可能
      - ここで新たなパラメータが得られると、現在用いている潜在変数の分布が最適な解ではなくなる、故に、KL≠0となる
          - これは、新たにE-stepを行わなければならないことを意味する
          - 混合分布で考えるとわかる話だが、割り当てが最適でない状態だと最適なパラメータを得たとしても、全体的な最適解ではない
:::

i.i.d. データ集合を表すモデルを仮定している特別な場合を考える

- $p(\mathbf{X}, \mathbf{Z})=\prod_np(\mathbf{x}_n, \mathbf{z}_n)$^ となることと同値であり、さらには、以下のように式変形ができる

    $$
    p(\mathbf{X}, \mathbf{Z})=\frac{\prod_{n=1}^Np(\mathbf{x}_n, \mathbf{z}_n|\boldsymbol{\theta})}{\sum_{\mathbf{Z}}\prod_{n=1}^Np(\mathbf{x}_n, \mathbf{z}_n|\boldsymbol{\theta})}=\prod_{n=1}^Np(\mathbf{z}_n|\mathbf{x}_n, \boldsymbol{\theta})
    $$

    - これは、混合ガウス分布の場合には、負担率がデータ自身 $\mathbf{x}_n$ とモデルパラメータにしか依存しないことを裏付ける

        （参考）混合ガウス分布の負担率は以下の通り

        $$
        \frac{\pi_k\mathcal{N}(\mathbf{x}|\boldsymbol{\mu}_k, \boldsymbol{\Sigma}_k)}{\sum_{j=1}^K\pi_j\mathcal{N}(\mathbf{x}|\boldsymbol{\mu}_j, \boldsymbol{\Sigma}_j)}
        $$


その他のモデル

- 事前分布を導入することで、MAP推定（事後分布を最大化する）も可能となる
- 手におえないM-stepに対応するために、**一般化EMアルゴリズム** (GEMアルゴリズム; generalized EM algorithm)
- EM-algorithm については、１データ点のみを用いる逐次型のEMアルゴリズムで扱うことができる[^2]
    - さらに、これはバッチ型に比べて早く収束するらしい
[^2]: [オンラインEMアルゴリズムで混合ガウス分布推論](https://shuyo.hatenablog.com/entry/20100426/online_em)
