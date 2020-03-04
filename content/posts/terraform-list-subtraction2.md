---
title: "terraform の list 同士の差分 list を取得する (v0.12.21 terraform 対応版)"
date: "2020-03-04T00:00:00+09:00"
draft: false
keyword: [ terraform ]
tags: [ terraform ]
categories: [ tips ]
summary: "terraform の list 同士の差分 list を取得したい場合、2020/03/04 時点では Builtin Functions で setsubtract ができたので標準関数で対応可能"
authors: noissefnoc
menu: posts
---

以前に terraform の list 同士の差分 list を取得したい場合には `for` で対応するしかない状態でした。

* [add setsubtract function and docs #23424](https://github.com/hashicorp/terraform/pull/23424)

の Pull Request がマージされて v0.12.21 でリリースされた。ドキュメントは以下

* [setsubtract Function](https://www.terraform.io/docs/configuration/functions/setsubtract.html)

標準関数なので、挙動は `terraform console` で確認できる

新しいバージョンのインストール

```
% tfenv install 0.12.21
% tfenv use 0.12.21
```

`terraform console` の実行

```
% terraform console
> setsubtract(["a", "b", "c"], ["a", "c"])
[
  "b",
]
```

v0.12 へのバージョンアップのリファクタリングも含めてこの辺対応していければなと思います。
