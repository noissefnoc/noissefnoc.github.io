---
title: "zsh で環境変数 PATH 周りの設定"
date: "2020-01-14T00:00:00+09:00"
draft: false
keyword: [ zsh, environmental-variable ]
tags: [ zsh ]
categories: [ blog ]
summary: "zsh で環境変数 PATH 周りの設定をするときに存在する時だけ追加や重複を排除して設定する方法"
authors: noissefnoc
menu: posts
---

## tl;dr

macOS Catalina からユーザのログインシェルが zsh に変わり、 bash を使っていると「zsh を使え」と警告文が出るので、ボチボチ移行しています。

今回は環境変数 `PATH` の設定の話についてのメモ。

* 環境変数の記述ファイル
* zsh で使える配列変数 path
* パスが存在するときだけ `PATH` に追加
* `PATH` 中の重複パス削除


## 環境変数の記述ファイル

環境変数は `~/.zshenv` に書くのが正しいらしいので、そちら側に移動。


## zsh で使える配列変数 path

環境変数 `PATH` と同期している配列変数 path が使える。

``` shell
export PATH="/usr/local/bin:$PATH"
```

は

``` shell
path=(
    /usr/local/bin
    $path
)
```

と同義。path の方が追加/削除が簡単なのと、次に書くパスが存在する時だけ追加するオプションが利用できるのでこっちを使う。


## パスが存在する時だけ PATH に追加

配列変数 path だと、追加パスの末尾に `(N-/)` をつけることでパスが存在する場合だけ追加することができる。

``` shell
path=(
    $HOME/bin(N-/)
    /usr/local/bin
    $path
)
```

のような形。 `.*env` 系など、ユーザが自分で作成しないとないパスを `PATH` に追加するときに便利。


## PATH の中の重複パス削除

諸々あって、 `PATH` の追加が何回も呼ばれてしまって、重複したパスが追加されたりするケースで、 bash だと少しサイクが必要だったが、zsh だと

``` shell
typeset -U path
```

を使えば良い。これを `~/.zshrc` の最後に書いてる。
