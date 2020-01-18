---
title: "Python の venv でインストールした CLI の挙動"
date: "2020-01-17T00:00:00+09:00"
draft: false
keyword: [ python, venv ]
tags: [ python ]
categories: [ reference ]
summary: "Python の venv でインストールした CLI は shebang がインストール時に書き換えられる    "
authors: noissefnoc
menu: posts
---

Python の venv を使って仮想環境を作って `pip install` していくと `venv/bin` や `venv/lib` 以下にファイルがインストールされるまでは知っていたが、 `source venv/bin/activate` せずに `venv/bin/some-cli` したときの Python の環境はどれを使っているんだろうかと疑問になったので調べてみた。

公式ドキュメントの [venv -- 仮想環境の作成](https://docs.python.org/ja/3/library/venv.html) にずばり答えが書いてあって

> 仮想環境にインストールされたスクリプトには、仮想環境の Python インタプリタを指す "shebang" 行があります。 これはつまり、スクリプトは PATH の値に関係無く、仮想環境のインタプリタで実行されるということです。

ということで `venv/bin/some-cli` の shebang を見ると

``` python
#! /path/to/project_root/venv/bin/python
``` 

となっており、該当 venv の Python が呼ばれるように指定されていた。

そういう仕組みなのかと感心しました。
