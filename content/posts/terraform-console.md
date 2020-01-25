---
title: "terraform の Syntax と挙動を確認するのに terraform console を使う"
date: "2020-01-22T00:00:00+09:00"
draft: false
keyword: [ terraform ]
tags: [ terraform ]
categories: [ tips ]
summary: "久々に terraform を使うことになって大分 Syntax が変わっていたので、いくつか試してみたかったので、シンタックスチェック(terraform validate)と REPL(terraform console)について調べた。"
authors: noissefnoc
menu: posts
---

久々に terraform を使うことになって大分 Syntax が変わっていたので、いくつか試してみたかったので、シンタックスチェックと REPL について調べた。


## Syntax 確認

[Command: validate - terraform](https://www.terraform.io/docs/commands/validate.html) を使う。 `terraform plan` でもチェックが走るが、こちらだとリモートのリソースにアクセスしに行くのでファイルのみでのオフラインチェックができない。

また、 `terraform validate` を実行するためには `terraform init` を実行しておかなければならないのだが、 backend がリモートの場合 `terraform init` 実行時にリモートアクセスが走るので

```
$ terraform init -backend=false
```

を一度実行しておく。


## 挙動確認

[Command: console - terraform](https://www.terraform.io/docs/commands/console.html) を使う。

現在のディレクトリの state を使いたければ

```
$ terraform console
```

で REPL モードに入る。他の state を使いたければ `-state=path` で指定。

REPL は `exit` ないしは C-C or C-D で終了。

また、以下のようにパイプで標準入力に HCL を渡せば

``` console
$ echo "1 + 1" | terraform console
2
```

評価結果が返ってくる。


