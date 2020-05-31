---
title: "リモートモブプログラミングのためのビデオチャット比較"
date: "2020-05-31T00:00:00+09:00"
draft: false
keyword: [ mob-programing, videochat ]
tags: [ mob-programing, videochat ]
categories: [ tips ]
summary: "リモートモブプログラミングのためのビデオチャット比較をしてみました。"
authors: noissefnoc
menu: posts
---

昨今の情勢下でリモートでモブプログラミングをする機会があったので、ビデオチャットについて調べてみました。

観点としては

* 無償プランで画面共有機能があるか
    * 会社の場合はそもそも有償の指定されたサービスがあるので個人でもやれる方法を見たい
* 無償プランでの制限
    * 時間の制限は許容するが、人数は3人以上でできるものを使いたい
* 画面書き込み機能
    * ないと意外に「ここの場所」という説明が大変だったり、フォネティックに物事を伝えるのが面倒なので

なお、LINEやSkypeは私が使ってないのと、ChatworkやTypeTalkなどのB向け要素強いものは比較対象から外しました。

比較表は以下になります。

|ビデオチャット名|料金プランページ|無償プランでの画面共有有無|無償プランでの制限(時間や人数)|画面書き込み機能|
|:---------------|:---------------|:-------------------------|:-----------------|:---------------|
|[Zoom](https://zoom.us/)|https://zoom.us/pricing|あり|3人以上の場合40分まで|「画面の共有」の「[視聴者として注釈をつける](https://support.zoom.us/hc/ja/articles/115005706806-%E8%A6%96%E8%81%B4%E8%80%85%E3%81%A8%E3%81%97%E3%81%A6%E6%B3%A8%E9%87%88%E3%82%92%E4%BB%98%E3%81%91%E3%82%8B)」機能(ホスト他による制限あり)|
|[Slack](https://slack.com/intl/ja-jp/)|https://slack.com/intl/ja-jp/pricing|なし|-|あり|
|[Google Meet](https://gsuite.google.co.jp/intl/ja/products/meet/)|https://apps.google.com/meet/pricing/|あり|60分(2020-09-30までは24時間)|標準にはない|
|[Microsoft Teams](https://www.microsoft.com/ja-jp/microsoft-365/microsoft-teams/group-chat-software)|https://www.microsoft.com/ja-jp/microsoft-365/microsoft-teams/compare-microsoft-teams-options|あり|よく分からなかった|よく分からなかった(別途ホワイトボード機能あり)|
|

ということで、想定はしていましたが、無償だと「これでいける」というものはありませんでした。

私の環境だと[VS Code Live Share Audio](https://marketplace.visualstudio.com/items?itemName=MS-vsliveshare.vsliveshare-audio)がちゃんと動かなかったのと、普段VS Code使ってないので、どこかちゃんとしたサービス使ったほうがよさそうですね。

できればホストのみが支払いしていればいいものの方が人を気軽に誘いやすいのでよいのですが。
