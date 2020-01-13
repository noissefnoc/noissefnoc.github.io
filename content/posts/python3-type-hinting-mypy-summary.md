---
title: "Python3 の Type Hinting の早見表"
date: "2020-01-13T00:00:00+09:00"
draft: false
keyword: [ python, type-hinting ]
tags: [ python ]
categories: [ blog ]
summary: "Mypy のサイトにあったPython3 の Type Hinting の早見表"
authors: noissefnoc
menu: posts
---

Python 3.5+ から [PEP484](https://www.python.org/dev/peps/pep-0484/) にある型ヒントで「この場合どう書いたらいいんだっけ」というのが分からなかったので調べてみた。

すると Mypy のサイトに以下のようなページがありました。

[Type hints cheat sheet (Python 3)](https://mypy.readthedocs.io/en/latest/cheat_sheet_py3.html)


## 関数のディフォルト値

例えば関数のディフォルト値なら

``` python
def inc(d: int = 1):
    return d + 1
```

のように変数ではなく、型の方にディフォルト値を書く。


## クラス

独自クラスに関してはそのままが型になる

``` python
from typing import List

class Hoge:
    # これはコンストラクタが返ってきてる気がするけど None らしい
    def __init__(self) -> None:
        self.foo: List[str] = []
    
    # インスタンスメソッドの self の方は省略する
    def i_method(self, i: int) -> int;
        return i + 1


if __name__ == '__main__':
    # ユーザ定義のクラスの型はそのままクラス名
    x: Hoge = Hoge()
```


## その他

自分で書いた範囲に関してはこのページの内容で問題なさそうですが

* 外部のライブラリを使ったときはどうなるのか
* 有名なライブラリには型ファイルが用意されているものか

あたりはまた別途調べてみようと思います。
