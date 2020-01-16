---
title: "Python でマルチプラットフォームのファイルパスを扱う"
date: "2020-01-16T00:00:00+09:00"
draft: false
keyword: [ python, builtin-functions ]
tags: [ python ]
categories: [ blog ]
summary: "Python でマルチプラットフォームのファイルパスを扱う方法について調べてみた。"
authors: noissefnoc
menu: posts
---

AWS のクレデンシャルファイルのパスが Widnows と　POSIX 互換システムで以下のように違う。

* Windows: `%USERPROFILE%\.aws\credentials`
* その他： `$HOME/.aws/credentials`

これをプラットフォームの分岐なしで記載したい。

* `os.path.expanduser` : マルチプラットフォームでユーザのホームディレクトリを取得
* `os.path.join` : マルチプラットフォームのパスの結合 (セパレータを加味)

を使って以下のように記載する。

``` python
from os.path import expanduser, join

if __name__ == '__main__':
    user_home_path = expanduser('~')
    aws_credential_file_path = join(user_home_path, '.aws', 'credentials')
```

以上。
