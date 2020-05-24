---
title: "gPRC のクライアントツール evans に入門した"
date: "2020-05-24T00:00:00+09:00"
draft: false
keyword: [ grpc, client, tool ]
tags: [ grpc, beginning ]
categories: [ grpc ]
summary: "gRPC の開発にあたってクライアントツールを調べており、今回は evans について調べてみました"
authors: noissefnoc
menu: posts
---

# tl;dr

gPRC の開発を進めていくにあたって HTTP における [curl](https://curl.haxx.se/) のような CLI が欲しいと思って調べました。条件としては

* CLI のためにランタイムを用意したり、手元でビルドする必要がない

というところで、必然的に Go や Rust 製のものになります。ざっと調べたところ以下2つかなというところで

* [ktr0731/evans: Evans: more expressive universal gRPC client](https://github.com/ktr0731/evans)
* [fullstorydev/grpcurl: Like cURL, but for gRPC: Command-line tool for interacting with gRPC servers](https://github.com/fullstorydev/grpcurl)

今回は 5/3 に 0.9.0 が出た `evans` の側を調べてみました。 0.8.5 あたりから 1.0.0 に向けた引数の変更があったようで、そちらの方を見ていきます。

公式の `README.md` や issue に書いてある話のまとめ直しになりますが、私の利用シーンの都合上、 REPL モードの話は記載しません。

## 前提

gRPC Server が Reflection を実装しているものとします。Go の場合だと以下になります。

* [gRPC Server Reflection Tutorial](https://github.com/grpc/grpc-go/blob/master/Documentation/server-reflection-tutorial.md)

## 基本的な使い方

呼び出しの詳細は以下を参照するのが一番わかりやすいと思います。

```console
$ evans cli call -h
```

ワンライナーでやるなら

```console
$ echo ${INPUT_JSON} | evans -r cli call package.service.method
```

JSON 形式の入力をファイルにするなら

```console
$ evans -r cli call -f input.json package.service.method
```

さらに出力を JSON 方式にするなら(ディフォルトは `curl` 形式)

```console
$ evans -r cli call -f input.json -o json package.service.method
```

### gPRC サーバのポート番号の変更

ディフォルトでは 50051 番ポートを参照しているので、変更したい場合は `-p` オプションを使います。

例えば 8080 番に変更したい場合は以下になります。

```console
$ evans -r -p 8080 -f input.json package.service.method
```


### gPRC metadata のリクエストへの付与方法

グローバルオプションの `--header` に対して `key1=value1,key2=value2,...` の形式で渡します。例えば `client_name` に `api-test` という値を渡したい場合は

```console
$ evans -r --header client_name=api-test cli call -f input.json package.service.method
```

となります。

### gRPC metadata や status などの情報表示方法

`--enrich` を使います。

```console
$ evans -r cli call -f input.json -o json --enrich package.service.method
```
