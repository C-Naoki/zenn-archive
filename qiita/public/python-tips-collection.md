---
title: PythonのTips集
tags:
  - Python
  - GitHub
  - tech
  - 静的解析
private: false
updated_at: '2024-10-05T23:11:14+09:00'
id: 015d40f2fb639cb43034
organization_url_name: null
slide: false
ignorePublish: false
---
## はじめに
Pythonを書く際に、よく使うテクニックやツールについてまとめました。特に、自分が今まで困ってきた内容を中心に取り上げており、自分のためのメモとしても活用しています。

## Seaborn
SeabornはPythonで利用可能なデータ可視化ライブラリです。Matplotlibのラッパーライブラリであるため、Matplotlibの機能を利用することができます。Seabornは、データの可視化を行う際に、Matplotlibよりも簡単に利用できるため、データの可視化を行う際には、Seabornをよく利用しています。

### データの読み込み
seabornは、`sns.load_dataset`関数を用いて、データを読み込むことができます。以下のコードを実行することで、`tips`データを読み込むことができます。
```python
import seaborn as sns

df = sns.load_dataset("tips")
```
<details><summary>データの詳細</summary>

|     |   total_bill |   tip | sex    | smoker   | day   | time   |   size |
|----:|-------------:|------:|:-------|:---------|:------|:-------|-------:|
|   0 |        16.99 |  1.01 | Female | No       | Sun   | Dinner |      2 |
|   1 |        10.34 |  1.66 | Male   | No       | Sun   | Dinner |      3 |
|   2 |        21.01 |  3.5  | Male   | No       | Sun   | Dinner |      3 |
|   3 |        23.68 |  3.31 | Male   | No       | Sun   | Dinner |      2 |
|   4 |        24.59 |  3.61 | Female | No       | Sun   | Dinner |      4 |
|   5 |        25.29 |  4.71 | Male   | No       | Sun   | Dinner |      4 |
|   6 |         8.77 |  2    | Male   | No       | Sun   | Dinner |      2 |
|   7 |        26.88 |  3.12 | Male   | No       | Sun   | Dinner |      4 |
|   8 |        15.04 |  1.96 | Male   | No       | Sun   | Dinner |      2 |
|   9 |        14.78 |  3.23 | Male   | No       | Sun   | Dinner |      2 |
|  10 |        10.27 |  1.71 | Male   | No       | Sun   | Dinner |      2 |
|  11 |        35.26 |  5    | Female | No       | Sun   | Dinner |      4 |
|  12 |        15.42 |  1.57 | Male   | No       | Sun   | Dinner |      2 |
|  13 |        18.43 |  3    | Male   | No       | Sun   | Dinner |      4 |
|  14 |        14.83 |  3.02 | Female | No       | Sun   | Dinner |      2 |
|  15 |        21.58 |  3.92 | Male   | No       | Sun   | Dinner |      2 |
|  16 |        10.33 |  1.67 | Female | No       | Sun   | Dinner |      3 |
|  17 |        16.29 |  3.71 | Male   | No       | Sun   | Dinner |      3 |
|  18 |        16.97 |  3.5  | Female | No       | Sun   | Dinner |      3 |
|  19 |        20.65 |  3.35 | Male   | No       | Sat   | Dinner |      3 |
|  20 |        17.92 |  4.08 | Male   | No       | Sat   | Dinner |      2 |
|  21 |        20.29 |  2.75 | Female | No       | Sat   | Dinner |      2 |
|  22 |        15.77 |  2.23 | Female | No       | Sat   | Dinner |      2 |
|  23 |        39.42 |  7.58 | Male   | No       | Sat   | Dinner |      4 |
|  24 |        19.82 |  3.18 | Male   | No       | Sat   | Dinner |      2 |
|  25 |        17.81 |  2.34 | Male   | No       | Sat   | Dinner |      4 |
|  26 |        13.37 |  2    | Male   | No       | Sat   | Dinner |      2 |
|  27 |        12.69 |  2    | Male   | No       | Sat   | Dinner |      2 |
|  28 |        21.7  |  4.3  | Male   | No       | Sat   | Dinner |      2 |
|  29 |        19.65 |  3    | Female | No       | Sat   | Dinner |      2 |
|  30 |         9.55 |  1.45 | Male   | No       | Sat   | Dinner |      2 |
|  31 |        18.35 |  2.5  | Male   | No       | Sat   | Dinner |      4 |
|  32 |        15.06 |  3    | Female | No       | Sat   | Dinner |      2 |
|  33 |        20.69 |  2.45 | Female | No       | Sat   | Dinner |      4 |
|  34 |        17.78 |  3.27 | Male   | No       | Sat   | Dinner |      2 |
|  35 |        24.06 |  3.6  | Male   | No       | Sat   | Dinner |      3 |
|  36 |        16.31 |  2    | Male   | No       | Sat   | Dinner |      3 |
|  37 |        16.93 |  3.07 | Female | No       | Sat   | Dinner |      3 |
|  38 |        18.69 |  2.31 | Male   | No       | Sat   | Dinner |      3 |
|  39 |        31.27 |  5    | Male   | No       | Sat   | Dinner |      3 |
|  40 |        16.04 |  2.24 | Male   | No       | Sat   | Dinner |      3 |
|  41 |        17.46 |  2.54 | Male   | No       | Sun   | Dinner |      2 |
|  42 |        13.94 |  3.06 | Male   | No       | Sun   | Dinner |      2 |
|  43 |         9.68 |  1.32 | Male   | No       | Sun   | Dinner |      2 |
|  44 |        30.4  |  5.6  | Male   | No       | Sun   | Dinner |      4 |
|  45 |        18.29 |  3    | Male   | No       | Sun   | Dinner |      2 |
|  46 |        22.23 |  5    | Male   | No       | Sun   | Dinner |      2 |
|  47 |        32.4  |  6    | Male   | No       | Sun   | Dinner |      4 |
|  48 |        28.55 |  2.05 | Male   | No       | Sun   | Dinner |      3 |
|  49 |        18.04 |  3    | Male   | No       | Sun   | Dinner |      2 |
|  50 |        12.54 |  2.5  | Male   | No       | Sun   | Dinner |      2 |
|  51 |        10.29 |  2.6  | Female | No       | Sun   | Dinner |      2 |
|  52 |        34.81 |  5.2  | Female | No       | Sun   | Dinner |      4 |
|  53 |         9.94 |  1.56 | Male   | No       | Sun   | Dinner |      2 |
|  54 |        25.56 |  4.34 | Male   | No       | Sun   | Dinner |      4 |
|  55 |        19.49 |  3.51 | Male   | No       | Sun   | Dinner |      2 |
|  56 |        38.01 |  3    | Male   | Yes      | Sat   | Dinner |      4 |
|  57 |        26.41 |  1.5  | Female | No       | Sat   | Dinner |      2 |
|  58 |        11.24 |  1.76 | Male   | Yes      | Sat   | Dinner |      2 |
|  59 |        48.27 |  6.73 | Male   | No       | Sat   | Dinner |      4 |
|  60 |        20.29 |  3.21 | Male   | Yes      | Sat   | Dinner |      2 |
|  61 |        13.81 |  2    | Male   | Yes      | Sat   | Dinner |      2 |
|  62 |        11.02 |  1.98 | Male   | Yes      | Sat   | Dinner |      2 |
|  63 |        18.29 |  3.76 | Male   | Yes      | Sat   | Dinner |      4 |
|  64 |        17.59 |  2.64 | Male   | No       | Sat   | Dinner |      3 |
|  65 |        20.08 |  3.15 | Male   | No       | Sat   | Dinner |      3 |
|  66 |        16.45 |  2.47 | Female | No       | Sat   | Dinner |      2 |
|  67 |         3.07 |  1    | Female | Yes      | Sat   | Dinner |      1 |
|  68 |        20.23 |  2.01 | Male   | No       | Sat   | Dinner |      2 |
|  69 |        15.01 |  2.09 | Male   | Yes      | Sat   | Dinner |      2 |
|  70 |        12.02 |  1.97 | Male   | No       | Sat   | Dinner |      2 |
|  71 |        17.07 |  3    | Female | No       | Sat   | Dinner |      3 |
|  72 |        26.86 |  3.14 | Female | Yes      | Sat   | Dinner |      2 |
|  73 |        25.28 |  5    | Female | Yes      | Sat   | Dinner |      2 |
|  74 |        14.73 |  2.2  | Female | No       | Sat   | Dinner |      2 |
|  75 |        10.51 |  1.25 | Male   | No       | Sat   | Dinner |      2 |
|  76 |        17.92 |  3.08 | Male   | Yes      | Sat   | Dinner |      2 |
|  77 |        27.2  |  4    | Male   | No       | Thur  | Lunch  |      4 |
|  78 |        22.76 |  3    | Male   | No       | Thur  | Lunch  |      2 |
|  79 |        17.29 |  2.71 | Male   | No       | Thur  | Lunch  |      2 |
|  80 |        19.44 |  3    | Male   | Yes      | Thur  | Lunch  |      2 |
|  81 |        16.66 |  3.4  | Male   | No       | Thur  | Lunch  |      2 |
|  82 |        10.07 |  1.83 | Female | No       | Thur  | Lunch  |      1 |
|  83 |        32.68 |  5    | Male   | Yes      | Thur  | Lunch  |      2 |
|  84 |        15.98 |  2.03 | Male   | No       | Thur  | Lunch  |      2 |
|  85 |        34.83 |  5.17 | Female | No       | Thur  | Lunch  |      4 |
|  86 |        13.03 |  2    | Male   | No       | Thur  | Lunch  |      2 |
|  87 |        18.28 |  4    | Male   | No       | Thur  | Lunch  |      2 |
|  88 |        24.71 |  5.85 | Male   | No       | Thur  | Lunch  |      2 |
|  89 |        21.16 |  3    | Male   | No       | Thur  | Lunch  |      2 |
|  90 |        28.97 |  3    | Male   | Yes      | Fri   | Dinner |      2 |
|  91 |        22.49 |  3.5  | Male   | No       | Fri   | Dinner |      2 |
|  92 |         5.75 |  1    | Female | Yes      | Fri   | Dinner |      2 |
|  93 |        16.32 |  4.3  | Female | Yes      | Fri   | Dinner |      2 |
|  94 |        22.75 |  3.25 | Female | No       | Fri   | Dinner |      2 |
|  95 |        40.17 |  4.73 | Male   | Yes      | Fri   | Dinner |      4 |
|  96 |        27.28 |  4    | Male   | Yes      | Fri   | Dinner |      2 |
|  97 |        12.03 |  1.5  | Male   | Yes      | Fri   | Dinner |      2 |
|  98 |        21.01 |  3    | Male   | Yes      | Fri   | Dinner |      2 |
|  99 |        12.46 |  1.5  | Male   | No       | Fri   | Dinner |      2 |
| 100 |        11.35 |  2.5  | Female | Yes      | Fri   | Dinner |      2 |
| 101 |        15.38 |  3    | Female | Yes      | Fri   | Dinner |      2 |
| 102 |        44.3  |  2.5  | Female | Yes      | Sat   | Dinner |      3 |
| 103 |        22.42 |  3.48 | Female | Yes      | Sat   | Dinner |      2 |
| 104 |        20.92 |  4.08 | Female | No       | Sat   | Dinner |      2 |
| 105 |        15.36 |  1.64 | Male   | Yes      | Sat   | Dinner |      2 |
| 106 |        20.49 |  4.06 | Male   | Yes      | Sat   | Dinner |      2 |
| 107 |        25.21 |  4.29 | Male   | Yes      | Sat   | Dinner |      2 |
| 108 |        18.24 |  3.76 | Male   | No       | Sat   | Dinner |      2 |
| 109 |        14.31 |  4    | Female | Yes      | Sat   | Dinner |      2 |
| 110 |        14    |  3    | Male   | No       | Sat   | Dinner |      2 |
| 111 |         7.25 |  1    | Female | No       | Sat   | Dinner |      1 |
| 112 |        38.07 |  4    | Male   | No       | Sun   | Dinner |      3 |
| 113 |        23.95 |  2.55 | Male   | No       | Sun   | Dinner |      2 |
| 114 |        25.71 |  4    | Female | No       | Sun   | Dinner |      3 |
| 115 |        17.31 |  3.5  | Female | No       | Sun   | Dinner |      2 |
| 116 |        29.93 |  5.07 | Male   | No       | Sun   | Dinner |      4 |
| 117 |        10.65 |  1.5  | Female | No       | Thur  | Lunch  |      2 |
| 118 |        12.43 |  1.8  | Female | No       | Thur  | Lunch  |      2 |
| 119 |        24.08 |  2.92 | Female | No       | Thur  | Lunch  |      4 |
| 120 |        11.69 |  2.31 | Male   | No       | Thur  | Lunch  |      2 |
| 121 |        13.42 |  1.68 | Female | No       | Thur  | Lunch  |      2 |
| 122 |        14.26 |  2.5  | Male   | No       | Thur  | Lunch  |      2 |
| 123 |        15.95 |  2    | Male   | No       | Thur  | Lunch  |      2 |
| 124 |        12.48 |  2.52 | Female | No       | Thur  | Lunch  |      2 |
| 125 |        29.8  |  4.2  | Female | No       | Thur  | Lunch  |      6 |
| 126 |         8.52 |  1.48 | Male   | No       | Thur  | Lunch  |      2 |
| 127 |        14.52 |  2    | Female | No       | Thur  | Lunch  |      2 |
| 128 |        11.38 |  2    | Female | No       | Thur  | Lunch  |      2 |
| 129 |        22.82 |  2.18 | Male   | No       | Thur  | Lunch  |      3 |
| 130 |        19.08 |  1.5  | Male   | No       | Thur  | Lunch  |      2 |
| 131 |        20.27 |  2.83 | Female | No       | Thur  | Lunch  |      2 |
| 132 |        11.17 |  1.5  | Female | No       | Thur  | Lunch  |      2 |
| 133 |        12.26 |  2    | Female | No       | Thur  | Lunch  |      2 |
| 134 |        18.26 |  3.25 | Female | No       | Thur  | Lunch  |      2 |
| 135 |         8.51 |  1.25 | Female | No       | Thur  | Lunch  |      2 |
| 136 |        10.33 |  2    | Female | No       | Thur  | Lunch  |      2 |
| 137 |        14.15 |  2    | Female | No       | Thur  | Lunch  |      2 |
| 138 |        16    |  2    | Male   | Yes      | Thur  | Lunch  |      2 |
| 139 |        13.16 |  2.75 | Female | No       | Thur  | Lunch  |      2 |
| 140 |        17.47 |  3.5  | Female | No       | Thur  | Lunch  |      2 |
| 141 |        34.3  |  6.7  | Male   | No       | Thur  | Lunch  |      6 |
| 142 |        41.19 |  5    | Male   | No       | Thur  | Lunch  |      5 |
| 143 |        27.05 |  5    | Female | No       | Thur  | Lunch  |      6 |
| 144 |        16.43 |  2.3  | Female | No       | Thur  | Lunch  |      2 |
| 145 |         8.35 |  1.5  | Female | No       | Thur  | Lunch  |      2 |
| 146 |        18.64 |  1.36 | Female | No       | Thur  | Lunch  |      3 |
| 147 |        11.87 |  1.63 | Female | No       | Thur  | Lunch  |      2 |
| 148 |         9.78 |  1.73 | Male   | No       | Thur  | Lunch  |      2 |
| 149 |         7.51 |  2    | Male   | No       | Thur  | Lunch  |      2 |
| 150 |        14.07 |  2.5  | Male   | No       | Sun   | Dinner |      2 |
| 151 |        13.13 |  2    | Male   | No       | Sun   | Dinner |      2 |
| 152 |        17.26 |  2.74 | Male   | No       | Sun   | Dinner |      3 |
| 153 |        24.55 |  2    | Male   | No       | Sun   | Dinner |      4 |
| 154 |        19.77 |  2    | Male   | No       | Sun   | Dinner |      4 |
| 155 |        29.85 |  5.14 | Female | No       | Sun   | Dinner |      5 |
| 156 |        48.17 |  5    | Male   | No       | Sun   | Dinner |      6 |
| 157 |        25    |  3.75 | Female | No       | Sun   | Dinner |      4 |
| 158 |        13.39 |  2.61 | Female | No       | Sun   | Dinner |      2 |
| 159 |        16.49 |  2    | Male   | No       | Sun   | Dinner |      4 |
| 160 |        21.5  |  3.5  | Male   | No       | Sun   | Dinner |      4 |
| 161 |        12.66 |  2.5  | Male   | No       | Sun   | Dinner |      2 |
| 162 |        16.21 |  2    | Female | No       | Sun   | Dinner |      3 |
| 163 |        13.81 |  2    | Male   | No       | Sun   | Dinner |      2 |
| 164 |        17.51 |  3    | Female | Yes      | Sun   | Dinner |      2 |
| 165 |        24.52 |  3.48 | Male   | No       | Sun   | Dinner |      3 |
| 166 |        20.76 |  2.24 | Male   | No       | Sun   | Dinner |      2 |
| 167 |        31.71 |  4.5  | Male   | No       | Sun   | Dinner |      4 |
| 168 |        10.59 |  1.61 | Female | Yes      | Sat   | Dinner |      2 |
| 169 |        10.63 |  2    | Female | Yes      | Sat   | Dinner |      2 |
| 170 |        50.81 | 10    | Male   | Yes      | Sat   | Dinner |      3 |
| 171 |        15.81 |  3.16 | Male   | Yes      | Sat   | Dinner |      2 |
| 172 |         7.25 |  5.15 | Male   | Yes      | Sun   | Dinner |      2 |
| 173 |        31.85 |  3.18 | Male   | Yes      | Sun   | Dinner |      2 |
| 174 |        16.82 |  4    | Male   | Yes      | Sun   | Dinner |      2 |
| 175 |        32.9  |  3.11 | Male   | Yes      | Sun   | Dinner |      2 |
| 176 |        17.89 |  2    | Male   | Yes      | Sun   | Dinner |      2 |
| 177 |        14.48 |  2    | Male   | Yes      | Sun   | Dinner |      2 |
| 178 |         9.6  |  4    | Female | Yes      | Sun   | Dinner |      2 |
| 179 |        34.63 |  3.55 | Male   | Yes      | Sun   | Dinner |      2 |
| 180 |        34.65 |  3.68 | Male   | Yes      | Sun   | Dinner |      4 |
| 181 |        23.33 |  5.65 | Male   | Yes      | Sun   | Dinner |      2 |
| 182 |        45.35 |  3.5  | Male   | Yes      | Sun   | Dinner |      3 |
| 183 |        23.17 |  6.5  | Male   | Yes      | Sun   | Dinner |      4 |
| 184 |        40.55 |  3    | Male   | Yes      | Sun   | Dinner |      2 |
| 185 |        20.69 |  5    | Male   | No       | Sun   | Dinner |      5 |
| 186 |        20.9  |  3.5  | Female | Yes      | Sun   | Dinner |      3 |
| 187 |        30.46 |  2    | Male   | Yes      | Sun   | Dinner |      5 |
| 188 |        18.15 |  3.5  | Female | Yes      | Sun   | Dinner |      3 |
| 189 |        23.1  |  4    | Male   | Yes      | Sun   | Dinner |      3 |
| 190 |        15.69 |  1.5  | Male   | Yes      | Sun   | Dinner |      2 |
| 191 |        19.81 |  4.19 | Female | Yes      | Thur  | Lunch  |      2 |
| 192 |        28.44 |  2.56 | Male   | Yes      | Thur  | Lunch  |      2 |
| 193 |        15.48 |  2.02 | Male   | Yes      | Thur  | Lunch  |      2 |
| 194 |        16.58 |  4    | Male   | Yes      | Thur  | Lunch  |      2 |
| 195 |         7.56 |  1.44 | Male   | No       | Thur  | Lunch  |      2 |
| 196 |        10.34 |  2    | Male   | Yes      | Thur  | Lunch  |      2 |
| 197 |        43.11 |  5    | Female | Yes      | Thur  | Lunch  |      4 |
| 198 |        13    |  2    | Female | Yes      | Thur  | Lunch  |      2 |
| 199 |        13.51 |  2    | Male   | Yes      | Thur  | Lunch  |      2 |
| 200 |        18.71 |  4    | Male   | Yes      | Thur  | Lunch  |      3 |
| 201 |        12.74 |  2.01 | Female | Yes      | Thur  | Lunch  |      2 |
| 202 |        13    |  2    | Female | Yes      | Thur  | Lunch  |      2 |
| 203 |        16.4  |  2.5  | Female | Yes      | Thur  | Lunch  |      2 |
| 204 |        20.53 |  4    | Male   | Yes      | Thur  | Lunch  |      4 |
| 205 |        16.47 |  3.23 | Female | Yes      | Thur  | Lunch  |      3 |
| 206 |        26.59 |  3.41 | Male   | Yes      | Sat   | Dinner |      3 |
| 207 |        38.73 |  3    | Male   | Yes      | Sat   | Dinner |      4 |
| 208 |        24.27 |  2.03 | Male   | Yes      | Sat   | Dinner |      2 |
| 209 |        12.76 |  2.23 | Female | Yes      | Sat   | Dinner |      2 |
| 210 |        30.06 |  2    | Male   | Yes      | Sat   | Dinner |      3 |
| 211 |        25.89 |  5.16 | Male   | Yes      | Sat   | Dinner |      4 |
| 212 |        48.33 |  9    | Male   | No       | Sat   | Dinner |      4 |
| 213 |        13.27 |  2.5  | Female | Yes      | Sat   | Dinner |      2 |
| 214 |        28.17 |  6.5  | Female | Yes      | Sat   | Dinner |      3 |
| 215 |        12.9  |  1.1  | Female | Yes      | Sat   | Dinner |      2 |
| 216 |        28.15 |  3    | Male   | Yes      | Sat   | Dinner |      5 |
| 217 |        11.59 |  1.5  | Male   | Yes      | Sat   | Dinner |      2 |
| 218 |         7.74 |  1.44 | Male   | Yes      | Sat   | Dinner |      2 |
| 219 |        30.14 |  3.09 | Female | Yes      | Sat   | Dinner |      4 |
| 220 |        12.16 |  2.2  | Male   | Yes      | Fri   | Lunch  |      2 |
| 221 |        13.42 |  3.48 | Female | Yes      | Fri   | Lunch  |      2 |
| 222 |         8.58 |  1.92 | Male   | Yes      | Fri   | Lunch  |      1 |
| 223 |        15.98 |  3    | Female | No       | Fri   | Lunch  |      3 |
| 224 |        13.42 |  1.58 | Male   | Yes      | Fri   | Lunch  |      2 |
| 225 |        16.27 |  2.5  | Female | Yes      | Fri   | Lunch  |      2 |
| 226 |        10.09 |  2    | Female | Yes      | Fri   | Lunch  |      2 |
| 227 |        20.45 |  3    | Male   | No       | Sat   | Dinner |      4 |
| 228 |        13.28 |  2.72 | Male   | No       | Sat   | Dinner |      2 |
| 229 |        22.12 |  2.88 | Female | Yes      | Sat   | Dinner |      2 |
| 230 |        24.01 |  2    | Male   | Yes      | Sat   | Dinner |      4 |
| 231 |        15.69 |  3    | Male   | Yes      | Sat   | Dinner |      3 |
| 232 |        11.61 |  3.39 | Male   | No       | Sat   | Dinner |      2 |
| 233 |        10.77 |  1.47 | Male   | No       | Sat   | Dinner |      2 |
| 234 |        15.53 |  3    | Male   | Yes      | Sat   | Dinner |      2 |
| 235 |        10.07 |  1.25 | Male   | No       | Sat   | Dinner |      2 |
| 236 |        12.6  |  1    | Male   | Yes      | Sat   | Dinner |      2 |
| 237 |        32.83 |  1.17 | Male   | Yes      | Sat   | Dinner |      2 |
| 238 |        35.83 |  4.67 | Female | No       | Sat   | Dinner |      3 |
| 239 |        29.03 |  5.92 | Male   | No       | Sat   | Dinner |      3 |
| 240 |        27.18 |  2    | Female | Yes      | Sat   | Dinner |      2 |
| 241 |        22.67 |  2    | Male   | Yes      | Sat   | Dinner |      2 |
| 242 |        17.82 |  1.75 | Male   | No       | Sat   | Dinner |      2 |
| 243 |        18.78 |  3    | Female | No       | Thur  | Dinner |      2 |
</details>
他にも、アヤメのデータ`irix`や、タイタニックのデータ`titanic`などがあります。
使用可能なデータセットは、`sns.get_dataset_names()`で確認できます。

### catplotの使い方
catplotとは、洗練された可視化を短いコードで実現可能な関数です。
kindでグラフの種類を指定可能であり、散布図`"strip"`、箱ひげ図`"box"`、バイオリンプロット`"violin"`、棒グラフ`"bar"`、ポイントプロット`"point"`、カウントプロット`"count"`があります。
```python
import seaborn as sns

df = sns.load_dataset("diamonds")

g = sns.catplot(
    data=df,         # データセットを指定
    kind="bar",      # グラフの種類を選択
    x="cut",         # X軸に"cut"を設定
    y="carat",       # Y軸に"carat"を設定
    hue="color",     # "color"に基づいて色分け
    col="clarity",   # "clarity"に基づいて列を分ける
    col_wrap=4,      # 一行あたりの列数を4に設定
    height=3,        # 各グラフの高さ
    aspect=1.0,      # 各グラフのアスペクト比 (大きいほど横長)
    errwidth=1.2,    # エラーバーの太さ
    # palette="Set2",  # カラーパレット（デフォルトが良いのでコメントアウト）
)
g.tick_params(axis="x", rotation=30)
```
![](https://raw.githubusercontent.com/C-Naoki/zenn-archive/main/images/python-tips-collection/catplot.png)

### 凡例の位置を変更する方法
凡例はデフォルトで、グラフの右側に表示される。位置を変更したい時は、`sns.move_legend`を用いる。
```python
sns.move_legend(
  obj=g,                        # g = sns.catplot()の戻り値
  loc="upper center",           # 凡例の位置を指定
  ncol=7,                       # 凡例の列数
  bbox_to_anchor=(0.51, 1.22),  # 凡例の位置を調整
  fontsize=14.5,                # 凡例のフォントサイズ
  frameon=True,                 # 凡例の枠線を表示
  columnspacing=0.5             # 凡例の列間のスペース
)
```
![](https://raw.githubusercontent.com/C-Naoki/zenn-archive/main/images/python-tips-collection/legend.png)

### 保存したpdfに凡例が表示されない時の対処法
グラフをg.fig.savefig()で保存した際に、凡例が表示されないことがある。その場合は、`bbox_inches="tight"`を追加する。
```python
g.fig.savefig("name.pdf", bbox_inches="tight")
```