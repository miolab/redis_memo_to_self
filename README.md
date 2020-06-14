# Redis Memo

`Redis` 開発メモ。

- __Redis__
  - KVS (NoSQL)
  - インメモリ型データベース
  - 高速と言われる

- 参考
  * [公式](https://redis.io)
    * [commands](https://redis.io/commands)
  * [The Little Redis Book](https://www.openmymind.net/2012/1/23/The-Little-Redis-Book/)
    * [和訳](https://github.com/craftgear/the-little-redis-book)

#### 実行環境

| | バージョン |
| :-- | :-- |
| macOS | 10.14.6 |
| Redis | 6.0.5 |

#### セットアップ

- インストール

  ```terminal
  $ brew install redis
  ```

---

## サーバー/クライアントの起動/終了

- サーバー起動 __（`redis-server`）__

  ```redis
  $ redis-server
  12349:C 14 Jun 2020 08:13:01.687 # oO0OoO0OoO0Oo Redis is starting oO0OoO0OoO0Oo
  12349:C 14 Jun 2020 08:13:01.687 # Redis version=6.0.5, bits=64, commit=00000000, modified=0, pid=12349, just started
  12349:C 14 Jun 2020 08:13:01.687 # Warning: no config file specified, using the default config. In order to specify a config file use redis-server /path/to/redis.conf
  12349:M 14 Jun 2020 08:13:01.689 * Increased maximum number of open files to 10032 (it was originally set to 256).
                  _._                                                  
            _.-``__ ''-._                                             
        _.-``    `.  `_.  ''-._           Redis 6.0.5 (00000000/0) 64 bit
    .-`` .-```.  ```\/    _.,_ ''-._                                   
  (    '      ,       .-`  | `,    )     Running in standalone mode
  |`-._`-...-` __...-.``-._|'` _.-'|     Port: 6379
  |    `-._   `._    /     _.-'    |     PID: 12349
    `-._    `-._  `-./  _.-'    _.-'                                   
  |`-._`-._    `-.__.-'    _.-'_.-'|                                  
  |    `-._`-._        _.-'_.-'    |           http://redis.io        
    `-._    `-._`-.__.-'_.-'    _.-'                                   
  |`-._`-._    `-.__.-'    _.-'_.-'|                                  
  |    `-._`-._        _.-'_.-'    |                                  
    `-._    `-._`-.__.-'_.-'    _.-'                                   
        `-._    `-.__.-'    _.-'                                       
            `-._        _.-'                                           
                `-.__.-'                                               

  12349:M 14 Jun 2020 08:13:01.692 # Server initialized
  12349:M 14 Jun 2020 08:13:01.692 * Ready to accept connections
  ```

- クライアント

  （サーバーと別ターミナルを起動）

  | | コマンド |
  | :-- | :-- |
  | クライアント起動 | `$ redis-cli` |
  | クライアント終了 | `> exit` |
  | __サーバー終了__ | `> shutdown` |

  ```terminal
  $ redis-cli
  127.0.0.1:6379> exit

  $ redis-cli
  127.0.0.1:6379> shutdown
  not connected>
  ```

  （サーバーターミナル側の状態）

  ```
  .
  .
  12349:M 14 Jun 2020 08:13:01.692 * Ready to accept connections
  12349:M 14 Jun 2020 08:30:52.635 # User requested shutdown...
  12349:M 14 Jun 2020 08:30:52.635 * Saving the final RDB snapshot before exiting.
  12349:M 14 Jun 2020 08:30:52.637 * DB saved on disk
  12349:M 14 Jun 2020 08:30:52.637 # Redis is now ready to exit, bye bye...
  ```

---

## データベース接続 / データ永続化（save）

- データベース選択 __（`select NUM`）__

  ```redis
  $ redis-cli

  127.0.0.1:6379> select 1
  OK

  127.0.0.1:6379[1]> select 0
  OK
  ```

- データ保存 __（`bgsave`）__

  ```redis
  127.0.0.1:6379> bgsave
  Background saving started
  ```

---

# データ型

## 概要

__String__
- 個別要素

__List__
- 時系列データ（タイムライン等）

__Set__
- 集合。順不同で重複は許可されない。
- タグづけなど
- 共通の友達など（ソーシャルグラフ）

__Sorted Set__
- 重み付けのスコアをつける
- ランキングなど

__Hash__
- 連想配列

---

## String

```terminal
$ redis-cli

// データ登録（set KEY VALUE）
127.0.0.1:6379> set name im
OK

// データ読み出し（get KEY）
127.0.0.1:6379> get name
"im"

// 複数データ登録（mset KEY VALUE KEY VALUE ...）
127.0.0.1:6379> mset email im@miolab.hoge team miolab score 100
OK

// 複数データ読み出し（mget KEY VALUE KEY VALUE ...）
127.0.0.1:6379> mget name email team score
1) "im"
2) "im@miolab.hoge"
3) "miolab"
4) "100"

// インクリメント 数値1ずつ（incr KEY）
127.0.0.1:6379> incr score
(integer) 101

127.0.0.1:6379> get score
"101"

// インクリメント・デクリメント 数値指定（incrby/decrby KEY NUM）
127.0.0.1:6379> decrby score 5
(integer) 96

127.0.0.1:6379> get score
"96"

127.0.0.1:6379> incrby score 10
(integer) 106

127.0.0.1:6379> get score
"106"
```

### Key

- 登録されている `Key` を確認。

  ```terminal
  // ワイルドカード併用し確認
  127.0.0.1:6379> keys *
  1) "team"
  2) "email"
  3) "name"
  4) "score"

  127.0.0.1:6379> keys *i*
  1) "email"

  // exists
  127.0.0.1:6379> exists name
  (integer) 1

  127.0.0.1:6379> exists area
  (integer) 0

  // key名前変更（rename 変更前KEY 変更後KEY）
  127.0.0.1:6379> set basho kitakyu
  OK

  127.0.0.1:6379> rename basho area
  OK

  127.0.0.1:6379> keys *
  1) "area"
  2) "email"
  3) "team"
  4) "name"
  5) "score"

  // key削除（del KEY）
  127.0.0.1:6379> del area
  (integer) 1

  127.0.0.1:6379> keys *
  1) "email"
  2) "team"
  3) "name"
  4) "score"

  // keyの有効期限を「秒」指定で設ける（expire KEY SEC）
  127.0.0.1:6379> set age 18
  OK

  127.0.0.1:6379> expire age 10
  (integer) 1

  127.0.0.1:6379> get age
  "18"

  127.0.0.1:6379> get age
  (nil)

  // ランダムでkey抽出（randomkey）
  127.0.0.1:6379> randomkey
  "score"

  127.0.0.1:6379> randomkey
  "email"

  127.0.0.1:6379> randomkey
  "team"
  ```

## List

```terminal
// データを最後尾/頭に追加（rpush/lpush）
127.0.0.1:6379> rpush fruit orange
(integer) 1
127.0.0.1:6379> rpush fruit apple
(integer) 2
127.0.0.1:6379> rpush fruit banana
(integer) 3
127.0.0.1:6379> rpush fruit melon
(integer) 4
127.0.0.1:6379> rpush fruit grapevine
(integer) 5

// データを頭から参照（lrange）
127.0.0.1:6379> lrange fruit 0 5
1) "orange"
2) "apple"
3) "banana"
4) "melon"
5) "grapevine"

127.0.0.1:6379> lrange fruit 0 2
1) "orange"
2) "apple"
3) "banana"

127.0.0.1:6379> lrange fruit 0 -1
1) "orange"
2) "apple"
3) "banana"
4) "melon"
5) "grapevine"
// 0 -1で、最初の値から最後の値までを範囲指定

// index抽出 ※ 0始まり（lindex）
127.0.0.1:6379> lindex fruit 3
"melon"

// length的な個数カウント（llen）
127.0.0.1:6379> llen fruit
(integer) 5

// 最後尾/いちばん頭の値を削除（rpop/lpop）
127.0.0.1:6379> rpop fruit
"grapevine"

127.0.0.1:6379> lrange fruit 0 -1
1) "orange"
2) "apple"
3) "banana"
4) "melon"

127.0.0.1:6379> lpop fruit
"orange"

127.0.0.1:6379> lrange fruit 0 -1
1) "apple"
2) "banana"
3) "melon"

// listで見に行く範囲を固定（ltrim LIST 最初の位置 最後の位置）
127.0.0.1:6379> ltrim fruit 0 1
OK
127.0.0.1:6379> lrange fruit 0 -1
1) "apple"
2) "banana"
```

## Set

```terminal
// 追加（sadd）
127.0.0.1:6379> sadd set1 a
(integer) 1
127.0.0.1:6379> sadd set1 b
(integer) 1
127.0.0.1:6379> sadd set1 c
(integer) 1
127.0.0.1:6379> sadd set1 d
(integer) 1
127.0.0.1:6379> sadd set1 dd
(integer) 1

// 参照（smembers）
127.0.0.1:6379> smembers set1
1) "b"
2) "d"
3) "c"
4) "a"
5) "dd"

// 削除（srem SET PARAM）
127.0.0.1:6379> srem set1 dd
(integer) 1

127.0.0.1:6379> smembers set1
1) "d"
2) "c"
3) "a"
4) "b"

127.0.0.1:6379> sadd set2 c
(integer) 1
127.0.0.1:6379> sadd set2 d
(integer) 1
127.0.0.1:6379> sadd set2 e
(integer) 1
127.0.0.1:6379> sadd set2 f
(integer) 1

127.0.0.1:6379> smembers set2
1) "e"
2) "f"
3) "d"
4) "c"

// 和集合（sunion SET SET）
127.0.0.1:6379> sunion set1 set2
1) "b"
2) "d"
3) "c"
4) "a"
5) "e"
6) "f"

// 積集合（sinter SET SET）
127.0.0.1:6379> sinter set1 set2
1) "d"
2) "c"

// 差集合（sdiff SET SET）
127.0.0.1:6379> sdiff set1 set2
1) "b"
2) "a"

// 各集合した新Setを、新しいSetとして別名保存（~store）
127.0.0.1:6379> sdiffstore new_set set1 set2
(integer) 2

127.0.0.1:6379> smembers new_set
1) "b"
2) "a"
```

## Sorted Set

```terminal
// 追加（zadd Set 重み付け点 PARAM）
127.0.0.1:6379> zadd ranking 100 flower
(integer) 1
127.0.0.1:6379> zadd ranking 120 animal
(integer) 1
127.0.0.1:6379> zadd ranking 80 sport
(integer) 1

// 参照（zrange）
127.0.0.1:6379> zrange ranking 0 -1
1) "sport"
2) "flower"
3) "animal"

// 降順で参照（zrevrange）
127.0.0.1:6379> zrevrange ranking 0 -1
1) "animal"
2) "flower"
3) "sport"

// 重みの点数を参照（zrank SET PARAM）
127.0.0.1:6379> zrank ranking animal
(integer) 2

127.0.0.1:6379> zrevrank ranking animal
(integer) 0
```

## Hash

```terminal
// 追加（hset HASH KEY VALUE）
127.0.0.1:6379> hset animal name dog
(integer) 1

// 複数追加（hmset HASH KEY VALUE KEY VALUE ...）
127.0.0.1:6379> hmset animal sound wanwan point 100
OK

// 参照（hget HASH KEY）
127.0.0.1:6379> hget animal sound
"wanwan"

// 複数参照（hmget HASH KEY KEY ...）
127.0.0.1:6379> hmget animal name point
1) "dog"
2) "100"

// 個数カウント（hlen）
127.0.0.1:6379> hlen animal
(integer) 3

// 登録Keyを確認（hkeys）
127.0.0.1:6379> hkeys animal
1) "name"
2) "sound"
3) "point"

// 登録Valueを確認（hvals）
127.0.0.1:6379> hvals animal
1) "dog"
2) "wanwan"
3) "100"

// Key, Valueを全件確認（hgetall HASH）
127.0.0.1:6379> hgetall animal
1) "name"
2) "dog"
3) "sound"
4) "wanwan"
5) "point"
6) "100"
```

---

# ソート（Sort）

以下の各データ型で __ソート__ をおこなう。

- List
- Set
- Sorted Set

```terminal
// （例）List準備
127.0.0.1:6379> rpush points 22 10 55 66 5
(integer) 5

127.0.0.1:6379> lrange scores 0 -1
1) "10"
2) "22"
3) "33"
4) "44"
5) "55"

127.0.0.1:6379> lrange points 0 -1
1) "22"
2) "10"
3) "55"
4) "66"
5) "5"

// 型を確認
127.0.0.1:6379> type points
list

// 昇順ソート（sort ~）
127.0.0.1:6379> sort points
1) "5"
2) "10"
3) "22"
4) "55"
5) "66"

// 降順ソート（sort ~ desc）
127.0.0.1:6379> sort points desc
1) "66"
2) "55"
3) "22"
4) "10"
5) "5"

// 表示個数指定（... limit 開始位置 件数）
127.0.0.1:6379> sort points desc limit 0 3
1) "66"
2) "55"
3) "22"

// 要素が（数値でなく）文字列型の場合の処理（... alpha ...）
127.0.0.1:6379> type set1
set

127.0.0.1:6379> smembers set1
1) "d"
2) "c"
3) "a"
4) "b"

127.0.0.1:6379> sort set1
(error) ERR One or more scores can't be converted into double

127.0.0.1:6379> sort set1 alpha desc
1) "d"
2) "c"
3) "b"
4) "a"
```

---

# トランザクション（のような）処理

- multi
- 処理
- exec / discard

  - discard : 捨てる

### （例.1）ATMの待ち行列

```terminal
127.0.0.1:6379> multi
OK

127.0.0.1:6379> incr row_counter
QUEUED

127.0.0.1:6379> incr visitor
QUEUED

127.0.0.1:6379> exec
1) (integer) 1
2) (integer) 1
```

### （例.2）ライフゲージ残量

```terminal
127.0.0.1:6379> multi
OK

127.0.0.1:6379> decr energy
QUEUED

127.0.0.1:6379> decr mp
QUEUED

127.0.0.1:6379> discard
OK
127.0.0.1:6379> 
```

- Redis の場合、execまでの __処理途中でなんらかのトラブルで落ちた場合でも、途中の処理は実行されてしまう__ ことに注意

---

# データベース削除

- flushdb

  現在選択しているデータベースのKeyをすべて削除

  ```terminal
  127.0.0.1:6379> flushdb
  OK
  ```

- flushall

  すべてのデータベースのKeyをすべて削除（選択中データベース以外もすべて）

  ```terminal
  127.0.0.1:6379> flushall
  OK
  ```
