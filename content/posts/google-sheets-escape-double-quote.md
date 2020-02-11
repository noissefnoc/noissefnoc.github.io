---
title: "Google Sheets の数式内でのダブルクオートのエスケープ"
date: "2020-02-10T00:00:00+09:00"
draft: false
keyword: [ google-sheets ]
tags: [ gsuite, google-sheets ]
categories: [ tips ]
summary: "Google Sheets の数式内でのダブルクオートのエスケープはダブルクォート派閥だった。"
authors: noissefnoc
menu: posts
---

Google Sheets の数式内で文字列を結合するさいに、ダブルクォートを使いたい。

が、バックスラッシュエスケープがダメで、色々調べてみたところ、Google Sheets はダブルクオートをダブルクォートでエスケープする派閥だった。

つまり

```
=CONCATENATE("""", B1, """")
```

でいける。

Google で検索するとダブルクォートの ASCII コード (34)を指定した

```
=CONCATENATE(CHAR(34), B1, CHAR(34))
```

の用例が結構あったけれど、これだと一般の人には可読性低いのでエスケープ使った方がいいのではと思ってメモした。
