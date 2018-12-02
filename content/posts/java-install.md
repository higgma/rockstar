---
title: ジャバ戦争への参加
date: 2018-12-02
---

Java環境のことをJDKと呼ぶ。Java史を追うと、現在はJDK王位継承問題に特徴づけられており、色々なJDKの入手先が併存しているようだ。

JDKのオープンソース実装としてOpenJDKがある。Amazon Correttoはマルチプラットフォーム、無償、LTSを売りとする[^1]OpenJDKである。

[^1]: https://gihyo.jp/news/nr/2018/11/1501

## Installation

[Amazon Corretto Production-ready distribution of OpenJDK](https://aws.amazon.com/jp/corretto/)

からインストーラをDLしてインストールする。
その後、Path環境変数に `%JAVA_HOME%\bin`  を与える。

```
$ java -version
java version "1.8.0_192"
Java(TM) SE Runtime Environment (build 1.8.0_192-b12)
Java HotSpot(TM) Client VM (build 25.192-b12, mixed mode, sharing)
```

## Links

- [Amazon Corretto 8 Installation instructions for Amazon Linux 2 - Amazon Corretto 8](https://docs.aws.amazon.com/ja_jp/corretto/latest/corretto-8-ug/windows-7-install.html)
