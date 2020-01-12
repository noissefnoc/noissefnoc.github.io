---
title: "Python の input 関数の出力を標準エラー出力に向ける"
date: "2020-01-11T00:00:00+09:00"
draft: false
keyword: [ python, stderr ]
tags: [ python ]
categories: [ blog ]
summary: "Python の input 関数の出力を標準エラー出力に向ける"
authors: noissefnoc
menu: posts
---

もう少しマシな方法があるかもしれないけれど、一旦できたので記録。

Python で CLI を作っていて、Username/Password を対話的に入力して環境変数を `export` する構文を出力したいという要件があった。

例示すると以下

``` console
# そのまま実行すると以下
$ python cli.py
Username:             # ユーザ名を対話的に入力
Password:             # パスワードを対話的に入力
export X_TOKEN=yyyyy

# こうやると環境変数に定義されて欲しい
$ $(python cli.py)
$ echo ${X_TOKEN}
yyyyy
```

上記のようにするためにはUsernameとPasswordのプロンプト両方が標準出力ではなく、標準エラー出力に出力して、環境変数の箇所だけを標準出力に出力する必要がある。


Passowrd のように echo back させない対話入力は `getpass` モジュールの `getpass` 関数を使う

[getpass --- 可搬性のあるパスワード入力機構](https://docs.python.org/ja/3/library/getpass.html)

`getpass` 関数には `stream` 引数にプロンプトの出力先を変更できる。

``` python
from getpass import getpass
import sys


if __name__ == '__main__':
    password = getpass('Password: ', stream=sys.stderr)
```

問題は echo back する方で、標準の `input` 関数を使うのだけど、これには出力先を選択するオプションがない。

[組み込み関数 --- input()](https://docs.python.org/ja/3/library/functions.html#input)

調べてみると10年以上前から issue には上がっていた

[Change input() to always prompt to stderr](https://bugs.python.org/issue1927)

ここに上がっているパッチだと単純に `stdout` を `stderr` に差し替えられていたが、マージされる様子はなさそうだった。

Python のレイヤーで言うと `getpass.getpass` と同じようにオプション引数を追加すればいいような話ではあるが、実態の `PyOS_Readline` あたりの諸々に影響がある？

暫定的な対応をすると `getpass` 関数と合わせて以下のような処理を書いた。

``` python
import sys
from typing import IO


def getusername(prompt: str, stream: IO[str] = sys.stderr) -> str:
    # 標準出力を指定した先に変更
    sys.stdout = stream

    # 対話的に入力を取得
    username = input(prompt)

    # 標準出力を処理起動時の標準出力に戻す
    sys.stdout = sys.__stdout__

    return username

if __name__ == '__main__':
    try:
        username = getusername('Username: ')
    except KeyboardInterrupt:
        # input 関数実行時に Ctrl-C などで止められたときに標準出力を元に戻しておく
        # ここやる必要ないかも
        sys.stdout = sys.__stdout__
```

他にいい方法があるかもしれないが、一旦はこんなもので
