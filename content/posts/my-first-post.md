---
title: "メイキング・ブログ(Hugo編)"
date: 2018-11-30T16:23:42+09:00
---

特にこだわりがなくJekyllを触っていたら、いつの間にかHugoに寝返っていた。Jekyllのドキュメントが見づらく要求を満たせずIssueの海に潜ることの辛さに音を上げたのだった。他方、Hugoのドキュメントはしっかりしている。

## インストール

管理者権限コマンドプロンプトで

```
choco install hugo
```

新しくつくるには

```
hugo new site myblog
```

## 何か書く

```
hugo new posts/my-first-post.md
```

```
---
title: "My First Post"
date: 2018-11-30T16:23:42+09:00
draft: false
---

Hello World
```

## 表示

```
hugo server -D
```

`-D` をつけると記事の更新はホットリロードになる。ただし、`config.toml`をいじった場合は再起動させる必要がある。

## テーマの適用

```
cd themes
git submodule add https://github.com/tmaiaroto/hugo-redlounge
```

config.toml

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

はやく脳死で書けるようになりたい。

```
version: 2
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
      - run: |
          git config --global user.name CircleCI
          git config --global user.email "<>"
          git add -A
          git commit -m "[ci skip] Circle CI"
      - run: git push origin master
```

コミットメッセージは最初まじめに書いていてもそのうち `git pull origin master; git add .; git commit -m "a"; git push origin master` などやるようになり、治安が悪くっていく。
