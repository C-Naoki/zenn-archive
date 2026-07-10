---
title: "論文執筆で役立つ LaTeX の小ワザ集"
emoji: "📐"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["latex", "tex", "論文", "数式"]
published: false
---

## はじめに

LaTeX で数式や論文を整えていると、「少しだけ位置を動かしたい」「長い添字を収めたい」「参照の書き方を統一したい」といった場面によく遭遇します。毎回調べ直さなくて済むように、私が文書作成でよく使う小ワザを、コピーして試せる形でまとめます。

この記事のコード例は、基本的に `.tex` ファイルで使うことを想定しています。Zenn の数式表示には KaTeX が使われており、LaTeX のすべてのコマンドや環境をサポートしているわけではありません。そのため、コード例と Zenn 上での表示例を分けて掲載します。

使用する機能に応じて、プリアンブルに次のパッケージを追加してください。すべてを一度に読み込む必要はありません。

```latex
\usepackage{amsmath,amssymb} % 数式環境と記号
\usepackage{mathtools}      % amsmath の拡張
\usepackage{bm}             % ギリシャ文字などの太字
\usepackage{booktabs}       % 表の罫線
\usepackage{tabularx}       % 幅を指定した表
\usepackage{graphicx}       % 画像の読み込み
```

## 数式の見た目を整える

### `\raisebox` で添字の位置を調整する

上付き文字と下付き文字を同時に付けると、TeX が両者の間隔を確保するため、下付き文字だけの場合よりも低く見えることがあります。そのようなときは、`\raisebox` で文字の位置を上下に微調整できます。

```latex
\[
\mathcal{H}_{\raisebox{0.2ex}{$\scriptstyle u$}}^{\circ}
\subseteq \mathcal{H}_u
\]
```

表示結果は次のとおりです。

$$
\mathcal{H}_{\raisebox{0.2ex}{\(\scriptstyle u\)}}^{\circ}
\subseteq \mathcal{H}_u
$$

第1引数の値が正なら上、負なら下へ移動します。`ex` は現在のフォントの x-height を基準にする相対単位なので、固定値の `pt` よりもフォントの変更に追従しやすくなります。

`\raisebox` の中はテキストモードになるため、添字の `u` を `$...$` で数式モードに戻しています。さらに `\scriptstyle` を付けることで、通常の下付き文字と同じ大きさを保っています。位置を動かしすぎると行間や他の記号とのバランスが崩れるため、まずは `0.1ex` から `0.3ex` 程度の小さな値で調整するのがおすすめです。

### `\phantom` で見えない余白を確保する

`\phantom{...}` は、引数の内容を表示せず、その幅・高さ・深さだけを確保します。符号の有無が異なる数値を揃えるときなどに便利です。

```latex
\[
\begin{aligned}
a &= \phantom{-}1.25, \\
b &= -1.25.
\end{aligned}
\]
```

$$
\begin{aligned}
a &= \phantom{-}1.25, \\
b &= -1.25.
\end{aligned}
$$

用途に応じて、幅だけを確保する `\hphantom` と、高さ・深さだけを確保する `\vphantom` も使えます。ただし、見えない要素が増えるほどソースからレイアウトを把握しにくくなるため、数式の構造自体を揃えられない場合の局所的な調整に向いています。

### `\mathclap` で長い添字の幅を詰める

総和や最大値の条件が長いと、その条件に合わせて数式全体の横幅が広がります。`mathtools` の `\mathclap` を使うと、内容を表示したまま、その部分の横幅を 0 として扱えます。

```latex
\[
\sum_{\mathclap{1 \le i < j \le n}} a_{ij}
\]
```

$$
\sum_{\mathclap{1 \le i < j \le n}} a_{ij}
$$

周囲の要素との間隔を自動では確保しないため、隣の数式と重なる場合があります。条件を2行に分けたほうが読みやすい場合は、次に紹介する `\substack` を使います。

### `\substack` で添字の条件を複数行にする

総和や最適化の条件を複数行に分けるときは、`\substack` が便利です。

```latex
\[
\sum_{\substack{1 \le i \le n \\
                  1 \le j \le m}}
a_{ij}
\]
```

$$
\sum_{\substack{1 \le i \le n \\
                  1 \le j \le m}}
a_{ij}
$$

`\mathclap` は横幅を小さくしたい場合、`\substack` は条件そのものを読みやすくしたい場合に使い分けるとよいです。

### 括弧の大きさを内容に合わせる

分数や行列を括弧で囲むときは、`\left` と `\right` を使うと、内容に合わせて括弧の大きさが変わります。条件付き確率の縦線には `\middle` も利用できます。

```latex
\[
\Pr\!\left(
  X \ge x
  \,\middle|\,
  Y = y
\right)
\]
```

$$
\Pr\!\left(
  X \ge x
  \,\middle|\,
  Y = y
\right)
$$

片側だけに区切り記号を置きたい場合は、表示しない側に `.` を指定します。たとえば、関数を特定の点で評価する記号は次のように書けます。

```latex
\[
\left.
\frac{\mathrm{d}}{\mathrm{d}x}f(x)
\right|_{x=0}
\]
```

$$
\left.
\frac{\mathrm{d}}{\mathrm{d}x}f(x)
\right|_{x=0}
$$

`\left` と `\right` に任せると括弧が大きくなりすぎる場合は、`\bigl(` と `\bigr)`、`\Bigl[` と `\Bigr]` のように大きさを明示できます。開き括弧には `l`、閉じ括弧には `r` を付けると、前後の空きも適切になります。

### 数式中の空きを明示する

積分の関数と微分記号の間など、LaTeX の既定値では少し詰まって見える箇所には、小さな空きを追加できます。

```latex
\[
\int_a^b f(x)\,\mathrm{d}x
\qquad
p(x\mid y)
\]
```

$$
\int_a^b f(x)\,\mathrm{d}x
\qquad
p(x\mid y)
$$

よく使う空きは、小さい順に `\,`、`\:`、`\;`、`\quad`、`\qquad` です。反対に、`\!` は小さな負の空きを入れます。条件を表す縦線には、手作業で `|` の前後を空けるよりも、関係記号として適切な余白が付く `\mid` を使うほうが安定します。

空きの追加は最小限にとどめ、まず意味に合った記号やコマンドを選ぶことが大切です。

## 数式の構造を読みやすくする

### `align` で等号の位置を揃える

複数行にわたる式変形では、`align` 環境の `&` を揃えたい位置に置きます。

```latex
\begin{align}
L(\theta)
  &= \sum_{i=1}^{n}\bigl(y_i - f_\theta(x_i)\bigr)^2 \notag \\
  &\quad + \lambda \lVert\theta\rVert_2^2
  \label{eq:objective}
\end{align}
```

$$
\begin{aligned}
L(\theta)
  &= \sum_{i=1}^{n}\bigl(y_i - f_\theta(x_i)\bigr)^2 \\
  &\quad + \lambda \lVert\theta\rVert_2^2.
\end{aligned}
$$

`align` はそれ自体が別行立て数式の環境なので、外側を `\[...\]` や `$$...$$` で囲みません。各行に番号が付き、`\notag` を置いた行だけ番号が省略されます。すべての番号が不要なら `align*` を使います。

式全体に番号を1つだけ付けたい場合は、`equation` の中に `aligned` を置きます。

```latex
\begin{equation}
\begin{aligned}
a &= b + c, \\
  &= d + e.
\end{aligned}
\label{eq:single-number}
\end{equation}
```

### `cases` で場合分けを書く

場合分けには `cases` 環境を使います。数式中の説明文は、文字を無理に数式として並べず、`\text{...}` で書きます。

```latex
\[
f(x) =
\begin{cases}
x^2, & \text{if } x \ge 0, \\
-x,  & \text{if } x < 0.
\end{cases}
\]
```

$$
f(x) =
\begin{cases}
x^2, & \text{if } x \ge 0, \\
-x,  & \text{if } x < 0.
\end{cases}
$$

各行の `&` より前が値、後ろが条件です。条件に日本語を直接書く場合は、文書全体で日本語を扱えるエンジンや設定が必要です。

### 記号の上下に説明を付ける

式の一部を説明するには `\underbrace` や `\overbrace`、関係記号に注釈を付けるには `\overset` や `\underset` を使えます。

```latex
\[
\underbrace{\bigl(\mathbb{E}[\hat{\theta}] - \theta\bigr)^2}
            _{\text{squared bias}}
+
\underbrace{\operatorname{Var}(\hat{\theta})}
            _{\text{variance}}
\]
```

$$
\underbrace{\bigl(\mathbb{E}[\hat{\theta}] - \theta\bigr)^2}
            _{\text{squared bias}}
+
\underbrace{\operatorname{Var}(\hat{\theta})}
            _{\text{variance}}
$$

確率収束や写像の説明には、`amsmath` の `\xrightarrow` も便利です。波括弧の引数が矢印の上、角括弧の引数が下に表示されます。

```latex
\[
X_n \xrightarrow[n \to \infty]{\mathrm{d}} X
\]
```

$$
X_n \xrightarrow[n \to \infty]{\mathrm{d}} X
$$

注釈が長くなると数式の流れを追いにくくなるため、式の理解に直接必要な説明だけを残します。

### ギリシャ文字や記号を太字にする

`\mathbf` は英数字には使えますが、通常はギリシャ文字を太字にできません。`bm` パッケージの `\bm` を使うと、ギリシャ文字や演算記号も含めて太字にできます。

```latex
\[
\mathbf{x},\qquad
\bm{\theta},\qquad
\bm{\Sigma}^{-1}\mathbf{x}
\]
```

$$
\mathbf{x},\qquad
\bm{\theta},\qquad
\bm{\Sigma}^{-1}\mathbf{x}
$$

ベクトルを `\mathbf`、ベクトルと行列をどちらも `\bm` で表すなど、記法の方針を文書内で統一しておくと読み手が迷いません。

## 記法を再利用しやすくする

### 演算子を `\DeclareMathOperator` で定義する

`arg min` のような演算子をそのまま文字として書くと、斜体や文字間隔が不自然になります。プリアンブルで演算子として定義すると、`\sum` などと同じように添字を扱えます。

```latex
\DeclareMathOperator*{\argmin}{arg\,min}
\DeclareMathOperator*{\argmax}{arg\,max}
```

本文では次のように使います。

```latex
\[
\hat{\theta}
= \argmin_{\theta \in \Theta} L(\theta)
\]
```

$$
\hat{\theta}
= \operatorname*{arg\,min}_{\theta \in \Theta} L(\theta)
$$

アスタリスク付きの `\DeclareMathOperator*` は、別行立て数式で条件を演算子の真下に配置します。1回しか使わない演算子なら、表示例のように `\operatorname` または `\operatorname*` を直接使う方法もあります。

### `\DeclarePairedDelimiter` で括弧付き記号を定義する

ノルムや絶対値を何度も使う場合は、`mathtools` の `\DeclarePairedDelimiter` で意味のあるコマンドを作っておくと、表記を統一できます。

```latex
\DeclarePairedDelimiter{\abs}{\lvert}{\rvert}
\DeclarePairedDelimiter{\norm}{\lVert}{\rVert}
```

```latex
\[
\abs{x},
\qquad
\norm{\mathbf{x}}_2,
\qquad
\norm*{\frac{\mathbf{x}}{\alpha}}
\]
```

アスタリスクなしでは通常サイズ、アスタリスク付きでは内容に応じたサイズになります。常に自動調整すると行ごとに括弧の大きさがばらつくこともあるため、必要な箇所だけ `\norm*{...}` を使うと見た目を制御しやすくなります。

### よく使う集合や記号をマクロにする

同じ記号を何度も書くなら、プリアンブルで定義しておくと、記法を変更するときも1箇所の修正で済みます。

```latex
\newcommand{\R}{\mathbb{R}}
\newcommand{\E}{\mathbb{E}}
\newcommand{\vect}[1]{\bm{#1}}
```

```latex
\[
\vect{x} \in \R^d,
\qquad
\E\bigl[\vect{x}\bigr] = \vect{\mu}
\]
```

短さだけを目的に `\newcommand{\x}{...}` のような名前を増やすと、かえって意味を追いにくくなります。`\vect` や `\norm` のように、役割が分かる名前を付けるのがポイントです。また、読み込んだパッケージがすでに同名のコマンドを定義していないか確認します。

## 式・図・表を参照しやすくする

### `\cref` で参照の種類も自動化する

式番号や図番号を本文に直接書くと、要素を追加したときに番号がずれます。参照先に `\label` を置き、`cleveref` パッケージの `\cref` から参照すると、番号だけでなく「式」「図」「表」といった参照先の種類も自動で補えます。

`hyperref` と一緒に使う場合は、原則として `cleveref` を後に読み込みます。日本語で参照名を表示したい場合は、プリアンブルで名前を設定します。

```latex
\usepackage{hyperref}
\usepackage[nameinlink,noabbrev]{cleveref}

\crefname{equation}{式}{式}
\crefname{figure}{図}{図}
\crefname{table}{表}{表}
```

参照先と本文は次のように記述します。

```latex
\begin{equation}
p(\theta \mid x)
= \frac{p(x \mid \theta)p(\theta)}{p(x)}
\label{eq:bayes}
\end{equation}

\cref{eq:bayes} より、事後分布を計算できる。
```

この例では、`\cref{eq:bayes}` が「式 (1)」のような参照に置き換わります。`nameinlink` オプションを付けているため、番号だけでなく参照名も含めてリンクになります。

複数の要素や連続した範囲も、専用のコマンドでまとめて参照できます。

```latex
\cref{eq:loss,eq:gradient}       % 複数の参照
\crefrange{fig:first}{fig:last}  % 連続した範囲
```

英文で文頭から参照を始める場合は、先頭を大文字にする `\Cref` も使えます。ラベルには `eq:`、`fig:`、`tab:`、`sec:` のような接頭辞を付けておくと、ソース上で参照先の種類を判別しやすくなります。ただし、`\cref` が表示する参照名は接頭辞ではなく、ラベルが紐付いているカウンターから決まります。

図や表では、`\label` を `\caption` の直後に置きます。これにより、直前に更新された図番号や表番号を正しく取得できます。

## レイアウトの問題を見つける

### はみ出した行を可視化する

長い数式や URL が行幅を超えると、コンパイルログに `Overfull \hbox` と表示されます。次の設定を一時的に追加すると、はみ出した行の右端に黒い目印が付きます。

```latex
\overfullrule=5pt
```

ページ全体の余白や本文領域を確認したい場合は、`showframe` パッケージも利用できます。

```latex
\usepackage{showframe}
```

これらは確認用の設定なので、問題を修正したら最終版から取り除きます。

### `\sloppy` で改行の許容範囲を広げる

通常の設定では行をうまく分割できず、`Overfull \hbox` が残る場合は、`\sloppy` で改行条件を緩められます。`\sloppy` は引数を取らない宣言で、単語間の空きを通常より柔軟に伸縮させることで、行が本文領域からはみ出しにくくします。

文書の途中から適用する場合は、`\fussy` を置くまで効果が続きます。

```latex
\sloppy
% ここから改行条件を緩める

This paragraph contains text that is difficult to fit within the line width.

\fussy
% ここから通常の改行条件に戻す
```

段落の改行計算は段落の終端で行われるため、例のように `\fussy` の前へ空行を入れ、`\sloppy` を適用したい段落を終了させます。

影響を特定の段落だけに限定したい場合は、`sloppypar` 環境を使うと適用範囲が明確になります。

```latex
\begin{sloppypar}
This paragraph contains text that is difficult to fit within the line width.
\end{sloppypar}
```

`\sloppy` を使うと、単語間の空きが不自然に広がり、行によって密度がばらつくことがあります。また、途中で改行できない長い文字列を分割できるようにするコマンドではありません。長い式を `align` や `multline` で分割する、URL を `url` または `hyperref` パッケージの `\url{...}` で記述する、表の構成を見直す、といった原因ごとの修正を先に行い、それでも解消できない段落へ局所的に使うのがおすすめです。

## まとめ

LaTeX の見た目は、少数のコマンドを知っているだけでも整えやすくなります。

- 局所的な位置や余白は `\raisebox`、`\phantom`、`\mathclap` で調整する
- 長い式は `align`、`cases`、`\substack` で構造を見せる
- 記法は `\DeclareMathOperator` や `\DeclarePairedDelimiter` で統一する
- 式・図・表の番号は `\label` と参照コマンドに任せる
- 表や図は文字を小さくする前に、列幅や余白、配置を見直す
- `\sloppy` は原因を修正したあともはみ出す段落だけに適用する
- 最後はコンパイルログと PDF の両方を確認する

細かな調整は便利ですが、読みやすいソースと一貫した記法を保つことが最優先です。まず LaTeX が用意している構造的なコマンドを使い、それでも整わない箇所だけを局所的に調整すると、後から修正しやすい文書になります。
