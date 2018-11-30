---
title: "herokuコマンドを使わずにherokuにデプロイする"
date: 2018-11-30T23:01:00+09:00
---

Herokuにアプリをデプロイする場合、普通は`heroku`コマンドを使えという話になるはずだが、使うツールが少なければ少ないほど偉いというマイブームの縛りプレイである。

```
mkdir sandbox
cd sandbox

# 使い捨ての鍵ペア
ssh-keygen -t rsa -b 4096 -C 'username@example.com' -f ./id_rsa

# 適当なWebアプリ
rails new myapp --database=postgresql
cd myapp
```

要するにHerokuさんに公開鍵さえ預けてしまえばデプロイはできるわけで。

HerokuのWeb管理画面を開き、適当なアプリを作り、↑で生成した公開鍵を登録する。

WebアプリのGitリポジトリに、Herokuアプリのリモートリポジトリの場所と、使用する秘密鍵の情報を与える。


```
git remote add heroku git@heroku.com:blooming-oasis-35383.git
git config core.sshCommand 'ssh -i ../id_rsa'
```

ついでに名前。

```
NAME="Higuma"
EMAIL="higgma@example.com"
git config user.name $NAME
git config user.email $EMAIL
```

このようにすると何が嬉しいかというと、 `.git/config` に書いてある情報のみを用いていることがはっきりする。

```
$ cat .git/config
[core]
        repositoryformatversion = 0
        filemode = false
        bare = false
        logallrefupdates = true
        ignorecase = true
        sshCommand = ssh -i ../id_rsa
[remote "heroku"]
        url = git@heroku.com:blooming-oasis-35383.git
        fetch = +refs/heads/*:refs/remotes/heroku/*
[user]
        name = Higuma
        email = higgma@example.com
```

git push する。

```
$ git push heroku master
remote: -----> Launching...
remote:        Released v6
remote:        https://blooming-oasis-35383.herokuapp.com/ deployed to Heroku
remote:
remote: Verifying deploy... done.
To heroku.com:blooming-oasis-35383.git
 * [new branch]      master -> master
```
