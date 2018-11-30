---
title: "メイキング・ブログ後編(Hugo)"
date: 2018-11-30T16:23:42+09:00
---

特にこだわりがなくJekyllを触っていたら、いつの間にかHugoに寝返っていた。Jekyllのドキュメントが見づらく要求を満たせずIssueの海に潜ることの辛さに音を上げたのだった。他方、Hugoのドキュメントはしっかりしている。

前編で溶けた時間の大半はCircleCI由来だったので、Hugoへの以降は大したことなかろう。そう高をくくっていたら、またCircleCIに時間を無限に溶かされた。

## インストール

管理者権限コマンドプロンプトで

```
choco install hugo
```

## 何か書く

```
hugo new site myblog # 新規サイト作成
hugo new posts/my-first-post.md # 記事作成
hugo server -D # サーバ
hugo # ビルド
```

記事の更新はホットリロードである。ただし、`config.toml`をいじった場合は再起動させる必要がある。

## テーマの適用

git submodule で管理するのが定石。

```
cd themes
git submodule add https://github.com/tmaiaroto/hugo-redlounge
```

config.toml にパラメータを求められることがあり、テーマのREADMEを確認する。

```
title = "ロックスター・エナジードリンク"
baseURL = "https://higgma.github.io/rockstar/"
languageCode = "ja-jp"

# github pages
publishDir = "docs"

# for summary
hasCJKLanguage = true
summaryLength = 140

# tmaiaroto/hugo-redlounge
theme = "hugo-redlounge"

[params]
    sidebartitle = "Rockstar"
    sidebartagline = "Is super awesome"
```

## 数式の有効化

数式エンジンにはMathJaxを選んだ。Jupyter NotebookがMathJaxなので揃えたかった。

Hugoはpartial[^ref2]と呼ばれる context-aware components を定義することができる。ここにシュッとMathJaxのコードを入れてしまえばいい[^ref1]。

```html
<!-- layouts/partials/footer.html -->
<script type="text/x-mathjax-config">
    MathJax.Hub.Config({
        tex2jax: {inlineMath: [['$','$'], ['\\(','\\)']]}
    });
</script>
<script type="text/javascript" 
    async src="https://cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.2/MathJax.js?config=TeX-MML-AM_CHTML">
</script>
```

$F$

$$
y = f(x)
$$

[^ref1]: https://m0t0k1ch1st0ry.com/blog/2017/10/07/mathjax/
[^ref2]: https://gohugo.io/templates/partials/

## CircleCI

CIの気持ちがよくわからず疲弊する。はやく脳死で書けるようになりたい。

```
version: 2.1
jobs:
  build:
    docker:
      - image: golang:latest
    working_directory: ~/repo
    steps:
      - checkout
      - run: git submodule sync
      - run: git submodule update --init --recursive
      - run: go get -v github.com/gohugoio/hugo
      - run: hugo
      - run:
          name: Git Push
          command: |
              git config --global user.name CircleCI
              git config --global user.email "<>"
              git add -A

              if [ "$(git diff --cached --numstat | wc -l)" -ne "0" ]; then
                git commit -m "[ci skip] Circle CI"
                git push origin master
              else
                echo "nothing to commit."
              fi
```

Insightを見ると、ビルド時間はだいたい1分ぐらいである。

ブログのコミットメッセージは、最初まじめに書いていてもそのうち誤字を軽くいじったりするために、 `git pull origin master; git add .; git commit -m "a"; git push origin master` などやるようになり、治安が悪くっていく。
