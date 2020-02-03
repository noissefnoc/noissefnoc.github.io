---
title: "terraform の linter の tflint を使う"
date: "2020-01-28T00:00:00+09:00"
draft: false
keyword: [ terraform ]
tags: [ terraform ]
categories: [ tips ]
summary: "最近 terraform を使うようになったので、linter を探していていて tflint を使うことにした。"
authors: noissefnoc
menu: posts
---

## tl;dr

[terraform-linters/tflint](https://github.com/terraform-linters/tflint) を使うことにしました。


## モチベーション

* DSL とはいえ、言語を書くので Linter が欲しかった
* `terraform plan` で問題なさそうになっていても `terraform apply` で失敗するの辛いので事前に知りたい

## メモ

* `--deep` オプションの詳細を調べる
* インストール手順書く
