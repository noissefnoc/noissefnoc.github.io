---
title: "gRPC に入門した"
date: "2020-04-04T00:00:00+09:00"
draft: false
keyword: [ grpc, golang ]
tags: [ grpc, golang, beginning ]
categories: [ grpc, golang ]
summary: "これまでバッチ処理系ばかりやってきたのですが、APIも作ることになり、gRPC に入門することになりました"
authors: noissefnoc
menu: posts
---

## tl;dr

ただの調べたことメモなので、サマリはありません。

## 参考

* gRPC 公式
    * [gRPC – A high-performance, open source universal RPC framework](https://grpc.io/)
* Protocol Buffers
    * [Protocol Buffers | Google Developers](https://developers.google.com/protocol-buffers)
    * [golang/protobuf: Go support for Google's protocol buffers](https://github.com/golang/protobuf) ※ 現行。 `v1.4.0` で一部新APIにも対応
    * [protocolbuffers/protobuf-go: Go support for Google's protocol buffers](https://github.com/protocolbuffers/protobuf-go) ※将来的に移行する方で、新API対応。 `go get` するときのパスは `google.golang.org/protobuf` 。 **`protoc-gen-go` で gRPC のコード生成をサポートしなくなった**
        * 新APIの話は例えば [A new Go API for Protocol Buffers - The Go Blog](https://blog.golang.org/protobuf-apiv2)
        * 将来的に [grpc/grpc-go: The Go language implementation of gRPC. HTTP/2 based RPC](https://github.com/grpc/grpc-go) で生成を求められている
        * `grpc/grpc-go` 側では [google.golang.org/grpc: move protoc-gen-go-grpc into grpc module · Issue #903 · golang/protobuf](https://github.com/golang/protobuf/issues/903)がメインの issue で PR として [cmd/protoc-gen-go-grpc: add code generator by neild · Pull Request #3453 · grpc/grpc-go](https://github.com/grpc/grpc-go/pull/3453) が上がっているが、現在進行が止まっている (4月末もまだ)
* gPRC Go
    * [grpc/grpc-go: The Go language implementation of gRPC. HTTP/2 based RPC](https://github.com/grpc/grpc-go) ※ 近いうちに `protoc-gen-grpc` の提供が必要だが、まだ未対応
        * `Documentation` ディレクトリに FAQ の回答みたいなドキュメントが置いてある
        * `examples` にサンプルコードが書いてある
            * `features` に gRPC の諸々を実現するときのコード
            * `helloworld` に サンプルコード(モックコード付き)
            * `route_guide` に server/client で各種呼び出し形式する場合のサンプルコード
        * `test` にテストや、テスト用リソース
            * `bufconn` オンメモリでサーバ側の処理をする。単体テスト用
        * `health` に gRPC ヘルスチェック用の資材
    * [grpc-ecosystem/go-grpc-middleware: Golang gRPC Middlewares: interceptor chaining, auth, logging, retries and more.](https://github.com/grpc-ecosystem/go-grpc-middleware) gRPC のミドルウェア
    * [grpc-ecosystem/grpc-gateway: gRPC to JSON proxy generator following the gRPC HTTP spec](https://github.com/grpc-ecosystem/grpc-gateway) ※ gRPC を REST API にマッピングするプロキシ
    * [grpc-ecosystem/awesome-grpc: A curated list of useful resources for gRPC](https://github.com/grpc-ecosystem/awesome-grpc) ※ いろいろなところである awesome シリーズの gRPC 版
        * [fullstorydev/grpcui: An interactive web UI for gRPC, along the lines of postman](https://github.com/fullstorydev/grpcui) GUI リクエスト送信ツール
        * [apssouza22/grpc-server-go: A GRPC production ready example](https://github.com/apssouza22/grpc-server-go) もろもろ入れ込んだサンプル
    * Tips
        * [Can GracefulStop have a timeout? · Issue #2448 · grpc/grpc-go](https://github.com/grpc/grpc-go/issues/2448)
        * [go - Testing a gRPC service - Stack Overflow](https://stackoverflow.com/questions/42102496/testing-a-grpc-service) 古いけどテストの方法まとまってる
        * (protoファイルからいい感じのAPI仕様書を作りたい - Qiita)[https://qiita.com/ayatothos/items/ea05a70856d8812c644e]
            * [pseudomuto/protoc-gen-doc: Documentation generator plugin for Google Protocol Buffers](https://github.com/pseudomuto/protoc-gen-doc) の紹介とカスタムテンプレート
    * Reflection を使おうと思ったら今のところ Google 公式のより [jhump/protoreflect: Reflection (Rich Descriptors) for Go Protocol Buffers](https://github.com/jhump/protoreflect) を使った方がよさそう。Google の [API Improvement Proposals](https://aip.dev/) の linter ([googleapis/api-linter: A linter for APIs defined in protocol buffers.](https://github.com/googleapis/api-linter) も使ってない上にメンテナのGooglerが[Extracting custom option info from Protobuf message · Issue #794 · golang/protobuf](https://github.com/golang/protobuf/issues/794#issuecomment-519353512) で機能不十分や同じようなパッケージ何個も使わないといけないことに対して「えー」って言ってる
