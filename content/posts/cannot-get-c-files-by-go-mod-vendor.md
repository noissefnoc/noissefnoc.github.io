---
title: "Go 1.13 の go mod vendor で C/C++ 系のファイルがダウンロードされない問題とその対応"
date: "2020-02-28T00:00:00+09:00"
draft: false
keyword: [ golang, c/c++ ]
tags: [ golang, c/c++ ]
categories: [ golang ]
summary: "調べた範囲ではGo 1.13 の  go mod vendor では特定条件の C/C++ 系のファイルがダウンロードされないので、vendor ディレクトリ配下で git clone する必要がある"
authors: noissefnoc
menu: posts
---

## tl;dr

Go 1.13 の `go mod vendor` の仕様として package でないサブディレクトリはコピーされない。ので、パッケージの提供元が non-package directory に C/C++ 系のファイルを配置してると取得できない。

Russ Cox も [golang/go](https://github.com/golang/go) の [Issue](https://github.com/golang/go/issues/26366#issuecomment-405683150) のコメントでそう書いてるのでしゃーなし。

エラーを見てそのディレクトリを削除して `git clone` するしかない。


## きっかけ

あるパッケージを `go mod vendor` した後にビルドしようとして以下のエラーが起こった。

```
# github.com/karalabe/hid
vendor/github.com/karalabe/hid/hid_enabled.go:38:11: fatal error: 'hidapi/mac/hid.c' file not found
        #include "hidapi/mac/hid.c"
                 ^~~~~~~~~~~~~~~~~~
1 error generated.
```

該当のパッケージレポジトリは [karalabe/hid](https://github.com/karalabe/hid) で、レポジトリをみると

* `hidpi/mac/hid.c`

というCファイルがあるが、 `vendor` ディレクトリ側にはエラーメッセージどおり、そもそも `hidpi` ディレクトリごとない。


## 調査

エラーメッセージで調べてみると Golang の公式レポジトリの以下の issue にたどり着いた。

* [cmd/go: go mod -vendor prunes non-package directories #26366](https://github.com/golang/go/issues/26366)

で、 Russ Cox が「残念だけどそれが仕様(`go mod` では non-package のサブディレクトリは取ってこない)なので、必要なやつコピーしてくれ」と言って issue を close しているのでしゃーなし(結構 downvote もらってるけど)。

とはいえ、無碍にしているわけではなくて、同 Issue で紹介された `modvendor` コマンドの Golang 本体へのポーティングを提案する Issue 

* [cmd/go: add modvendor sub-command #27618](https://github.com/golang/go/issues/27618)

では Russ Cox がこの Issue に `Go1.14` と `Backlog` のマイルストーンを設定しているので、対応してくれないわけではなさそう。

で、先日リリースがあった Go1.14

* [Go 1.14 Release Notes](https://golang.org/doc/go1.14)

には、その記述がない気がするけど、Issue も Close されてないしまだ対応されていないということか。


## 対応

仕方ないので `vendor` のディレクトリ配下の `hid` ディレクトリを削除して `git clone` した。

仕様的には仕様を守って作っていない方が悪いのでしょうが、早いところ対応して欲しいですね。
