# POLYGON MINESWEEPER — 24版UI準拠

この版は、アップロードされた最新版の「24 MINESWEEPER」(`index(3).html`, `style(3).css`, `script(6).js`)を基準に、**ルール・多角形の形状だけを変更**したものです。既存の24版のファイルは変更していません。

## 遊び方

`index.html` を開き、「五角形」または「六角形」を選んで難易度を選択してください。

| 形状 | 隣接 | 初級100 | 中級480 | 上級1000 |
| --- | --- | ---: | ---: | ---: |
| 五角形 | 辺＋頂点で接するマス（最大7） | 13 | 67 | 145 |
| 六角形 | 辺だけを共有するマス（最大6） | 12 | 62 | 135 |

いずれも初手には原則8マス以上の安全なマスが開き、数字0の連鎖も同じ隣接ルールを使います。盤面は横長で引き伸ばしません。

## 24版に揃えた点

- メニュー・HUD・ボタン・配色・数値描画・爆弾/旗/？のアイコンはアップロード版を基準に統一。
- 「開く」「⚑ 旗を立てる」「？ 仮置き」を独立した3つのモードに統一。
- PC：右クリックで旗、Shift＋右クリックで「？」。Rでリスタート、Escで難易度選択。
- クリア時にはアップロード版と同じ「クリア！おめでとう！！」オーバーレイを5秒表示し、クリックでも閉じます。失敗時は爆弾を表示してHUDのメッセージを更新します。
- SEは24版のWeb Audio API実装を使用。
- ズームのUIは24版の−／現在倍率／＋の3ボタンに統一。50〜200%の操作に加え、POLYGON版にあったスマホのピンチ操作・Ctrl＋ホイールによる倍率変更を保持。
- JavaScriptは`DIFFICULTIES`、`COLORS`、`game.board`、`game.opened`、`game.flags`、`game.questions`、`createGame`、`createInitialSafeCells`、`placeBombs`、`reveal`、`toggleFlag`、`toggleQuestion`、`finishGame`、`renderBoard`、`setZoom`などを24版に揃えています。
- **POLYGON固有処理**は`SHAPES`、`makePolygon`、`createCells`、`getNeighbors`、`isInsidePolygon`、`canvasCellFromEvent`などに分離。

## ファイル

- `index.html`：画面と説明
- `style.css`：24版CSSを基準にしたレイアウト
- `script.js`：24版JSを基準にしたゲームエンジン＋多角形の固有処理

サーバー・追加ライブラリは不要です。
