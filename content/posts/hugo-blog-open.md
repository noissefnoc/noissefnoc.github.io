---
title: "Hugo + GitHub Pages + GitHub Actions で技術メモを開始した"
date: "2020-01-06T00:00:00+09:00"
draft: false
keyword: [ hugo, github-pages, github-actions, blog ]
tags: [ golang, github-actions ]
categories: [ blog ]
summary: "Hugo + GitHub Pages + GitHub Actions で技術メモを開始しました。"
authors: noissefnoc
menu: posts
---

GitHub の草を生やすのにできればコードを書き続けたいのですが、昨年トライしてみたところそうもいかないことがありました。

過分余暇時間で Blog を書いたりスニペットを書いたりもするのですが、両方とも GitHub の Contributions に反映されないので、反映できるようにまずは　Blog を GitHub Pages にしてみました。

スタックは

* Visual Code Studio: エディタ
* Hugo: 静的ファイルネジェレータ
* Hugo テーマ: Zzo
* ホスティング: GitHub Pages
* ジェネレート: GitHub Actions

で実施しています。

`git push` をトリガーにして GitHub Actions で `master` ブランチにジェネレートされた静的ファイルをコミットし、 GitHub Pages に反映するようにしています。


## Reference

* [Hugo](https://gohugo.io/)
* [Hugo Theme Zzo](https://themes.gohugo.io/hugo-theme-zzo/)
* [GitHub Actions Hugo Setup](https://github.com/marketplace/actions/hugo-setup)
* [GitHub Actions GitHub Pages](https://github.com/marketplace/actions/github-pages-action)
