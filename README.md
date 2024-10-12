# zenn-archive
![](https://github.com/C-Naoki/zenn-archive/actions/workflows/publish.yml/badge.svg)

This repository is used to manage and publish articles on [Zenn](https://zenn.dev/) using [Zenn CLI](https://github.com/zenn-dev/zenn-editor) and GitHub. Primarily, it's a place to share code I've created and record what I've learned. This serves as a personal archive that others can also learn from. They are then synced with Zenn for publication. Click [here](https://zenn.dev/naoki0103) for details.

## Directory Structure

- `.github/workflows/`: It contains GitHub Actions workflows.
- `articles/`: It contains Zenn articles written in Markdown format.
- `books/`: It contains Zenn books. The structure should follow the Zenn book guidelines.
- `images/`: It contains images used in articles and books.
- `qiita/`: It contains Qiita articles, which are generated from Zenn articles by `ztoq.sh`.
- `scripts/`: It is used to convert Zenn articles to Qiita articles.

## Getting Started
Run the development server:
```bash
npx zenn preview
```
- Open [http://localhost:8000](http://localhost:8000) with your browser to see the result.

When you want to write a new article:
```bash
npx zenn new:article --slug 記事のスラッグ --title タイトル --type idea --emoji ✨
```
- The above command's options are as follows:
    - `--slug`: The slug of the article. This needs to be a combination of `a-z0-9`, `hyphen (-)`, and `underscore (_)` with 12 to 50 characters.
    - `--title`: The title of the article.
    - `--type`: The type of the article. The options should be chosen from `tech`, `idea`.
    - `--emoji`: The emoji of the article.

### Automatically Publish on Qiita
[`publish.yml`](https://github.com/C-Naoki/zenn-archive/blob/main/.github/workflows/publish.yml) publishes also articles on Qiita when pushing Zenn articles to the main branch. The procedure to setup is as follows:
1. Introduce Qiita-CLI.
    - I refer you to check [official repository](https://github.com/increments/qiita-cli) for more information.
2. Save the token in the Repo Secrets `GH_TOKEN = <Your Qiita Access Token>`.



## References
We appreciate the following articles for providing useful information:

- [zennに記事を投稿したらqiitaにも同時に投稿されるツールを作った話](https://qiita.com/shunk_jr/items/7d1029cae8f83ee8fd84)
- [Zenn / Qiitaに投稿する同じ記事を一元管理するGitHubリポジトリを作りました](https://zenn.dev/ot07/articles/zenn-qiita-article-centralized)
