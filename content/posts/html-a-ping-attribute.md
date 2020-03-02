---
title: "HTML a タグの ping 属性でリンク先とは別のURLに POST する"
date: "2020-03-02T00:00:00+09:00"
draft: false
keyword: [ html, web-analytics ]
tags: [ html ]
categories: [ web-analytics ]
summary: "HTML a タグの ping 属性の存在を知ったので Treasure Data の Pixel Tracking をはめ込んでみてログを出力してみた"
authors: noissefnoc
menu: posts
---

存在を忘れていたのか知らなかったのか分からないが、HTML の a タグには `ping` 属性があって、`ping` 属性にカンマ区切りにした URL のリストを設定することで、a タグをクリックした際に `ping` にある URL に POST リクエストを出してくれる。

例えば Google の検索結果もこれを使っていて、検索結果のこのサイトだと

```html
<a href="https://github.com/noissefnoc/noissefnoc.github.io" ping="/url?sa=t&source=web&rct=j&url=https://github.com/noissefnoc/noissefnoc.github.io&ved=2ahUKEwjigYvXhvznAhXvG6YKHSSNBs4QFjAKegQIBBAB">...</a>
```

といった具合に Google の URL トラッキングのパスにパラメータ付きで POST リクエストを出している。

`ping` 属性に Treasure Data の [Tracking Pixels](https://support.treasuredata.com/hc/en-us/articles/360000685028-Tracking-Pixels) を埋めてやれば計測できるものかやってみた。

例示にあるように

```
https://in.treasuredata.com/postback/v3/event/{DATABASE_NAME}/{TABLE_NAME}
  ?td_format=pixel
  &td_write_key={WRITE_ONLY_KEY_URLENCODED}
  &td_global_id=td_global_id    // ここの値は Treasure Data のサーバ側で置換されるのでまま書く
  &td_ip=td_ip                  // ここの値は Treasure Data のサーバ側で置換されるのでまま書く
  &td_ua=td_ua                  // ここの値は Treasure Data のサーバ側で置換されるのでまま書く
  &url={PAGE_URL_URLENCODED}    // Treasure Data 的にはカスタムパラメータ
  &link={LINK_URL_URLENCODED}   // Treasure Data 的にはカスタムパラメータ
```

のようにして `ping` 属性に埋め込んでおけばリンクを押したタイミングで確かに POST リクエストが飛んでいることが Google Chrome の開発者コンソールで確認でき、Treasure Data のデータベースにも反映されているのが確認できた。Google Analytics の Measurement Protocol も同じような形式だったはずなので多分できると思う。

もっともこの辺りのツールであれば click イベントで取れという話かもしれませんが。


こんな機能をなぜ知らなかったのだろうかと思って W3C のサイトに行くと `ping` 属性の定義がない。

* [4.5.1. The a element](https://www.w3.org/TR/html52/textlevel-semantics.html#the-a-element)

対応ブラウザを caniuse.com で調べてみると

* [Ping Attribute](https://caniuse.com/#search=ping)

と、最近のブラウザは一通り対応していて、Firefox が対応はしているが、フラグをオンにしないと対応しないようだ。で、ここの注記に2010年に仕様からは外れたということで、大体で動くけど、仕様じゃない扱いのものなんですね。
