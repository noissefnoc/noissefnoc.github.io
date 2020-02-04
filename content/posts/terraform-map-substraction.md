---
title: "terraform で対象の key を 除いた Map を取得する"
date: "2020-02-04T00:00:00+09:00"
draft: false
keyword: [ terraform ]
tags: [ terraform ]
categories: [ tips ]
summary: "terraform で対象の key を除いた Map を取得するには for で対応する。"
authors: noissefnoc
menu: posts
---

## tl;dr

terraform で対象の key を除いた Map を取得するには for で対応する。

``` terraform
{ for k, v in {a: 1, b: 2}: k => v if !contains(["a"], k) }
```

確認した terraform のバージョンは `v0.12.19` 。


## やりたいこと

terraform で [AWS Config](https://aws.amazon.com/jp/config/) 対象のマネージドルールを指定するときに

``` terraform
locals {
    target_identifiers = {
        s3-bucket-public-read-prohibited = "S3_BUCKET_PUBLIC_READ_PROHIBITED"
        // 以下略
    }
}

resource "aws_config_rule" "rule" {
    for_each = local.target_identifiers
    name = each.key
    // 中略
    source = {
        owner = "AWS"
        source_identifier = each.value
    }
}
```

と `for_each` を使いながら例示では直接内容を記載しているが `target_identifiers` のところに AWS Config のマネージドルールを記載していくことになる。

上記のコードはモジュール側に配置されていて、基本的に対象とするリソースは設定しておきたいのだけど、アカウントによって個別の足し引きをしたいケースがあり、それに対応したい。

足すのは

``` terraform
merge(base_map, additional_map)
```

でよいのだが、引く方は `list` のときと同じく自前で対応する必要があることが分かった。

※ `base_map` と `additional_map` に同じキーがあった場合は第二引数の `additional_map` の設定で上書きされる。


## 対応

結論書くと

``` terraform
{ for k, v in {a: 1, b: 2}: k => v if !contains(["a"], k) }
```

でよい。

今回の対象の Map の要素は最大でも100ちょっとだし、個別に除外するものも数個なので大雑把でいいだろうという判断。


総合すると以下のようなイメージか

``` terraform
locals {
    target_map = { for k, v in merge(module.aws_config.base_map, var.additional_map): k => v if !contains(var.substraction_key_list, k) }
}
```

で、 `target_map` を `for_each` に渡す。
