---
title: "Semantic Versioning 2.0.0 を改めて読み直してみた"
date: "2020-03-18T00:00:00+09:00"
draft: false
keyword: [ semantic_versioning, development ]
tags: [ semantic_versioning, development ]
categories: [ development, atitude ]
summary: "アプリケーション開発にあたって、Semantic Versioning 2.0.0 を読み直して認識が曖昧だった部分を見直した"
authors: noissefnoc
menu: posts
---

## tl;dr

[Semantic Versioning 2.0.0](https://semver.org/) の Summary の箇所を読んでください。


## 学び

メジャーバージョンを上げるのがなかなかできずに、フラグ付きでマイナーバージョンで機能追加をしてしまってコード側で分岐をもってしまうことになりそうな気もした。

カジュアルにメジャーバージョンを上げないものだとは思うものの、どのケースだと上げて、どのケースだと上げないというのを開発する集団内部で事前の合意をとったほうがいいなと思いました。

API開発においては `.proto` のバージョンと、APIアプリのバージョン両方を加味したバージョンをどうつけるべきかと思いました。


## 参考

* [Semantic Versioning 2.0.0](https://semver.org/)：原文(英語)
* [セマンティック バージョニング 2.0.0](https://semver.org/lang/ja/)：上記の日本語訳
