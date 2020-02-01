---
title: "lodash.js を使って Google Sheets の入力値から JSONLを作成する"
date: "2020-01-31T00:00:00+09:00"
draft: false
keyword: [ google-apps-script ]
tags: [ google-apps-script ]
categories: [ productivity ]
summary: "lodash.js の `lodash.set` を使ってヘッダに JavaScript Object の Path をヘッダにした Google Sheets の入力値から JSONL を生成する Google Apps Script を作成した。"
authors: noissefnoc
menu: posts
---

## tl;dr

lodash.js の `lodash.set` を使ってヘッダに JavaScript Object の Path をヘッダにした Google Sheets の入力値から JSONL を生成する Google Apps Script を作成した。


## やりたいこと

BigQuery のデータ加工処理のテストのためにテストデータが必要になった。最初 CSV で作ろうとしたが、ネストした構造が含まれる場合は CSV では連携できない。

> CSV ファイルはネストされたデータや繰り返しデータに対応していません。
> 
> [Cloud Storage からの CSV データの読み込み - 制約事項](https://cloud.google.com/bigquery/docs/loading-data-cloud-storage-csv?hl=ja#limitations)
