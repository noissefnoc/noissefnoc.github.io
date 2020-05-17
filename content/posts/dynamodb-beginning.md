---
title: "AWS DynamoDB に入門した"
date: "2020-05-02T00:00:00+09:00"
draft: false
keyword: [ aws, dynamodb ]
tags: [ aws, dynamodb, beginning ]
categories: [ aws ]
summary: "AWS DynamoDB を触ったことがなかったのですが、使うことになったので調べることにしました"
authors: noissefnoc
menu: posts
---

## tl;dr

ただの調べたことメモなので、サマリはありません。

## AWS CLI

### v1

* 検証 `aws-cli` バージョン：`1.8.0`
* 検証環境：dockerのdynamodb公式イメージ
    * エンドポイントはディフォルトの `http://localhost:8000` とする


#### テーブル定義をJSONファイルで投入

項目定義にPartition/Sort/Indexに使わないキーを含めているとエラーになるので注意。パフォーマンス設定値はどの値が適切なのかよく分かっていない。

```console
$ aws dynamodb create-table --endpoint-url http://localhost:8000 --cli-input-json file://PATH/TO/JSON/table_name.json
```

#### テーブル定義のdump

```console
$ aws dynamodb describe-table --endpoint-url http://localhost:8000 --table-name TABLE_NAME > ./table_name.json
```

ただし、自分では定義しないはずのフィールドも含まれるので、その点は注意。

#### テーブル削除

```console
$ aws dynamodb delete-table --endpoint-url http://localhost:8000 --table-name TABLE_NAME
```

#### データ投入

文字列以外も全てAttribute上は文字列で作成することに注意

```console
$ aws dynamodb batch-write-item --endpoint-url http://localhost:8000 --request-items file://PATH/TO/JSON/table_name.json
```


## 参考

* AWS CLI
    * 公式リファレンス
        * [dynamodb — AWS CLI 1.18.51 Command Reference](https://docs.aws.amazon.com/cli/latest/reference/dynamodb/index.html): v1のリファレンス
        * [dynamodb — AWS CLI 2.0.10 Command Reference](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/dynamodb/index.html): v2のリファレンス
    * 参考記事
        * [AWS CLI v2でDynamoDBのハイレベルコマンドを試してみた #reinvent](https://dev.classmethod.jp/articles/aws-cli-v2-dynamodb-high-level-commnad/): v2 の使い方
        * [AWS CLIコマンドのメモ](https://www.magata.net/memo/index.php?AWS%20CLI%A5%B3%A5%DE%A5%F3%A5%C9%A4%CE%A5%E1%A5%E2#e7b0ce49): v1の使い方
* DynamoDB NoSQL Workbench
    * [DynamoDB の NoSQL Workbench の使用 - Amazon DynamoDB](https://docs.aws.amazon.com/ja_jp/amazondynamodb/latest/developerguide/Using.Workbench.html)
* DynamoDB JavaScript console
    * [JavaScript shell console で DynamoDB Local を手軽に試す！](https://dev.classmethod.jp/articles/dynamodb-local-shell-console/)
* AWS SDK GO GoDoc
    * [dynamodb - Amazon Web Services Go SDK](https://docs.aws.amazon.com/sdk-for-go/api/service/dynamodb/): aws-sdk-go v1のリファレンス
* 概要解説
    * [DynamoDB全くわからない、から、ちょっとわかるようになるまでの道しるべ](https://dev.classmethod.jp/articles/dynamodb-chottowakaru/)
* 設計Tips系
    * [DynamoDBを使いこなして精神的安定を手に入れた](https://qiita.com/walkers/items/9b39d752cc5b8df7b4b5): Ruby gems の作者による解説
* 実装例
    * [DynamoDBローカルをDockerコンテナとして動かす](https://qiita.com/tamo_breaker/items/7a2344032bc7e736b071): Node.jsでの例