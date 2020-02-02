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

BigQuery のデータ加工処理のテストのためにテストデータが必要になった。カラムにはネストまたは繰り返しの構造が含まれる。


## 検討

### データの入力と生成

当初は元ファイルは適当なテキスト形式で作って CLI で出力しようとしていたが、テストデータ作成担当者の端末へのランタイムインストールの制限があった。

そこで代替手段として Google Sheets にデータを入力してもらい、Google Apps Script でデータを生成するフローを採ることにした。


最初はネストや繰り返しデータを各カラムに入力して、CSV形式でダウンロードしたもので対応できるかと思っていた。

が、BigQuery にロードするデータでネストまたは繰り返しの構造が含まれる場合は CSV では連携できない。

> CSV ファイルはネストされたデータや繰り返しデータに対応していません。
> 
> [Cloud Storage からの CSV データの読み込み - 制約事項](https://cloud.google.com/bigquery/docs/loading-data-cloud-storage-csv?hl=ja#limitations)

ということで、Google Sheets の各セルの入力を JSONL に変換する必要がある。


### ネストや繰り返し構造の実現

単純に一階層のハッシュ構造であれば

|key1|key2|
|:--:|:--:|
|val11|val12|
|val21|val22|

を

``` json
{
    "key1": "val11",
    "key2": "val12"
},
{
    "key1": "val21",
    "key2": "val22"
}
```

するのは簡単だが、実際にはネストや繰り返しが入るので、この方式のままだと各セルに Object や Array を直接記述することになり、複雑なデータ構造を記載するときにミスが発生する可能性がある。

CSV から JSON に変換したいという要望は一般にありそうだと思ったので、調べてみた。すると Node.js のライブラリ [Keyang/node-csvtojson](https://github.com/Keyang/node-csvtojson) で [Lodash](https://lodash.com/) の `set` メソッドを使った方法があったのでそれを使うことにした。


``` javascript
Lodash.set(object, path, value)
```

という API で `path` に JavaScript のオブジェクト構造を設定すると中間階層を含めてオブジェクトを設定してくれる。

このメソッドを使うと

|key1|key2.keykey21|key2.keykey22|key3.0|key3.1|
|:--:|:--:|:--:|:--:|:--:|
|val11|val12|val13|val14|val15|
|val21|val22|val23|val24|val25|

から

``` json
{
    "key1": "val11",
    "key2": {
        "keykey21": "val12",
        "keykey22": "val13"
    },
    "key3": [
        "val14",
        "val15"
    ]
},
{
    "key1": "val21",
    "key2": {
        "keykey21": "val22",
        "keykey22": "val23"
    },
    "key3": [
        "val24",
        "val25"
    ]
}
```

を生成することができる。


### 真偽値と NULL

上記までの対応で JSON のデータ型の

* 数値
* 文字列
* 配列
* オブジェクト

は対応できた。残りの

* 真偽値
* null

の対応が残っている。

幸い Google Sheets にはシートに型が設定されていて、真偽値に関しては文字列エスケープ(先頭にシングルクォートを付ける)をしない `TRUE` / `FALSE` の文字列入力で対応できる。

問題は null の方で、関数の処理の中では null はあるが、シートの記載はできないようなので、`mysqldump` のディフォルトと同じ `\N` を null のテキスト表記として変換することにした。


## コード

Lodash.js は Google Apps Script 版の [contributorpw/lodashgs](https://github.com/contributorpw/lodashgs) を使った。

* `input` シート： ヘッダとデータを記載
* `output` シート： JSONL データを出力

エラー処理を除いてコードは以下

``` javascript
function sheets2jsonl() {
  var _ = LodashGS.load();
  var inputSheet = SpreadsheetApp.getActive().getSheetByName('input');
  var outputSheet = SpreadsheetApp.getActive().getSheetByName('output');
  
  var inputData = inputSheet.getDataRange().getValues();
  var headerLine = inputData.shift();
  var resultLines = [];
  
  for (var i = 0; i < inputData.length; i++) {
    var jsonLine = {};
    
    for (var j = 0; j < headerLine.length; j++) {
      var value = (inputData[i][j] === "\\N") ? null : inputData[i][j];
      _.set(jsonLine, headerLine[j], value);
    }
    
    resultLines.push([JSON.stringify(jsonLine)]);
  }
  
  outputSheet.clear();
  outputSheet.getRange(outputSheet.getLastRow() + 1, 1, resultLines.length, 1).setValues(resultLines);
}
```

この関数を Google Sheets のシートのメニューに追加するなどすれば対応完了。
