---
title: "Gitコミットの名義管理"
date: 2018-11-30T18:44:30+09:00
---

間違って恥ずかしい名前を載せたくないものだ。あるいは、新しいパソコンにGitの名前を設定するのがめんどくさい。

以下のコマンドは、一度実行すれば、`./.git/config` に設定として永続化される。もし手元のPCでochinchinというユーザーネームを使っていても、コミットログにはその気配を残さない。

```
git config user.useConfigOnly true
NAME="Higuma"
EMAIL="higgma@example.com"
git config user.name $NAME
git config user.email $EMAIL
echo "Set user.name as $(git config --get user.name)"
echo "Set user.email as $(git config --get user.email)"
```

`.git` がどのような情報を持っているかを見てみよう。 

```
$ git config --list --local
core.repositoryformatversion=0
core.filemode=false
core.bare=false
core.logallrefupdates=true
core.ignorecase=true
remote.origin.url=https://github.com/higgma/je.git
remote.origin.fetch=+refs/heads/*:refs/remotes/origin/*
user.useconfigonly=true
user.name=Higuma
user.email=higgma@example.com
```

ところで `git config` コマンド全般について、インターネットを見ていると `--global` なる引数を見かけることがある。これは `./.git/config` ではなく `~/.gitconfig` に対して永続化をもたらす。

つまり、設定がPC全体に影響する。常に同じ名義を使う場合、つまり大方のケースでは`--global`をつけるだろうが、特定のプロジェクト（フォルダ）に設定を閉じ込めたい場合、この引数は使わない。
