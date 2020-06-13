# redis_memo_to_self

## 参考

* [公式](https://redis.io)
  * [commands](https://redis.io/commands)

* [The Little Redis Book](The Little Redis Book)
  * [和訳](https://github.com/craftgear/the-little-redis-book)

## セットアップ

```
$ brew install redis
```

## データ型

String
- 個別要素

List
- 時系列データ（タイムライン等）

Set
- 集合。重複は許可されない。
- タグづけなど
- 共通の友達など（ソーシャルグラフ）

Sorted Set
- 重み付けのスコアをつける
- ランキングなど

Hash
- 連想配列

## サーバー/クライアントの起動/終了