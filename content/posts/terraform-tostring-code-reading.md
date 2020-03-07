---
title: "terraform の tostring 関数のコードを追ってみた"
date: "2020-03-06T00:00:00+09:00"
draft: false
keyword: [ terraform ]
tags: [ terraform ]
categories: [ code-reading ]
summary: "terraform の tostring 関数が primitive types しかサポートしていないので、実装がどうなっているのか調べてみた。結果、変換の多くを担っている外部ライブラリで対応しておらず、また、terraform が保持している変換部分でも対応していなかった。"
authors: noissefnoc
menu: posts
---

## モチベーション

terraform の `tostring` 関数がプリミティブ型(`string` / `number` / `bool` )しか変換できない。

> Only the primitive types (string, number, and bool) can be converted to string. All other values will produce an error.
[tostring Function](https://www.terraform.io/docs/configuration/functions/tostring.html)

しかし、AWS のセットアップにおいて `list` を `string` にしてヒアドキュメントに埋め込みたいというユースケースがあり (「`data` を使え」というのがおそらく terraform 的には正しいのだと思うけれど、やり方が悪いのか `terraform plan` のさいの差分範囲が広く見えてしまうので今は直接書いている)、 terraform で `tostring` がどう実装されているのかを調べてみたかったため。


## tl;dr

terraform の `tostring` 関数の変換ロジックの主な部分は [zclconf/go-cty](http://github.com/zclconf/go-cty) に頼っており、そこに変換定義がない。

terraform 側にも変換ロジックを少し持っているが、現状 `list` から `string` に変換する変換を保持していないため、エラーになる。

改修するとしたら terraform 側の箇所になると思うが、 `list` の文字列表記を一意に決めがたい点が問題になりそう。


## コードリーディング

### 前提

コードリーディング時のコミットは [d0d85f9](https://github.com/hashicorp/terraform/commit/d0d85f909a51f100956dc6e9a47d39e4792c476e) です。


### エントリポイントの検索

安直に [hashicorp/terraform](https://github.com/hashicorp/terraform) のレポジトリ内を `tostring` で調べる。

すると [lang/functions.go#117](https://github.com/hashicorp/terraform/blob/d0d85f909a51f100956dc6e9a47d39e4792c476e/lang/functions.go#L117) に関数定義のマッピングがあり、実体が `func.MakeToFunc(cty.String)` だと分かる。

ここで出てものがどこに定義されているかを `import` から確認すると

* `func.MakeToFunc()` -> `github.com/hashicorp/terraform/lang/funcs`
* `cty.String` -> `github.com/zclconf/go-cty/cty`

から呼ばれている。


### lang/funcs.go

[lang/funcs/conversion.go#L19](https://github.com/hashicorp/terraform/blob/d0d85f909a51f100956dc6e9a47d39e4792c476e/lang/funcs/conversion.go#L19) に `MakeToFunc` の定義がある。引数の `wantTy` には `cty.String` が渡されている。

ここで `convert.GetCOnversionUnsafe` と `convert.Convert` が呼ばれ。それぞれが `github.com/zclconf/go-cty/cty/convert` から呼ばれている。

変換は外部ライブラリの [zclconf/go-cty](http://github.com/zclconf/go-cty) で実施している。

### github.com/zclconf/go-cty convert

`convert.Convert` は2つの入力の型を見て 最終的に `convert.GetConversionUnsafe` を呼んでいるので、`convert.GetConversionUnsafe` を追う。

* `GetConversionUnsafe(in cty.Type, out cty.Type) Conversion`
* -> `retConversion(getConversion(in, out, true))`
    * -> `getConversion(in, out, true)`
        * -> `getConversionKnown(in, out, unsafe)`
        * ここで定義済の型変換マッピングがある。で、`cty.String` だと `privitiveConvertionsUnsafe` が呼ばれる
            * -> `privitiveConvertionsUnsafe` で `error` が発生しうる変換の `cty.ParseNumberVal` と `cty.Value.AsString` で変換

となっていて、 `list` / `map` / `tuple` / `set` に対しての `string` の変換がなさそう。

汎用的な変換だと確かに表記が一意ではないので(単純に考えても丸括弧と角括弧の差はある) Pull Request 出しても受理されるかはどうかといったところ。


### lang/funcs.go

再び `lang/funcs.go` に戻ると `MakeToFunc` の fallback 部分があるが、この箇所にあるのは `string` の `bool` と `number` への変換の落穂拾い。

ということで、 `list` から `string` の変換はエラーになる。

もし変換実装を入れるとしたらここになるのではないかと思う。

