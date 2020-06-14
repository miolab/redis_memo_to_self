# Redis Memo

`Redis` 開発メモ。

* [公式](https://redis.io)
  * [commands](https://redis.io/commands)

* [The Little Redis Book](https://www.openmymind.net/2012/1/23/The-Little-Redis-Book/)
  * [和訳](https://github.com/craftgear/the-little-redis-book)

### 実行環境

| | バージョン | |
| :-- | :-- | :-- |
| macOS | 10.14.6 |
| Redis | 6.0.5 |

---

## セットアップ

- インストール

  ```terminal
  $ brew install redis
  ```

---

## サーバー/クライアントの起動/終了

- サーバー起動 `redis-server`

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

- サーバー終了

  後述

- クライアント

  （別ターミナルで）

  | | コマンド |
  | :-- | :-- |
  | クライアント起動 | `$ redis-cli` |
  | クライアント終了 | `> exit` |
  | サーバー終了 | `> shutdown` |

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

```redis
$ redis-cli

// データベース選択（select NUM）
127.0.0.1:6379> select 1
OK

127.0.0.1:6379[1]> select 0
OK

// データ保存（bgsave）
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
- 集合。重複は許可されない。
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

// 複数データ登録（set KEY VALUE KEY VALUE ...）
127.0.0.1:6379> mset email im@miolab.hoge team miolab score 100
OK

// 複数データ読み出し（get KEY VALUE KEY VALUE ...）
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
