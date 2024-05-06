---
title: "第２章: 確率分布"
---
# 第２章: 確率分布

様々な確率分布とそれらの特徴について説明する

- 観測値の有限集合 $\mathbf{x}_1, ..., \mathbf{x}_N$ が与えられた時、確率変数$\mathbf{x}$の確率分布$p(\mathbf{x})$をモデル化する、すなわち**密度推定**(density estimation)ことに用いられる
- 事前分布の関数の形が事後分布の形と同じになる事前分布のことを**共役事前分布**(conjugate prior)と呼ぶ
- ガウス分布のような一部のパラメータによって形状が決定されるような分布のことを**パラメトリック**(parametric)であるという
    - しかし、パラメータによって形状が決まるというのは制限でありデメリットでもある
    - このような時は、**ノンパラメトリック**(nonparametric)な密度推定が有効である

## 2.1: 二値変数

コイン投げのような二値確率変数$x = \{0, 1\}$の場合を考える

- これは**ベルヌーイ分布**(Bernoulli distribution)

    $$
    \text{Bern}(x|\mu) = \mu^x(1-\mu)^{1-x}
    $$

- この分布に対して最尤推定を行うと平均値は

    $$
    \mu_{ML} = \frac{1}{N}\sum_{n=1}^Nx_n
    $$

    - 尤度関数は個々の$x_n$には依存せず、$\sum_{n=1}^Nx_n$ にのみ依存する
        - このような和のことを**十分統計量**(sufficient statistic)という
    - 🧐 しかし、これは３回中３回とも偶然に表が出たデータを用いた場合、予測ではこのコインは生涯表が出るという明らかに破綻した予測になる
- $N$個のデータ集合の内、$x=1$となる観測値の数が$m$となる分布を考える
    - これは**二項分布**(binomial distribution)

        $$
        \text{Bin}(m|N, \mu) = \dbinom{N}{m}\mu^m(1-\mu)^{N-m}
        $$


### 2.1.1: ベータ分布

上記で説明したベルヌーイ分布及び二項分布のパラメータ$\mu$をベイズ主義的に推定する

- 初めに、事前分布$p(\mu)$を与える必要がある
    - 尤度関数が$\mu^x(1-\mu)^{1-x}$の形の因数の積になっていることに着目する
        - 事前分布が$\mu$及び$1-\mu$の冪乗に比例するように選ぶなら、事後分布は事前分布と同じ関数形式になる→**共役性**(conjugacy)
    - ベルヌーイ分布及び二項分布の事前分布として**ベータ分布**(beta distribution)を採用する

        $$
        \text{Beta}(\mu|a, b) = \frac{\Gamma(a+b)}{\Gamma(a)\Gamma(b)}\mu^{a-1}(1-\mu)^{b-1}
        $$

        - $\Gamma(x):$  **ガンマ関数**(gamma function)
        - $a, b:$  ハイパーパラメータ
    - 事後分布は尤度関数と事前分布の積に比例するので

        $$
        p(\mu|m, l, a, b)\propto\mu^{m+a-1}(1-\mu)^{l+b-1}
        $$

        とかける

        - $l=N-m$
        - $a$を１増やす事と$m$を１増やすことは等価値
            - $x=0$の場合も同様
            - 事前分布の$a, b$はそれぞれ$x=1, x=0$の**有効観測数**(effective number of observations)となっている
            - **逐次学習**(sequential learning)も可能
                - 元々存在するデータを加味した数値を$a, b$に適用すれば良い
- ベイズ学習を頻度主義者の立場で考える
    - すなわち、観測データの平均で分布を近似できるという特性
        - 🧐 無限の観測データがあればモデルを完全に復元できるというイメージ

        $$
        \mathbb{E}_\theta[\theta] = \mathbb{E}_{\mathcal{D}}[\mathbb{E}_\theta[\theta|\mathcal{D}]]
        $$

        - 事後平均をデータを生成する分布上で平均すると、事前分布に等しくなることを示している
            - 🧐 尤度推定はデータを生成する分布のうち１部を分布全体と仮定して計算を行っているからデータが少ない時に過学習を起こす？
            - 無限回の観測 $(m\rightarrow\infin, l\rightarrow\infin)$ を行えば、それは分布が得られたことと同値であるため上記の式が完全に成立する

## 2.2: 多値変数

相互に排他的な$K$個の可能な状態のうち一つを取るような離散変数を取り扱いたい

- 有名な例の一つとして1-of-$K$符号化法が挙げられる
    - 観測値を$\mathbf{x}=(0, 0, 1, 0, 0, 0)^\top$で表現する方法
    - $\mathbf{x}$ の分布は$\mathbf{x}_k = 1$となる確率を$\mu_k$とすると

        $$
        p(\mathbf{x}|\boldsymbol\mu) = \prod_{k=1}^K\mu_k^{x_k}
        $$

        で与えられる

        - 🧐 $x_k$以外0であるため、自動的に$\mu_k$が得られる式となっている
    - この式の尤度関数を解いて得られる最尤推定解は以下のようになっている
        - 解く際にはラグランジュ乗数を活用する

            $$
            \mu_k^{ML} = \frac{\sum_nx_{nk}}{N}\Bigl( = \frac{m_k}{N} \Bigr)
            $$

            - $x_{nk}:$  $n$個目の観測データ内の$k$番目のデータ
            - 尤度関数は個々の$x_{nk}$を考慮する必要なく、和だけ考えれば良い
                - この分布は**十分統計量(**sufficient statistic)
    - パラメータ$\boldsymbol \mu$と観測数$N$が与えられた条件下で、以下のような同時分布を考える

        $$
        \text{Mult}(m_1, ..., m_K|\boldsymbol \mu, N) = \dbinom{N}{m_1m_2...m_K}\prod_{k=1}^K\mu_k^{m_k}
        $$

        - **多項分布**(multinomial distribution)として知られている

### 2.2.1: ディリクレ分布

ベイズ主観的な推定を多項分布に対しても行いたいので、事前分布を与える

- この時、ベータ分布と同じように共役性を考慮する

    $$
    p(\boldsymbol \mu|\boldsymbol\alpha) \propto \prod_{k=1}^K \mu_k^{\alpha_k-1}
    $$

    - この時、$\{\mu_k\}$は総和の制約から$K-1$次元の**単体**(simplex)上に制限されて分布する
        - 🧐 ちょっと意味わからん
    - 正規化すると、以下のようになる

        $$
        \text{Dir}(\boldsymbol \mu|\boldsymbol\alpha) = \frac{\Gamma(\sum_{k=1}^K\alpha_k)}{\Gamma(\alpha_1)...\Gamma(\alpha_K)} \prod_{k=1}^K \mu_k^{\alpha_k-1}
        $$

        - これを**ディリクレ分布**(Dirichlet distribution)と呼ぶ

## 2.3: ガウス分布

様々な場面で現れ、利用価値も様々な**ガウス分布**(Gaussian distribution)を紹介

- エントロピーを最大化する分布はガウス分布
- 確率変数の和を考える時も現れる
    - ラプラスによる**中心極限定理**(central limit theorem)によれば、ある緩やかな条件の下で足し合わされる変数の数が増えるに従って、徐々にガウス分布に従うようになる
- ガウス分布の幾何的な形状
    - 指数部分に現れる二次形式

        $$
        \Delta^2 = (\mathbf{x}-\boldsymbol\mu)^\top\Sigma^{-1}(\mathbf{x}-\boldsymbol\mu)
        $$

        は**マハラノビス距離**(Mahalanobis distance)と呼ばれる

        - これは、相関を考慮した距離計算が可能になる
        - $\Sigma$が単位行列の場合はユークリッド距離に一致する
        - 固有値計算を行うと、二次形式は以下のように書き換えることができる

            $$
            \Delta^2 = \sum_{i=1}^D\frac{y_i^2}{\lambda_i}
            $$

            - $y_i = \mathbf{u}_i^\top(\mathbf{x}-\boldsymbol\mu):$  すなわち、元の座標系を並行移動し回転した正規直交ベクトルにて定義された新しい座標系と捉えることができる
    - $y_i$で定義された新しい座標系でのガウス分布の形を考える
        - **ヤコビ行列** $\mathbf{J}$ (Jacobian matrix)を活用する
        - これによって多変量ガウス分布は$D$個の独立な１変数ガウス分布の積で表すことができる

            $$
            p(\mathbf{y}) = p(\mathbf{x})|\mathbf{J}| = \prod_{j=1}^D\frac{1}{(2\pi \lambda_j)^{1/2}}\exp \Bigl\{-\frac{y_j^2}{2\lambda_j}\Bigr\}
            $$

            - 同時確率分布を独立な分布の積に分解できるように固有ベクトルによって平行移動や回転をした
- ガウス分布のモーメントを考える
    - １次のモーメント
        - $\mathbf{z} = \mathbf{x} - \boldsymbol \mu$ とおいて考える
        - 指数部分の偶関数性を活用する

            $$
            \mathbb{E}[\mathbf{x}] = \boldsymbol\mu
            $$

    - ２次のモーメント
        - 単一変数の場合では、$\mathbb{E}[x^2]$を考えたが、多変量の場合は$\mathbb{E}[x_ix_j]$で与えられる$D^2$個の２次モーメントを考える必要がある
            - 但し、$\mathbb{E}[\mathbf{x}\mathbf{x}^\top]$の形でまとめて考えることが可能
        - $\mathbf{z} = \mathbf{x} - \boldsymbol \mu$ とおいて考える
        - １次の時と同様に、指数部分の偶関数性を利用する$(\mathbf{z}\boldsymbol\mu^\top, \boldsymbol\mu\mathbf{z}^\top)$
        - また、固有ベクトル展開を活用する $(\mathbf{z} = \sum_{j=1}^Dy_j\mathbf{u}_j)$

        $$
        \mathbb{E}[\mathbf{x}\mathbf{x}^\top] = \boldsymbol \mu \boldsymbol \mu ^\top + \Sigma
        $$

    - 確率変数の**共分散**(covariance)も定義する

        $$
        \begin{align*}
        \text{cov}[\mathbf{x}] &= \mathbb{E}[(\mathbf{x} - \mathbb{E}[\mathbf{x}])(\mathbf{x} - \mathbb{E}[\mathbf{x}])^\top] \\
        &= \Sigma
        \end{align*}
        $$

- ガウス分布は密度モデルとして広く利用されるものの重大な制限がある
    - パラメータの総数が$D$に対して２乗の割合で増加し、大規模な行列を計算することが困難になりうる
        - 対処法の一つは共分散行列に制限を加える
            - 対角: $\Sigma = \text{diag}(\sigma_i^2)$
            - 単位行列に比例: $\Sigma = \sigma^2\mathbf{I}$
                - しかし、形式を大きく制限したためにデータ中の興味深い相関を捉える能力を制限してしますという問題もある
    - ガウス分布は本質的に**単峰形**(極大値が１つ)という制限がある
        - 上記の問題点に対処するために**潜在変数**(latent variable)(**隠れ変数**(hidden variable)や**非観測変数**(unobserved variable)ともいう)を導入する
            - データ集合中の支配的な相関を捉えることが可能
- これら２つのアプローチを組み合わせたり、拡張したりして、階層的モデル群を形成することにより、現実の問題に幅広く適用することができるようになる
    - **マルコフ確率場**(Markov random field)
        - 画像の確率モデルとして幅広く利用されている
    - **線形動的システム**(linear dynamical system)
        - トラッキングの応用など時系列データのモデル化に用いられている

### 2.3.1: 条件付きガウス分布

多変量ガウス分布の重要な特性について考察する

- 2つの変数集合のときはがガウス分布に従うなら
1. 一方の変数集合が与えられた時の、もう一方の集合の条件付き分布もガウス分布になる
2. どちらの変数集合の周辺分布も同様にガウス分布になる
- $\mathbf{x}:$  ガウス分布$\mathcal{N}(\mathbf{x}|\boldsymbol\mu, \Sigma)$に従う$D$次元ベクトル
    - このベクトルを２つの互いに素な部分集合に分割する

        $$
        \mathbf{x} =
        \begin{pmatrix}
        \mathbf{x}_a \\ \mathbf{x}_b
        \end{pmatrix}
        $$

    - 上記の部分集合に対応した平均ベクトルおよび共分散行列も以下のように定義する


        $$
        \boldsymbol\mu =
        \begin{pmatrix}
        \boldsymbol\mu_a \\ \boldsymbol\mu_b
        \end{pmatrix}
        $$

        $$
        \Sigma =
        \begin{pmatrix}
        \Sigma_{aa} & \Sigma_{ab} \\ \Sigma_{ba} & \Sigma_{bb}
        \end{pmatrix}
        $$

        - 共分散行列の対称性 $\Sigma^\top = \Sigma$ から
            - $\Sigma_{aa}, \Sigma_{bb}$ も対称となる
            - $\Sigma_{ab} = \Sigma_{ba}^\top$
    - 共分散行列の逆行列を考えた方が便利なことが多いので**精度行列**(precision matrix)として $\Lambda$ を定義する

        $$
        \Lambda \equiv \Sigma^{-1}
        $$

- 条件付き分布 $p(\mathbf{x}_a|\mathbf{x}_b)$の表現を見つける
    - 同時分布$p(\mathbf{x}) = p(\mathbf{x}_a, \mathbf{x}_b)$を用いて条件付き分布を計算する
    - ガウス分布の指数部分の二次形式について考えることで効率よく計算可能
    - ガウス分布の計算に**平方完成**(completing the square)をよく用いる

        $$
        (ガウス分布の２次形式) = -\frac{1}{2}\mathbf{x}^\top\Sigma^{-1}\mathbf{x} + \mathbf{x}^\top\Sigma^{-1}\boldsymbol\mu + \text{const}
        $$

        - $\text{const}:$  $\mathbf{x}$に非依存な項
    - 上記で定義した部分集合を用いて$\boldsymbol\mu_{a|b}, \Sigma_{a|b}$を計算する
        1. ガウス分布の二次形式を上記で定義した部分集合を用いて書き表す
        2. $\mathbf{x}_b$を定数とみなして$\mathbf{x}_a$の関数的依存性を考える
        3. 係数比較を用いると以下のような結果が得られる

            $$
            \begin{align*}
            \Sigma_{a|b} &= \Lambda_{aa}^{-1} \\
            \boldsymbol\mu_{a|b} &= \boldsymbol\mu_a - \Lambda_{aa}^{-1}\Lambda_{ab}(\mathbf{x}_b - \boldsymbol\mu_b)
            \end{align*}
            $$

    - 精度行列に対して、共分散行列を用いて表現することも可能である
        - 分割された行列の逆行列に関する以下の公式を活用することで計算できる

            $$
            \begin{pmatrix}
            \mathbf{A} & \mathbf{B} \\
            \mathbf{C} & \mathbf{D}
            \end{pmatrix}^{-1} =
            \begin{pmatrix}
            \mathbf{M} & \mathbf{-MBD^{-1}} \\
            \mathbf{-D^{-1}CM} & \mathbf{D^{-1}+D^{-1}CMBD^{-1}}
            \end{pmatrix}
            $$

            - 但し、$\mathbf{M=(A-BD^{-1}C)^{-1}}$
                - $\mathbf{M}^{-1}$は部分行列$\mathbf{D}$に関する**シューア補行列**(Schur complement matrix)

        $$
        \begin{align*}
        \Sigma_{a|b} &= \boldsymbol\mu_a+\Sigma_{ab}\Sigma_{bb}^{-1}(\mathbf{x}_b-\boldsymbol\mu_b) \\
        \boldsymbol\mu_{a|b} &= \Sigma_{aa} - \Sigma_{ab}\Sigma_{bb}^{-1}\Sigma_{ba}
        \end{align*}
        $$

        - 条件付き分布 $p(\mathbf{x}_a|\mathbf{x}_b)$の平均は$\mathbf{x}_b$の線形関数であり、共分散行列は$\mathbf{x}_b$とは独立である→**線形ガウスモデル**(linear-Gaussian model)

### 2.3.2: 周辺ガウス分布

周辺ガウス分布について考える

$$
p(\mathbf{x}_a) = \int p(\mathbf{x}_a, \mathbf{x}_b) ~d\mathbf{x}_b
$$

- こちらでも2.3.1節と同様に二次形式に着目し、同様の手順を踏まえる
- 結果は以下の通り

    $$
    \begin{align*}
    \mathbb{E}[\mathbf{x}_a] &= \boldsymbol\mu_a \\
    \text{cov}[\mathbf{x}_a] &= \Sigma_{aa}
    \end{align*}
    $$

    - 直感にかなり沿った非常に簡潔な表現

### 2.3.3: ガウス変数に対するベイズの定理

以後の章で頻繁に現れる以下のような問題に対する一般的な結果を算出しておく

given:

1. ガウス周辺分布$p(\mathbf{x})$
2. 平均が$\mathbf{x}$の線形関数で共分散行列が$\mathbf{x}$とは独立であるようなガウス条件付き分布$p(\mathbf{y}|\mathbf{x})$
    - **線形ガウスモデル**(linear Gaussian model)の例

goal:

1. ガウス周辺分布$p(\mathbf{y})$
2. 条件付き分布$p(\mathbf{x}|\mathbf{y})$
- 上記の条件を数式化すると以下のようになる

    $$
    \begin{align*}
    p(\mathbf{x}) &= \mathcal{N}(\mathbf{x}|\boldsymbol\mu, \Lambda^{-1}) \\
    p(\mathbf{y}|\mathbf{x}) &= \mathcal{N}(\mathbf{y}|\mathbf{A}\mathbf{x}+\mathbf{B}, \mathbf{L}^{-1})

    \end{align*}
    $$

    - $\Lambda, \mathbf{L}:$  各ガウス分布に対する精度行列
- 初めに以下のような$\mathbf{x}$と$\mathbf{y}$の同時分布を考える

    $$
    p(\mathbf{z}) = p(\mathbf{x}, \mathbf{y}) = p(\mathbf{y}|\mathbf{x})~p(\mathbf{x})
    $$

    - 二次形式部分に着目したいため対数を考える
        - $\mathbf{z}$の二次形式なので$p(\mathbf{z})$もガウス分布
        - 上記の章での手順に倣って、二次の項から$p(\mathbf{z})$の精度行列を計算する
            - $(\ln p(\mathbf{z})の二次の項) = -1/2~\mathbf{z^\top Rz}$ となるように式変形する

            $$
            \mathbf{
            R =
            \begin{pmatrix}
            \mathbf{\Lambda + A^\top L A} & \mathbf{-A^\top L} \\
            \mathbf{-LA} & \mathbf{L}
            \end{pmatrix}

            }
            $$

        - 逆行列を計算することで共分散行列も計算可能

            $$
            \mathbf{\text{cov}[z] = R^{-1} =
            \begin{pmatrix}
            \mathbf{\Lambda^{-1}} & \mathbf{\Lambda^{-1}A^\top} \\
            \mathbf{A\Lambda^{-1}} & \mathbf{L^{-1} + A\Lambda^{-1}A^\top}

            \end{pmatrix}
            }
            $$

        - 平均に関しては一次の項に着目する

        $$
        \mathbf{\mathbb{E}[z] = R^{-1}
        }
        \begin{pmatrix}
        \mathbf{\Lambda\boldsymbol\mu - A^\top L b} \\
        \mathbf{Lb}
        \end{pmatrix} = \begin{pmatrix}
        \mathbf{\boldsymbol\mu} \\
        \mathbf{A\boldsymbol\mu + b}
        \end{pmatrix}
        $$

- 次に周変化した周辺分布$p(\mathbf{y})$を考える
    - 2.3.2節で得られた結果を活用する

        $$
        \begin{align*}
        \mathbf{\mathbb{E}[y]} &= \mathbf{A\boldsymbol\mu+b}\\
        \mathbf{\text{cov}[y]} &= \mathbf{L^{-1} + A\Lambda^{-1}A^\top}
        \end{align*}
        $$

        - 特に$\mathbf{A=I}$ (2つのガウス分布$\mathcal{N}(\mathbf{x|\boldsymbol\mu, \Lambda^{-1}}), \mathcal{N}(\mathbf{y|b, L^{-1}})$のたたみ込みになる) の場合は、平均及び共分散行列は元のガウス分布の和になる
- 最後に条件付き分布$p(\mathbf{x}|\mathbf{y})$を考える
    - 2.3.1節で得られた結果を活用する

        $$
        \begin{align*}
        \mathbf{\mathbb{E}[x|y]} &= \mathbf{(\Lambda+A^\top LA)^{-1}\{A^\top L(y-b)+\Lambda\boldsymbol\mu\}}\\
        \mathbf{\text{cov}[x|y]} &= \mathbf{(\Lambda+A^\top LA)^{-1}}
        \end{align*}
        $$


### 2.3.4: ガウス分布の最尤推定

多変量ガウス分布から観測値$\{x_n\}$が独立に得られたと仮定したデータ集合に対して、分布のパラメータを最尤推定法で推定する

- 確率密度関数$p(\mathbf{x}|\boldsymbol\mu, \Sigma)$から対数尤度関数を計算し、それを最小化する値を計算すれば良い

$$
\begin{align*}
\boldsymbol\mu_{ML} &= \frac{1}{N}\sum_{n=1}^N\mathbf{x}_n \\
\Sigma_{ML} &= \frac{1}{N}\sum_{n=1}^N(\mathbf{x}_n - \boldsymbol\mu_{ML})(\mathbf{x}_n - \boldsymbol\mu_{ML})^\top
\end{align*}
$$

- 真の分布のもとで上記で計算した最尤推定解の期待値を評価する

    $$
    \begin{align*}
    \mathbb{E}[\boldsymbol\mu_{ML}] &= \boldsymbol\mu \\
    \mathbb{E}[\Sigma_{ML}] &= \frac{N-1}{N}\Sigma
    \end{align*}
    $$

    - 共分散行列に関しては過小評価されているため不偏推定量を計算するには調整が必要
- 🧐 ただ頻度主義的な考えで計算を行っただけ

### 2.3.5: 逐次推定

データ点を一度に１つずつ処理してはそれを破棄すること

- 以下のケースで主に活躍する
    - オンラインや応用分野
    - 全てのデータを一度に一括処理することが不可能な大規模データ集合
- 平均の最尤推定量$\boldsymbol\mu_{ML}$に関して具体的に考える
    - 最後のデータ点$\mathbf{x}_N$に着目する

        $$
        \boldsymbol\mu_{ML}^{(N)} = \boldsymbol\mu_{ML}^{(N-1)} + \frac{1}{N}(\mathbf{x}_N-\boldsymbol\mu_{ML}^{(N-1)})
        $$

    - しかし、上記の方法で確実に逐次アルゴリズムを導出することが可能とは限らない
- 汎用的な逐次学習の定式化がなされた**Robbins-Monro**アルゴリズム(Robbins-Monro algorithm)を考える
    - 確率変数のパラメータ$\theta$とそれに依存する確率変数$z$の同時分布$p(z, \theta)$を考える
        - $\theta$が与えられた時の条件付き期待値 $\mathbb{E}[z|\theta]$を**回帰関数**$f(\theta)$として定義する
    - ここでの目標は$f(\theta^*) = 0$を満たす$\theta^*$を計算すること
        - 大規模なデータ集合があれば回帰関数を直接モデル化することも可能だが、今回は逐次的な方法に限定して考える
- 根 $\theta^*$の連続的推定の系列は以下のように定義する

    $$
    \theta^{(N)} = \theta^{(N-1)} - a_{N-1}z(\theta^{(N-1)})
    $$

    - $a_{N-1}$や$f(\theta)$に関して満たすべき条件は存在することに注意
    - 負の対数尤度関数に対して、$a_{N} = \sigma^2/N$となるように選ぶと、平均の最尤推定量を求める式になる
        - 🧐 要するに、(微分値) = 0を満たすようなパラメータを計算することで最大値を計算できるよねというお話(確率自身が0になる時を求めているのかと勘違いしていた…)
    - ある条件を満たした連続的推定の系列の目標の根は”確実に”収束する

        $$
        \lim_{N\rightarrow\infin}\theta^{(N)} = \theta^{*}
        $$


### 2.3.6: ガウス分布に対するベイズ推論

パラメータ上の事前分布を導入して、ベイズ主義的な扱い方を導く

1. 分散 $\sigma^2$ は既知として、平均 $\mu$ を推定する
    - 🧐 平均 $\mu$ に関する事後分布を計算したい、というお気持ち

    $$
    p(\bm{x} | \mu) = \prod_{n=1}^N~p(x_n|\mu) = \frac{1}{(2\pi\sigma^2)^{N/2}} \exp\Bigl\{ -\frac{1}{2\sigma^2} \sum_{n=1}^N(x_n - \mu)^2\Bigr\}
    $$

    - 事前分布に**ガウス分布**を選べば、**共役事前分布**(conjugate prior)となる

    $$
    p(\mu) = \mathcal{N}(\mu|\mu_o, \sigma^2_o)
    $$

    - 事後分布は以下の関係を満たす

        $$
        p(\mu|\bm{x}) \propto p(\bm{x}|\mu)~p(\mu)
        $$

    - 上記の関係を元に、事後分布を事前分布と尤度関数から計算する

        $$
        p(\mu|\bm{x}) = \mathcal{N}(\mu|\mu_N, \sigma^2_N)
        $$

    - 平均$\mu_N$と分散$\sigma^2_N$は以下の通り

        $$
        \begin{align*}
        \mu_n &= \frac{\sigma^2}{N\sigma^2_o+\sigma^2}\mu_o + \frac{N\sigma_o^2}{N\sigma^2_o+\sigma^2}\mu_{ML} \\
        \frac{1}{\sigma^2_N} &= \frac{1}{\sigma^2_o} + \frac{N}{\sigma^2}
        \end{align*}
        $$

        - $\mu_{ML}:$  最尤推定解(サンプル平均)
    - 事後分布の平均$\mu_n$
        - $N=0$ ならば事前分布の平均
        - $N\rightarrow\infin$ならば最尤推定解
    - 事後分布の分散$\sigma_n^2$
        - $N$が大きくなるほど小さくなる

             ☞  分解能が上がる ($N\rightarrow\infin$で無限に尖った密度になる)

- ベイズ推論を逐次推定の視点で捉えることは非常に汎用的
    - 🧐 (N個のデータ点観測後) = (N-1個のデータ点観測後) * (尤度関数)

        $$
        p(\mu|\bm{x}) \propto \Bigl[ p(\mu)\prod_{n=1}^{N-1} p(x_n|\mu) \Bigr]~p(x_N|\mu)
        $$

        - $p(x_N|\mu):$  逐次的な要素
2. 平均を既知として、分散を推定する

    $$
    p(\bm{x}|\lambda) = \prod_{n=1}^N\mathcal{N}(x_n|\mu, \lambda^{-1})\propto\lambda^{N/2}~\exp\Bigl\{ -\frac{\lambda}{2}\sum_{n=1}^N(x_n-\mu)^2 \Bigr\}
    $$

    - 事前分布に**ガンマ分布**を選べば共役事前分布となる

        $$
        \text{Gam}(\lambda|a, b) = \frac{1}{\Gamma(a)}b^a\lambda^{a-1}\exp(-b\lambda)
        $$

    - 事後分布は以下の通り

        $$
        p(\lambda|\bm{x}) = \text{Gam}(\lambda|a_N, b_N)
        $$

    - 各パラメータは以下の通り

        $$
        \begin{align*}
        a_N &= a_o + \frac{N}{2} \\
        b_N &= b_o + \frac{N}{2}\sigma^2_{ML}
        \end{align*}
        $$

        - $\sigma_N^2:$  最尤推定解(サンプル分散)
    - 上記の事後分布より
        - 観測データが１増えると$a_N$は $N/2$ 増える
            - 元々$2a_o$個の有用な観測値が事前に与えられていると解釈できる
        - 観測データが１増えると$b_N$は $N\sigma_{ML}^2/2$ 増える
            - 元々$2a_o$個の有用な観測値が事前に与えられていると解釈できる
                - これは$\sigma_{ML}^2=b_o/a_o$ならば成立する
                - 🧐 最尤推定解は観測データに依存するので、それに基づいて超パラメータを設定するということ？
    - 精度パラメータではなく分散そのものについて考える場合は、**逆ガンマ分布**(inverse gamma distribution)を考える必要がある
        - しかし、精度で考えた方が綺麗に示されるため略
3. 平均と精度が両方未知の場合

    $$
    \begin{align*}
    p(\bm{x}|\mu, \lambda) &= \prod_{n=1}^N{\Bigl(\frac{\lambda}{2\pi}\Bigr)}^{1/2}\exp\Bigl\{ -\frac{\lambda}{2}(x_n - \mu)^2 \Bigr\} \\
    &\propto \Bigl[ \lambda^{1/2}\exp \Big(-\frac{\lambda\mu^2}{2} \Big)\Bigr]^N\exp\Bigl\{ \lambda\mu\sum_{n=1}^Nx_n  - \sum_{n=1}^N x_n^2 \Bigl\}
    \end{align*}
    $$

    - 関数依存性を丁寧に考えると以下のような関係式が得られる

        $$
        p(\mu, \lambda) = \mathcal{N}(\mu|\mu_o, (\beta\lambda)^{-1})\text{Gam}(\lambda|a, b)
        $$

        - これは**正規-ガンマ分布**(normal-gamma distribution) or **ガウス-ガンマ分布**(Gaussian-gamma distribution)と呼ばれる
        - 独立な$\mu$上のガウス事前分布と$\lambda$上のガンマ事前分布の単純な積**ではない**
            - $\mu$の分布の精度は$\lambda$の線形関数になっているから
- $D$次元変数の多変量ガウス分布$\mathbf{\mathcal{N}(x|\boldsymbol\mu, \Lambda^{-1})}$の場合を考える
    - 精度が既知であれば、平均$\boldsymbol\mu$の共役事前分布は同様にガウス分布となる
    - 平均が既知であれば、精度行列$\mathbf{\Lambda}$の協約事前分布は**ウィシャート分布**(Wishart distribution)になる
    - どちらも未知であれば、**正規-ウィシャート分布**(normal-Wishart distribution) or **ガウス-ウィシャート分布**(Gaussian-Wishart distribution)になる

### 2.3.7: スチューデントの $\mathbf{t}$ 分布

ガウス分布に関する応用分布をひとつ紹介する

- １変数のガウス分布において以下の処理を施す
    - ガンマ分布を精度の事前分布とする
    - 精度を積分消去する
    - $z = \tau[b+(x-\mu)^2/2]$ の変数置換を用いる

    $$
    \begin{align*}
    p(x|\mu, a, b) &= \int_0^\infin\mathcal{N}(x|\mu, \tau^{-1})\text{Gam}(\tau|a, b)d\tau \\
    &= \frac{b^a}{\Gamma(a)}\Big(\frac{1}{2\pi}\Big)^{1/2}\Big[b+\frac{(x-\mu)^2}{2}\Big]^{-a-1/2}\Gamma(a+1/2)
    \end{align*}
    $$

    - $\nu=2a, \lambda=a/b$のパラメータを新たに定義すると以下のようになる

    $$
    \text{St}(x|\mu, \lambda, \nu) = \frac{\Gamma(\nu/2+1/2)}{\Gamma(\nu/2)}\Big(\frac{\lambda}{\pi\nu}\Big)^{1/2}\Big[1+\frac{\lambda(x-\mu)^2}{\nu}\Big]^{-\nu/2-1/2}
    $$

    - これは、**スチューデントの $\mathbf{t}$ 分布**(Student’s t-distribution)として知られる
        - $\lambda:$  **精度**(precision)
            - 但し、分散の逆数とは”限らない”
        - $\nu:$  **自由度**(degree of freedom)
            - $\nu=1:$  **コーシー分布**
            - $\nu\rightarrow\infin:$  平均$\mu$で精度が$\lambda$のガウス分布
        - 式から平均は同じ$(=\mu)$だが、精度は異なる(→$\tau$で積分)ようなガウス分布を無限個足し合わせたものとわかる
            - ガウス分布の無限混合分布
        - 一般的にガウス分布より”すそ”が長い→**頑健性**(robustness)
        - $\mathbf{t}$ 分布に対する最尤推定会はEMアルゴリズムによって得られる
    - パラメータ定義を$\nu=2a, \lambda=a/b, \eta=\tau a/b$と置き換えると以下のようになる

        $$
        p(x|\mu, \lambda, \nu) = \int_0^\infin\mathcal{N}(x|\mu, (\eta\lambda)^{-1})\text{Gam}(\eta|\nu/2, \nu/2)d\eta
        $$

        - これは多変量ガウス分布の場合にも一般化でき、多変量スチューデント $\mathbf{t}$ 分布に相当するものを計算できる

### 2.3.8: 周期変数

ガウス分布を周期性に対応させる

- ガウス分布はより複雑な確率モデルを構成する要素としても非常に重要だが、連続変数の密度モデルとして不適切な場合がある
    - その一つが周期変数
        - 特定の地理的な位置での風向など
    - 角座標(極座標)の利用を考える
- ある方向を原点に選んだ周期変数を使いたいと考える
    - しかしこれでは、原点の選択に強く依存した結果になる
        - ex.) $\theta_1=1\degree, \theta_2=359\degree$の場合
        - 原点が$0\degree$→ サンプル平均: $180\degree$, 標準偏差$179\degree$
        - 原点が$180\degree$→ サンプル平均: $0\degree$, 標準偏差$1\degree$
- ここでは周期変数の観測値集合 $\mathcal{D}=\{\theta_1, ..., \theta_N\}$の平均を求める問題を考える
    - 平均の普遍な尺度を求める為に、観測値は単位上の点と見る
        - $||x_n|| = 1$を満たす二次元単位ベクトルで観測値を表せる
    - よって、平均ベクトルは以下の通り

        $$
        \overline{\mathbf{x}} = \frac{1}{N}
        \begin{pmatrix}
        \sum_{n=1}^N\cos\theta_n\\
        \sum_{n=1}^N\sin\theta_n
        \end{pmatrix}
        $$

    - 上記の式を$\overline{\theta}$について解く

        $$
        \overline{\theta} = \tan^{-1}\Big\{ \frac{\sum_{n}\sin\theta_n}{\sum_{n}\cos\theta_n} \Big\}
        $$

- ガウス分布の周期変数への一般化を考える
    - 慣例により、周期$2\pi$の分布$p(\theta)$について考える
    - ２変数のガウス分布を変数変換させる事で以下のような結果が得られる

    $$
    p(\theta|\theta_0, m) = \frac{1}{2\pi I_0(m)}\exp\{m\cos(\theta-\theta_0)\}
    $$

    - これを**フォン・ミーゼス分布**(von Mises distribution)や**循環正規分布**(circular normal distribution)と呼ぶ
    - $\theta_0:$  分布の平均
    - $m(=r_0/\sigma^2):$  **集中度パラメータ**(concentration parameter)
    - $I_0(m):$  (0次の第1種変形)ベッセル関数(zeroth-order Bessel modified function of the first kind)
    - $\theta_0$の最尤推定量は上記で提示した$\overline{\theta}$と等しくなる
    - $m$の最尤推定量は以下の通り
        - $I'_0(m) = I_1(m)$ を利用する

        $$
        \begin{align*}
        A(m_{ML}) &= \frac{1}{N}\sum_{n=1}^N\cos(\theta_n-\theta^{ML}_0)\\
        &= \bigg(\frac{1}{N}\sum_{n=1}^N\cos\theta_n\bigg)\cos\theta^{ML}_0 + \bigg(\frac{1}{N}\sum_{n=1}^N\sin\theta_n\bigg)\sin\theta^{ML}_0
        \end{align*}
        $$

    - 右辺は容易に評価できる
    - 但し、$A(m)$は以下の通り
        - この関数の逆関数は数値的に求めることが可能

        $$
        A(m) = \frac{I_1(m)}{I_0(m)}
        $$

- 周期分布を生成する他の手法
    - ヒストグラム
        - 重大な制限あり
    - 単位円上の条件付けではなく、周辺化するアプローチ
    - 実数軸上の任意の妥当な分布を$2\pi$の間隔で連続する区間を周期変数$(0, 2\pi)$に写像する
- フォン・ミーゼス分布の制限の一つは単峰であること
    - 混合分布を用いれば多峰性を扱えるようになる

### 2.3.9: 混合ガウス分布

単一のガウス分布では捉えられない多峰性のあるデータを捉えたい

- 基本的な分布をいくつか線型結合して定式化された確率モデルのことを**混合分布**(mixture distribution)という
    - そのうちの例である**混合ガウス分布**(mixture of Gaussians)は以下の通り

    $$
    p(\mathbf{x}) = \sum_{k=1}^K\pi_k\mathcal{N}(\mathbf{x}|\boldsymbol\mu_k, \mathbf{\Sigma}_k)
    $$

    - $\pi_k:$  **混合係数**(mixing coefficient)
        - これは確率の条件を満たしている
    - 各ガウス分布は**混合要素**(mixture component)と呼ばれる
- 以下のように上式を捉えてみる
    - $\pi_k=p(k):$  $k$番目の混合分布を選択する事前分布
    - $\mathcal{N}(\mathbf{x}|\boldsymbol\mu_k, \mathbf{\Sigma}_k)=p(\mathbf{x}|k):$  $k$が与えられた時の$\bm{x}$の条件付き密度
    - この時の$\bm{x}$の周辺密度は

        $$
        p(\mathbf{x}) = \sum_{k=1}^Kp(k)p(\mathbf{x}|k)
        $$

        で与えられる

    - 事後確率 $p(k|\bm{x})$ は**負担率**(responsibility)と呼ばれる
- 混合分布のパラメータをも求める方法
    - 最尤推定法
        - 解析解を得ることは不可能なので数値最適化手法やEMアルゴリズムを活用する

## 2.4: 指数型分布族

今まで紹介してきた確率分布の共通点を述べる

- 今まで紹介したものたちは(混合ガウス分布を除いて)**指数型分布族**と呼ばれる大きな族の例となっている

    $$
    p(\mathbf{x}|\bm{\eta}) = h(\mathbf{x})g(\bm{\eta})\exp(\boldsymbol{\eta}^\top\mathbf{u}(\mathbf{x}))
    $$

    - $\bm{\eta}:$  **自然のパラメータ**(natural parameter)
    - $\mathbf{u}(\mathbf{x}):$  任意の関数
    - $g(\bm{x}):$  分布を正規化するための係数
    - 🧐 イメージは → $(密度関数) = (正則化)*(xの関数)*\exp(xの関数)$
- 初めに、**ベルヌーイ分布**(Bernoulli distribution)について考える

    $$
    \text{Bern}(x|\mu) = \mu^x(1-\mu)^{1-x}
    $$

    - これを上記の式の形になるように変形する

        $$
        p(x|\eta) = \sigma(-\eta)\exp(\eta x)
        $$

    - 但し、$\sigma(\eta)$は以下の通り

        $$
        \sigma(\eta) = \frac{1}{1+\exp(-\eta)}
        $$

        - これは、**ロジスティックシグモイド関数**(logistic sigmoid function)と呼ばれている
        - また、$\mu=\sigma(\eta)$の関係がある
- 続いて、**多項分布**(multinomial distribution)について考える

    $$
    p(\mathbf{x}|\boldsymbol\mu) = \prod_{k=1}^M\mu_k^{x_k}
    $$

    - これを上記の式の形になるように変形する

        $$
        p(\mathbf{x}|\boldsymbol\eta) = \bigg(1+\sum_{k=1}^{M-1}\exp(\eta_k)\bigg)^{-1}\exp(\boldsymbol\eta^\top\mathbf{x})
        $$

    - 但し、$\eta_k$と$\mu_k$は以下の関係にある

        $$
        \mu_k = \frac{\exp(\eta_k)}{1+\sum_j\exp(\eta_j)}
        $$

        - これは、**ソフトマックス関数**(softmax function)や**正規化指数関数**(normalized exponential function)と呼ばれている
- 最後にガウス分布について考える

    $$
    \mathcal{N}(x~|~\mu, \sigma^2) = \frac{1}{\sqrt{2\pi \sigma^2}}\exp\Bigl\{-\frac{1}{2\sigma^2}(x-\mu)^2\Bigr\}
    $$

    - これを上記の式の形になるように変形する

        $$
        \mathcal{N}(x~|~\mu, \sigma^2) = \frac{1}{\sqrt{2\pi \sigma^2}}\exp\Bigl\{-\frac{1}{2\sigma^2}x^2+\frac{\mu}{\sigma^2}x-\frac{1}{2\sigma^2}\mu^2\Bigr\}
        $$

    - これは以下のような対応関係で見ると良い
        - 🧐 二次形式の定数部分は外に出す

        $$
        \begin{align*}
        \boldsymbol\eta &= \begin{pmatrix}\mu/\sigma^2\\-1/2\sigma^2\end{pmatrix}\\
        \mathbf{u}(x) &= \begin{pmatrix}x\\x^2\end{pmatrix}\\
        h(x) &= (2\pi)^{-1/2} \\
        g(\boldsymbol\eta) &= (-2\eta_2)^{1/2}\exp\bigg(\frac{\eta_1^2}{4\eta_2}\bigg)
        \end{align*}
        $$


### 2.4.1: 最尤推定と十分統計量

一般的な指数型分布族のパラメータベクトルを最尤推定で推定する

- $\boldsymbol\eta$ についての勾配を考えると、以下のような関係が得られる

    $$
    -\nabla\ln g(\boldsymbol\eta) = \mathbb{E}[\mathbf{u}(\mathbf{x})]
    $$

    - 🧐 要するに、$\mathbf{u}(\mathbf{x})$ の期待値は正規化項のみで計算できる
    - $\mathbf{u}(\mathbf{x})$ の共分散を含む高次のモーメントは$g(\boldsymbol\eta)$の２次形式で表せる
        - 指数型分布族の分布を正規化できれば、その分布のモーメントは単に微分すればいつでも計算できる
- 独立に同分布に従うデータの集合$\mathbf{X = \{x_1, ..., x_N\}}$を考える
    - 上記と同様に$\boldsymbol\eta$ についての勾配を考えるので、先ほど得られた関係式を用いると

        $$
        -\nabla\ln g(\boldsymbol\eta_{ML}) = \frac{1}{N}\sum_{n=1}^N\mathbf{u}(\mathbf{x}_n)
        $$

        - この式を解けば$\boldsymbol\eta_{ML}$が得られる
        - 最尤推定解はデータ$\sum_{n=1}^N\mathbf{u}(\mathbf{x}_n)$にのみ依存し、個々には依存しない
            - $\sum_{n=1}^N\mathbf{u}(\mathbf{x}_n)$を分布の**十分統計量**(sufficient statistic)と呼ぶ
            - データの和だけ保存していれば良いことがわかる

### 2.4.2: 共役事前分布

一般的な指数型分布族の共役な事前分布について考える

- 事前分布は以下のように与えられる

    $$
    p(\boldsymbol\eta|\boldsymbol\chi, \nu) = f(\boldsymbol\chi, \nu)g(\boldsymbol\eta)^\eta\exp\{\nu\boldsymbol\eta^\top\boldsymbol\chi\}
    $$

    - $f(\boldsymbol\chi, \nu):$  正規化係数
- 上記の事前分布を用いて事後分布を計算することで共役性を確認する

    $$
    p(\boldsymbol\eta|\mathbf{X}, \boldsymbol\chi, \nu) = g(\boldsymbol\eta)^{\nu+N}\exp\bigg\{\boldsymbol\eta^\top\bigg(\sum_{n=1}^N\mathbf{u}(\mathbf{x}_n)+\nu\boldsymbol\chi\bigg)\bigg\}
    $$

    - これは事前分布と同じ関数形

### 2.4.3: 無情報事前分布

🧐 この章は全体的に何を言っているかあんまり分からんかった

分布がどのような形状になるべきかについて知見があまり無い場合の対処法

- 事前分布への影響がなるべく少ないようにした事前分布である、**無情報事前分布**(noninformative prior distribution)を利用する
    - 「データ自身に語らせる(letting the data speak for themselves)」という言葉で象徴されることもある
- 事後分布への影響を小さくするのに適切な事前文として、$p(\lambda)=\text{const}$ を用いたい
    - しかし、連続パラメータの場合には大きな潜在的問題点が２つ存在する
    1. $\lambda$の定義域が融解でないなら、$\lambda$上の積分が発散してしまう
        - **変則事前分布**(不完全事前分布; improper prior)と呼ばれている
        - 事後分布が**適切**(proper)であれば、用いられることもある
    2. 非線形な変数変換をした時の確率密度の変化に起因する

        $$
        p_{\eta}(\eta) = p_{\lambda}(\lambda)\Big|\frac{d\lambda}{d\eta}\Big| = p_{\lambda}(\eta^2)2\eta \propto \eta
        $$

        - $\lambda = \eta^2$のような変換をすると、変換先は$\eta$に依存する形になってしまう
- 2種類の無情報事前分布の例を紹介する
    1. 確率密度が $p(x|\mu) = f(x-\mu)$ で書ける場合を考える
        - $\mu:$  **位置パラメータ**(location parameter)
        - このような確率密度の族は**平行移動不変性**(並進不変性; translation invariance)を持つという
            - $x$を定数分移動させたとしても元の変数と同じ形状を保つ
        - このような確率密度の事前分布を考えたい
            - $A\leq\mu\leq B$ に入る確率と$A-c\leq\mu\leq B-c$ に入る確率が一致する
                - 🧐 平行移動不変性を持つ確率密度の事前分布が持つ特徴？
                - 🧐 恐らく平行移動不変性がピンと来ていない
                    - 🧐 $x$を1増やすのと$\mu$を1減らすのは同じ
                    - 🧐 $\mu$の値が重要ではなく、”幅”が大事？
            - $p(\mu-c) = p(\mu)$ → $p(\mu)=\text{const}$
                - 🧐 変則事前分布なはず…
        - 位置パラメータの例: ガウス分布の平均$\mu$
            - パラメータ$\mu$の共役事前分布もまたガウス分布 $p(\mu|\mu_0, \sigma^2_0) = \mathcal{N}(\mu|\mu_0, \sigma^2_0)$
            - 事前分布に対して$\sigma^2_0\rightarrow\infin$を考えると無情報事前分布となる
                - $\mu_N, \sigma^2_N$の式を考えるとわかる
                    - 🧐 $\lim_{\sigma_0^2\rightarrow\infin}p(\mu|\mu_0, \sigma^2_0)$ は確かに定数分布になりそう
    2. 確率密度が $p(x|\sigma) = f(x/\sigma)/\sigma$ で書ける場合を考える
        - $\sigma:$  **尺度パラメータ**(scale parameter)
        - このような確率密度の族は尺度**不変性**(scale  invariance)を持つという
            - $x$を定数倍させたとしても元の変数と同じ形状を保つ
        - このような確率密度の事前分布を考えたい
            - $A\leq\sigma\leq B$ に入る確率と$A/c\leq\sigma\leq B/c$ に入る確率が一致する
            - $p(\sigma) = f(\sigma/c)/c$ → $p(\ln \sigma)=\text{const}$
                - $0\leq\sigma\leq\infin$ での分布の積分が発散するため変則事前分布
        - 尺度パラメータの例: ガウス分布の標準偏差$\sigma$
            - 位置パラメータ$\mu$は考慮済みであることに注意
            - パラメータ$\sigma$の共役事前分布はガンマ分布 $\text{Gam}(\lambda|a_0, b_0)$
                - 但し、$\lambda=1/\sigma^2$
            - 事前分布に対して$a_0=b_0=0$を考えると無情報事前分布となる
                - $a_N, b_N$の式を考えるとわかる

## 2.5: ノンパラメトリック法

分布の形状を僅かにしか仮定しない手法について考える

- 最もシンプルな**ヒストグラム密度推定法**(histogram density estimation method)を考える
    - $x$ を幅 $\Delta_i$の別々の区間に区切り、$i$ 番目の区間に入った観測地の数 $n_i$ を考える
    - 確率密度は以下の通りに与えられる

        $$
        p_i = \frac{n_i}{N\Delta_i}
        $$

        - 区間の幅は全て均一の$\Delta$になるように選ばれることが多い
        - $\Delta$は非常に小さいと密度モデルはまばらなトゲトゲ状になり、大きすぎるとなだらかになりすぎてデータの特徴が損なわれてしまうことに注意するべきである
        - 元のデータ集合自体は破棄しても、解析が可能という特徴がある
            - データ点が逐次的に与えられても容易に適用できる
        - 但し、ほとんどの密度推定の応用問題には適さない
            - 推定した密度が区間の縁で不連続になる
            - 次元数の増加に伴う計算規模の増大
        - ヒストグラム密度度推定法から重要なことが２つわかる
            - 特定の位置の確率密度の推定のためには、局所的な近傍にある他のデータ点も考慮する必要がある
            - **平滑化パラメータ**(smoothing parameter)の値は大きすぎても小さすぎてもだめ
                - 今回は区間の幅$\Delta$が平滑化パラメータ

### 2.5.1: カーネル密度推定法

観測値の集合が得られていて、そこから未知の確率密度を推定したいとする

- $\mathbf{x}$を含む小さな領域$\mathcal{R}$を考えると、この領域に割り当てられる確率は

    $$
    P = \int_{\mathcal{R}}p(\mathbf{x})~d\mathbf{x}
    $$

    で与えられる

    - 観測値の数が$N$、あるデータ点が領域$\mathcal{R}$に属する確率を$P$とした時、領域$\mathcal{R}$内の点の総数$K$は二項分布に従う

        $$
        \text{Bin}(K|N, P) = \frac{N!}{K!(N-K)!}P^K(1-P)^{N-K}
        $$

        - 二項分布の期待値より$\mathbb{E}[K/N] = P$になるため

            $$
            K \simeq NP
            $$

        - また、確率密度$p(\mathbf{x})$が一定と満たせる程、領域$\mathcal{R}$が小さいと仮定できるのであれば

            $$
            P \simeq p(\mathbf{x})V
            $$

            が成り立つ

        - これらの式から以下のように確率密度を計算できる

            $$
            p(\mathbf{x}) = \frac{K}{NV}
            $$

            - $V$が大きいほど、確率密度が小さくなる
                - 🧐 領域内のどこに属するかは分かりずらいということ？
                - 🧐 $N=K$ならば全体の体積$V$が小さいほど特定できている
                    - 🧐 正の値全てを考慮した範囲$V_1$に全てのデータが含まれているよりも、0以上1以下の範囲$V_2$に全てのデータが含まれている方がデータ点$\mathbf{x}$がどこに属するかの情報を得れている
                    - 🧐 全体の体積が”1”であると仮定している
            - 上記の式は**相反する二つの仮定**に依存していることに注意すべきである
                1. 領域内では近似的に密度が一定と見做せるほど十分に領域$\mathcal{R}$が小さい
                2. 二項分布が鋭く尖るほどに十分に多くの$K$個の点が領域の内に存在する
            - 🧐 直感的に上記の式を見るなら、$Vp(\mathbf{x}) = K/N$ とみた方が良い
                - 🧐 $Vp(\mathbf{x}):$  (領域$\mathcal{R}$の体積)*(データ点$\mathbf{x}$が存在する確率の密度分布)
            - 上記の結果の活用方法は以下の２通り
                1. $K$を固定し、データから$V$の値を推定する → $**K$近傍法**
                2. $V$を固定し、データから$K$の値を推定する → **カーネル推定法**
- カーネル方について詳細な議論を始める
    - 確率密度を求めたいデータ点を$\mathbf{x}$、この点を中心とする小さな超立方体を領域$\mathcal{R}$とする
    - また、以下のような原点を中心とする単位立方体を表す関数(→**カーネル関数**(kernel function))を定義しておく
        - このような用途では**Parzen窓**(Parzen window)とも呼ばれる

        $$
        k(\mathbf{u}) = \left\{ \begin{align*}
        &1, ~~~~ (|u_i| \leq 1/2) \\ &0, ~~~~ (otherwise)
        \end{align*} \right.
        $$

    - 上記の関数を用いると、一辺が$h$の立方体内部の総点数は以下の通り

        $$
        K = \sum_{n=1}^N k\bigg(\frac{\mathbf{x}-\mathbf{x}_n}{h}\bigg)

        $$

        - これより、確率密度の近似が可能
- カーネル密度推定法でも大きな問題点が１つ存在する
    - (ヒストグラム法と同じ)立方体の縁で人為的な不連続が生じてしまう
        - より滑らかなカーネル関数を選べば、より滑らかな密度モデルが得られる
        - 例: ガウスカーネル
- 訓練段階では全く計算をしなくて済むという大きな利点がある一方、密度の評価にかかる計算コストがデータ集合の大きさに比例するという大きな欠点が存在する

### 2.5.2: 最近傍法

カーネル密度推定法と似て非なる手法を考える

- 以下は局所密度推定の一般的な結果の再掲である

    $$
    p(\mathbf{x}) = \frac{K}{NV}
    $$

    - $V$を固定し、データから$K$の値を推定するはカーネル密度推定法であったが、逆の$V$を固定し、データから$V$の値を推定する$**K$近傍法**について考える
    - $K$の値は平滑化の度合いを決めている
- $K$近傍法はクラス分類問題に拡張できる
    - クラス$C_k$の中に$N_k$個の点があるデータ集合を考える
    - 新たなデータ点$\mathbf{x}$に対して、近くのデータ点$K$個を含むような体積$V$の球を考える
        - この球が各クラス$\mathcal{C}_k$のデータを$K_k$ずつ含んでいたとすると、各クラスの密度の推定値は以下のように得られる

            $$
            p(\mathbf{x}|\mathcal{C}_k) = \frac{K_k}{N_kV}
            $$

            - 🧐 クラス$\mathcal{C}_k$と上記の説明で利用されていた領域$R$は同じようなもの
        - 事前分布は、データ点$\mathbf{x}$が増える前の各クラスのデータ点の存在割合

            $$
            p(\mathcal{C}_k) = \frac{N_k}{\sum_kN_k}
            $$

        - これらに対して事後分布は以下の通り

            $$
            p(\mathcal{C}_k|\mathbf{x}) = \frac{K_k}{K}
            $$

- $K$近傍法もカーネル密度推定法もデータ集合全体を保持しなくてはならない
    - ノンパラメトリック方の非常に強い制限

     ☞  柔軟性があり、訓練集合の大きさとは独立にモデルの複雑度を調整できるような密度モデルを見つける必要がある
