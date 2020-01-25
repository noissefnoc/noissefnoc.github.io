---
title: "Terraform v0.12.x ことはじめ"
date: "2020-01-25T00:00:00+09:00"
draft: false
keyword: [ terraform ]
tags: [ terraform ]
categories: [ infrastructure ]
summary: "Terraform v0.12.x 系を使うことになったので環境準備"
authors: noissefnoc
menu: posts
---

## tfenv: Terraform 自身のインストール・バージョン管理

メインのターゲットバージョンは v0.12.x だけど、古いものもあるので、複数バージョン使い分けたい。

そのため [tfenv](https://github.com/tfutils/tfenv) を使うことにしました。Windowsがサポートされてないけど、一旦そこは保留。

### インストール

macOS なので

``` console
$ brew install tfenv
```

でインストール。
