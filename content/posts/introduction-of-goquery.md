---
title: "goqyery を使ってみた"
date: "2020-03-01T00:00:00+09:00"
draft: false
keyword: [ golang, library ]
tags: [ golang, library ]
categories: [ golang, library ]
summary: "Golang の HTML セレクタライブラリの goquery を使ってみた"
authors: noissefnoc
menu: posts
---

Golang の あるライブラリにコントリビュートしようとしたときに、HTML のハンドリングを [PuerkitoBio/goquery](https://github.com/PuerkitoBio/goquery) でやっていたので使い方のメモ。

基本コンセプトとしては、`README.md` にあるように JQuery の DOM 操作を参考にしているので、CSS のセレクタで要素を指定したり前後の要素を取ったり、フィルタやマッチャを使うことができる。