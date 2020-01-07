---
title: "Bigquery GIS のデータを Google DataStudio で使う方法"
date: "2020-01-01T00:00:00+09:00"
draft: false
keyword: [ bigquery, datastudio, gis, sql ]
tags: [ bigquery, datastudio, gis, sql ]
categories: [ tips ]
summary: "Google BigQuery の `GEOGRAPHY` データ型はそのままでは Google DataStudio では使えないので(取り込み対象カラムとして表示されない)以下のように変換しておく必要がある。"
authors: noissefnoc
menu: posts
---

## tl;dr

Google BigQuery の `GEOGRAPHY` データ型はそのままでは Google DataStudio では使えないので(取り込み対象カラムとして表示されない)以下のように変換しておく必要がある。

``` sql
SELECT
    CONCAT(CAST(ST_Y(p) AS STRING), ",", CAST(ST_X(p) AS STRING)) AS lat_lng
FROM
    my_project.geo_table
```

このさい

* `GEOGRAPHY` データ型
    * `(経度, 緯度)` で保存されている (つまり `ST_Y` で緯度、 `ST_X` で経度が取れるということ)
* DataStudio の緯度経度型
    * `緯度,経度` のフォーマットを期待している

と、緯度経度の順番が逆になっていることに注意。


## 本文

###  きっかけ

Google BigQuery の地理情報をデータポータルで可視化する仕事があったが、データポータルの BigQuery テーブル指定だと `GEOGRAPHY` 型のカラムが出てこなかった。


### 対応

データポータルで読み取れるのは緯度経度なので

* BigQuery であらかじめ変換したテーブルを用意しておく
* データポータルの BigQuery 取得でカスタムクエリを書く

の方法が考えられますが、今回は後者を選びました。

カスタムクエリに以下のようなクエリをセットして

``` sql
-- カスタムクエリに記載するクエリ
SELECT
    -- その他のカラム
    CONCAT(CAST(ST_Y(p) AS STRING), ",", CAST(ST_X(p) AS STRING)) AS lat_lng
FROM
    my_project.geo_table
-- その他絞り込み条件
```

データポータルのカラムの設定マッピングで「地理フィールド」>「緯度/経度」を選択して完了です。


## Reference

* [BigQuery GIS の構文リファレンス - ST_X](https://cloud.google.com/bigquery/docs/reference/standard-sql/geography_functions?hl=ja#st_x): SQL のサンプルの記載がある
* [データポータルのヘルプ - 地図のリファレンス](https://support.google.com/datastudio/answer/7065037?hl=ja)：「地理フィールド」の「緯度 / 経度」の箇所

