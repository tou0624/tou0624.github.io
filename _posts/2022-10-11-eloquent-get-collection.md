---
layout: post
title:  "Eloquentのget"
---

# バージョン
* Laravel:8.x（Eloquentは左に準ずる）
* PHP:8.0.x

# 前提情報
LaravelにてSQLを利用したDBアクセスを実施する際、Laravel標準のクエリビルダを利用する or Eloquent(ORマッパー)を利用する。EloquentはLaravel標準のクエリビルダを拡張した様なイメージで捉えると混乱しない。  

Eloquentでクエリを組むとき、Laravelのクエリビルダの関数はすべて利用可能。  
※Laravelのクエリビルダについては [https://readouble.com/laravel/8.x/ja/queries.html](https://readouble.com/laravel/8.x/ja/queries.html) を参照。

# getで何が出来るのか
クエリの結果を取得する。`DB::table('hoge')->get();` といった具合にデータ取得可能。  
get自体に、クエリの制約(where句など)を加える力はない。  
それまで組み立てられたデータを取得するためのトリガーのようなもの。  

ちなみに:1件だけ検索すればいい場合は`first()`を利用可能。limit 1をつけてSELECTするイメージ。クエリビルダの機能。
ちなみに:全件検索するときは`all()`を利用可能。Eloquentの機能。

# 戻り値について
Collectionが返却されるが、クエリビルダとEloquentでは、戻ってくるCollectionが異なる。  
下記記載があるので、いずれのコレクションもforeachにてイテレート可能。
> LaravelのコレクションはすべてPHPのiterableなインターフェイスを実装しているため、コレクションを配列のようにループ処理できます。

## `Illuminate\Support\Collection`
* クエリビルダのget関数 の戻り
  * EloquentのModelはLaravelのクエリビルダであるため
  * 参考：[https://readouble.com/laravel/8.x/ja/eloquent.html#collections](https://readouble.com/laravel/8.x/ja/eloquent.html#collections)
* 利用可能な関数
  * [https://readouble.com/laravel/8.x/ja/collections.html#available-methods](https://readouble.com/laravel/8.x/ja/collections.html#available-methods)

`Illuminate\Database\Eloquent\Collection`が戻る場合
* Eloquentのget関数 の戻り。`php artisan make:model`にて作成されたモデルはこちら
    * 参考：[https://readouble.com/laravel/8.x/ja/collections.html](https://readouble.com/laravel/8.x/ja/collections.html)

* 利用可能な関数
  * `Illuminate\Support\Collection`でできること
  * [https://readouble.com/laravel/8.x/ja/eloquent-collections.html#available-methods](https://readouble.com/laravel/8.x/ja/eloquent-collections.html#available-methods)