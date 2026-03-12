# Changes from upstream (genotrance/px v0.10.2)

## tunnel_lifetime — CONNECTトンネル寿命制限

### 背景

企業NTLMプロキシ環境では、アップストリームプロキシがCONNECTトンネルを約30分で強制切断する場合がある。
従来のpxはこの切断を検知・リカバリできず、GitHub Copilot等のクライアントが `transient API error` となっていた。
回避策としてpxの再起動が必要だったが、このパッチにより不要になる。

### 変更内容

#### `px/config.py`

- `DEFAULTS` に `"tunnel_lifetime": "1500"` を追加
- `State` クラスに `tunnel_lifetime = 1500` 属性を追加
- `State.__init__` のコールバック辞書に `"tunnel_lifetime": self.set_tunnel_lifetime` を追加
- `set_tunnel_lifetime()` メソッドを追加（`set_proxyreload` の直後）
- `cfg_init()` の `[settings]` 整数パース対象リストに `"tunnel_lifetime"` を追加

#### `px/handler.py`

- `import threading` を追加
- `do_curl()` 内のCONNECT処理で `STATE.mcurl.select()` 呼び出しを `threading.Timer` でラップ
  - トンネル確立時にタイマーを開始
  - `tunnel_lifetime` 秒経過後、クライアントソケットを `shutdown(SHUT_RDWR)` して `mcurl.select()` を終了させる
  - `mcurl.select()` が正常終了（idle タイムアウト・リモートclose）した場合はタイマーをキャンセル

### 設定方法

`px.ini` の `[settings]` セクションに追加：

```ini
[settings]
tunnel_lifetime = 1500  # 秒単位（デフォルト: 1500 = 25分）
```

| 値 | 動作 |
|---|---|
| `1500`（デフォルト） | 25分でトンネルを能動的に閉じる |
| `0` | 寿命制限無効（従来動作） |
| 任意の正整数 | 指定秒数でトンネルを閉じる |

### 動作フロー

```
CONNECTトンネル確立
  └─ tunnel_lifetime > 0 の場合
      ├─ threading.Timer(tunnel_lifetime, _expire_tunnel) 起動
      ├─ mcurl.select() でデータ転送ループ（ブロッキング）
      │
      ├─ [正常終了] idle タイムアウト or リモートclose
      │      └─ finally: timer.cancel()
      │
      └─ [寿命超過] タイマー発火
             ├─ dprint("Tunnel lifetime exceeded (N sec), closing for re-auth")
             ├─ conn.shutdown(SHUT_RDWR) → mcurl.select() がクライアント切断検知
             └─ close_connection = True → クライアントが自動再接続・NTLM再認証
```

### 注意事項

- `pymcurl` パッケージは変更していない（アップデートに対して安全）
- `idle` 設定（データ転送のないアイドルタイムアウト）とは独立した機能
- `tunnel_lifetime` はデータ転送の有無にかかわらず、トンネル作成からの絶対時間制限
