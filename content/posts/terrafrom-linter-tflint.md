---
title: "tflint のインストールと設定"
date: "2020-02-09T00:00:00+09:00"
draft: false
keyword: [ terraform ]
tags: [ terraform ]
categories: [ tips ]
summary: "tflint のインストールと設定"
authors: noissefnoc
menu: posts
---

## インストール

macOS なので `homebrew` を使う。

``` console
$ brew install tflint
```

## ルール設定

標準だと `.tflint.hcl` を読むので、そこに対象のルールを設定する。パスは `--config` オプションで変更可能。
