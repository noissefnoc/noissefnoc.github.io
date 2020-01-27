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

### terraform のインストール

通常バージョンを指定してインストールするが

* `last` ：最新版
* `last:<regexp>` ：特定マイナー/メジャーバージョン内の最新版
* `min-required` ：`.tf` を見て必要な最小のバージョン

という記述も使える。

### terraform のバージョン固定

`*env` の設定にあるようにフォルダに設定ファイルを用意することでバージョンの指定ができる。

ファイル名は `.terraform-version`

``` console
$ echo 0.12.0 > .terraform-version
```

## terraform 0.12.x のインストール

特に以前のバージョンはないので最新のバージョンを入れる

``` console
$ tfenv install latest
```

インストールされたバージョンで固定する。


### tfenv 向け

``` console
$ echo 0.12.20 > .terraform-version
```

### .tf ファイル向け

``` main.tf
terraform {
    required_version = "<=0.12.20"
}
```

以上。
