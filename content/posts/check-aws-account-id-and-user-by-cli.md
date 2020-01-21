---
title: "AWS CLI で現在の認証情報のアカウント ID とユーザを確認する"
date: "2020-01-21T00:00:00+09:00"
draft: false
keyword: [ aws, tips ]
tags: [ aws ]
categories: [ blog ]
summary: "AWS CLI で AWS STS を呼んで現在の認証情報のアカウント ID とユーザを確認"
authors: noissefnoc
menu: posts
---

諸事情で AWS 認証情報ファイル `~/.aws/credentials` にプロファイルを書き込まずに環境変数だけで作業をしているときに、現在のアカウントを確認したくなる時があるので調べてみました。

[get-caller-identity - AWS CLI Command Reference](https://docs.aws.amazon.com/cli/latest/reference/sts/get-caller-identity.html)

``` console
$ aws sts get-caller-identity
```

で、以下スキーマの JSON が返ってくる。

``` javascript
{
    "Account": "AWS_ACCOUNT_TWELVE_DIGIT",
    "UserId": "AWS_USER_ID",
    "Arn": "arn:aws:iam::AWS_ACCOUNT_TWELVE_DIGIT:user/USER"
}
```

毎回実行するのもアレなので、本当は認証情報を環境変数に書き出すさいにユーザ定義環境変数にして書き出した方がいいのかもしれません。
