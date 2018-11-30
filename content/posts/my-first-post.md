---
title: "メイキング・ブログ(Hugo編)"
date: 2018-11-30T16:23:42+09:00
---

特に大きなこだわりないしなんでもいいやとばかりにJekyllを触っていたけど、Hugoに寝返った。

巷で言われるホットリロードの速さは嬉しいけども、なくても困らない。
よりクリティカルな問題は、Jekyllではドキュメントの整備が甘いのか、Issueの海に潜るなど辛くなりがちなことであった。

他方、Hugoのドキュメントはしっかりしている。


## インストール

管理者権限コマンドプロンプトで

```
choco install hugo
```

新しくつくるには

```
hugo new site myblog
```

## テーマの入手と適用

テーマはこれにきめた。

https://themes.gohugo.io//theme/kiss/

```
cd themes
git clone https://github.com/ribice/kiss.git
cd ../
echo 'theme = "kiss"' >> config.toml
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

![](https://i.imgur.com/fqOrgQ1.png)

## 数式を書けるようにする

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

## config.toml

```
title = "ロックスター・エナジードリンク"
baseURL = "http://example.org/"
languageCode = "ja-jp"
theme = "kiss"

# github pages
publishDir = "docs"

# for summary
hasCJKLanguage = true
summaryLength = 140
```

## .circleci/config.yml

```yaml
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

[^ref1]: https://m0t0k1ch1st0ry.com/blog/2017/10/07/mathjax/
[^ref2]: https://gohugo.io/templates/partials/
