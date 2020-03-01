---
title: "GJSON を使ってみた"
date: "2020-02-29T00:00:00+09:00"
draft: false
keyword: [ golang, library ]
tags: [ golang, library ]
categories: [ golang, library ]
summary: "Golang の JSON ライブラリ GJSON を使ってみた"
authors: noissefnoc
menu: posts
---

Golang の あるライブラリにコントリビュートしようとしたときに、JSON のハンドリングを [tidwall/gjson](https://github.com/tidwall/gjson) でやっていたので使い方のメモ。

GJSON のコンセプトは `README.md`

```
GJSON is a Go package that provides a fast and simple way to get values from a json document. It has features such as one line retrieval, dot notation paths, iteration, and parsing json lines.
```

とあるように JSON から簡単に値を取得することを目的にしている。ので、APIとしても既存の JSON を修正したり、 JSON を新しく作成する機能はない。

通常 Golang で JSON を扱うときは構造体を定義して `json.Marshal` ないしは `json.Unmarshal` して使うが、GJSON の場合は JSON 文字列に対して

```go
gjson.Get(jsonStr, "pathStr")
```

の構文で構造体を介さずに値を処理できる。


その分値の取得に対して DSL を提供しており、複雑な構造からも値を取得できるようになっている。

* [GJSON Path Syntax](https://github.com/tidwall/gjson/blob/master/SYNTAX.md)

自分で JSON と Path Syntax を入力できる対話的な Playground が提供されているので、まずはそこで確認するのがいいかもしれません。

* [GJSON Playground](https://gjson.dev/)

サンプルだと

```json
{
  "name": {"first": "Tom", "last": "Anderson"},
  "age":37,
  "children": ["Sara","Alex","Jack"],
  "fav.movie": "Deer Hunter",
  "friends": [
    {"first": "Dale", "last": "Murphy", "age": 44, "nets": ["ig", "fb", "tw"]},
    {"first": "Roger", "last": "Craig", "age": 68, "nets": ["fb", "tw"]},
    {"first": "Jane", "last": "Murphy", "age": 47, "nets": ["ig", "tw"]}
  ]
}
```

にような JSON が使われていて、順に階層をたどるのは特に説明が要らないと思うので、配列要素の取得の Path について。

上記例だと `friends` の配列要素へのアクセスがそれにあたる。

```
// index access
friends.0.nets >> ["ig", "fb", "tw"]

// 各要素を全部取得。#単体だと配列の個数になる
friends.#.nets >> [["ig", "fb", "tw"], ["fb", "tw"], ["ig", "tw"]]

// 他の要素でフィルタして該当要素だけを取得
// 一致だけでなく、大小関係やパターンマッチ他の下部要素での一致なども可能
friends.#(first=="Dale").nets >> ["ig", "fb", "tw"]
```

また、ネストした値を Golang 側で処理したい場合はも戻り値の `result.Array()` を `range` でループさせるか `result.ForEach` でイテレーターを回す。

```go
result := gjson.Get(json, "friends")
result.ForEach(func(key, value gjson.Result) bool {
    fmt.Println(value.String())
    return true // 戻り値は次のイテレーションに進むかどうか
})
```

ひとまずはこんなところか。