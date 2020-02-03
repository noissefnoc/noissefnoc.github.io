---
title: "terraform の list 同士の差分 list を取得する"
date: "2020-02-03T00:00:00+09:00"
draft: false
keyword: [ terraform ]
tags: [ terraform ]
categories: [ tips ]
summary: "terraform の list 同士の差分 list を取得したい場合、2020/02/03 時点では Builtin Functions に関数がないので、for で対応する"
authors: noissefnoc
menu: posts
---

## tl;dr

terraform の list 同士の差分 list を取得したい場合、2020/02/03 時点では Builtin Functions に関数がないので、for で対応する。

``` terraform
[for v in [1,2,3,4]: v if !contains([2, 4], v)]
```

確認した terraform のバージョンは `v0.12.19` 。


## やりたいこと

terraform で [AWS Config](https://aws.amazon.com/jp/config/) の対象リソースを個別に指定する場合に

``` terraform
resource "aws_config_configuration_recorder" "config_recoder" {
  name     = "default"
  role_arn = "${aws_iam_role.r.arn}"
  recording_group = {
      all_supported = false
      include_global_resource_types = false
      resource_types = [
          // resource list here
      ]

  }
}
```

と `resource_types` のところに AWS のリソースを list で列記していくことになる。

上記のコードはモジュール側に配置されていて、基本的に対象とするリソースは設定しておきたいのだけど、アカウントによって個別の足し引きをしたいケースがあり、それに対応したい。

足すのは

``` terraform
distinct(concat(base_list, additional_list))
```

でよいのだが、引く方は調べてみたものの [標準の関数群](https://www.terraform.io/docs/configuration/functions/) には該当のものがなかった。

[add setsubtract function and docs #23424](https://github.com/hashicorp/terraform/pull/23424) でPRは出ているものの、まだマージされていない様子。


## 対応

結論書くと

``` terraform
[for v in base_list: v if !contains(substraction_list, v)]
```

でよい。

`base_list` の要素は最大でも100以下だし、個別に除外するものも数個なので大雑把でいいだろうという判断。


総合すると以下のようなイメージか

``` terraform
locals {
    add_list    = distinct(concat(module.aws_config.base_list, var.additional_list))
    target_list = [for r in add_list: r if !contains(var.substraction_list, r)]
}
```

で、 `target_list` を `resource_types` に渡す。
