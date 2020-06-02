---
title: "Go 言語の gRPC コード生成 protoc plugin の2020-05の現状"
date: "2020-06-02T00:00:00+09:00"
draft: false
keyword: [ grpc, golang ]
tags: [ grpc, golang, beginning ]
categories: [ grpc, golang ]
summary: "Go 言語の gRPC コード生成 protoc plugin の2020-05の現状としては Go Protocol Buffer API v2 を使いたければ今までと状況かわらず deprecated ステータスの golang/protobuf v1.4.0 以上を使うしかない状態でした。"
authors: noissefnoc
menu: posts
---

## tl;dr

Go 言語の gRPC コード生成 protoc plugin の2020-05の現状としては Go Protocol Buffer API v2 を使いたければ今までと状況かわらず deprecated ステータスの golang/protobuf v1.4.0 以上を使うしかない状態でした。状況のサマリは以下

|レポジトリ名               |gPRC生成プラグイン提供の有無   |Go Protocol Buffer API V2対応|メンテナンス|
|---------------------------|-------------------------------|-----------------------------|------------|
|golang/protobuf            |あり(protoc-gen-goのプラグイン)|対応                         |移行中      |
|protocolbuffers/protobuf-go|なし                           |対応                         |アクティブ  |
|grpc/grpc-go               |あり(protoc-gen-go-grpc)       |非対応                       |アクティブ  |

grpc/grpc-goは直近でマージされていた(PRのコメントを読むと本リリースとはいってないようでしたが)ので期待していたのですが、golang/protoufのコードとも諸々開きがありましたし、生成コードにも欠損があったのでまだ使えない状態のようです。

## 経緯

Go 言語(というかGoogle?)側の Protocol Buffer のサポートは

* [protocolbuffers/protobuf-go: Go support for Google's protocol buffers](https://github.com/protocolbuffers/protobuf-go)

に移行のステータスで、`v1.20.0` (上記レポジトリのスタートバージョン)以降は gPRC コード生成の protoc plugin を同梱していません。

移行中は旧来の

* [golang/protobuf: Go support for Google's protocol buffers](https://github.com/golang/protobuf)

が後方互換保持時のために残されており、 gRPC コード生成の protoc plugin も同梱されており、 `v1.4.0` 以降は Go Protocol Buffer Message V2 にも対応しています。

Go 言語側の要望としては gRPC のコード生成は gRPC 側に任せたいということで

*  [google.golang.org/grpc: move protoc-gen-go-grpc into grpc module · Issue #903 · golang/protobuf](https://github.com/golang/protobuf/issues/903)

にチケットが起票され、gPRC 側では

* [cmd/protoc-gen-go-grpc: add code generator by neild · Pull Request #3453 · grpc/grpc-go](https://github.com/grpc/grpc-go/pull/3453)

で作業が進められていました。

## 直近の動き

gRPC 側のコードが先日ようやくマージされ、Go言語側の委託チケットもクローズされていたのでインストールして試してみたのですが

```console
# インストール
$ go get google.golang.org/grpc/cmd/protoc-gen-go-grpc

# コード生成
$ protoc -I . -I /usr/local/opt/protobuf/include --go-grpc_out=. helloworld.proto

# golang/protoufで生成したものと比較
$ diff -Naru helloworld_grpc.pb.go.old helloworld_grpc.bp.go
```

golang/protobuf から生成したコードに比べてコードが明らかに少なく、protoc plugin のコードも見てみたのですが、かなり開きがありました。

まだこちらを使うのは厳しいようです。しばらくは移行中のステータスの golang/protobuf を使うのが正解なようです。
