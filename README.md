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
