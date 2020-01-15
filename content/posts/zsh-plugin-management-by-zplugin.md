---
title: "zsh のプラグインマネージャーに zplugin を使うことにした"
date: "2020-01-15T00:00:00+09:00"
draft: false
keyword: [ zsh, plugin-management ]
tags: [ zsh ]
categories: [ blog ]
summary: "zsh のプラグインマネージャーに zplugin ことはじめ"
authors: noissefnoc
menu: posts
---

## tl;dr

macOS のバージョンアップに伴って zsh を使い始めたので、プラグインマネージャを検討した結果 [zplugin](https://github.com/zdharma/zplugin) を使うことにした。


## 経緯

bash のときはプラグインマネージャーを使っていなかったけれど、 zsh 使っている人から Oh my zsh のような存在は聞かされていたので、プラグインあるだろうと思ってプラグインマネージャーを調べたところ

* vim で使っていた(多分 neobundle + vim-plug) プラグインマネージャと似たような設定方法
* 同じ設定方式の中では動作が速い

という理由で [zplugin](https://github.com/zdharma/zplugin) を選びました。


## 設定

まだよく分かっていないのですが、コマンドタイプしていくとサジェストが出るプラグインと、ターミナル上でシンタックスハイライトが付くプラグインを入れてみました。

`~/.zshrc` に以下を追加しておいて

``` shell
zplugin light zsh-users/zsh-autosuggestions
zplugin light zdharma/fast-syntax-highlighting
```

再読み込みされたタイミングでインストールされていなければインストールされる仕組みになっています。


## この後のTODO

ちゃんと使いそうなプラグインと、起動時に読み込まなくてもいいプラグインなど、導入と精査を進めていきたいと思います。
