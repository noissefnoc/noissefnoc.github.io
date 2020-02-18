---
title: "terraform の list の JSON Array の文字列表現への変換"
date: "2020-02-19T00:00:00+09:00"
draft: false
keyword: [ terraform ]
tags: [ terraform ]
categories: [ tips ]
summary: "terraform の list の JSON Array の文字列表現への変換するには join して愚直に文字列を作成する"
authors: noissefnoc
menu: posts
---

terraform v0.12.19 で確認。

terraform で AWS IAM Policy Document を作成するさいにヒアドキュメントに JSON Array を埋め込みたいケースがあった。

terraform 内部では list で処理をしたいのだが、組み込みの `tostring` 関数は [tostring Function](https://www.terraform.io/docs/configuration/functions/tostring.html) 通りで

> Only the primitive types (string, number, and bool) can be converted to string. All other values will produce an error. 

と、 list には対応していない。

ということで、文字列表現を自分で生成する必要がある。

`join` 関数で結合する。

```.tf
"[\"${join("\", \"", var.target_list)}\"]"
```

`var.list` の中身が `["foo", "bar"]` だとすると

```
"[\"foo\",\"bar\"]"
```

になる。これが変数として埋め込むと

```
["foo","bar"]
```

になる。


このままだと `var.list` の中身が `[]` のときに

```
[""]
```

になる。長さが0での条件分岐が必要だが、単純に式で書こうとするとうまくいかないので [Directives](https://www.terraform.io/docs/configuration/expressions.html#directives) を使う

```.tf
"%{ if length(var.list) > 0 }[\"${join("\",\"", var.list)}\"]%{ else }[]%{ endif }"
```

これで  `var.list` の中身が `[]` のときに文字列の評価結果が

```
[]
```

になる。

文字列にするときに制御やループがネストする場合、Directives 使わずに一度変数定義していたけれど、これで対応可能。
