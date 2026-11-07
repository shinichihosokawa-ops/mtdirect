# mtdirect.jp — サイト作業の説明書（Claude Code用）

このリポジトリは MTDirect（日本からマルタへ中古車・工芸品・食品を個人輸入で直送する事業）の公開サイト。**Claude Code の担当はサイトの更新のみ**。価格計算・企画・スプレッドシートは ChatGPT 側で行い、ここには計算済みの値が渡される。

## 引き継ぎ資料（ChatGPTと共有）
- Google Drive：https://drive.google.com/drive/u/0/folders/1JdXEq6iBraAnzr0tbtJM1GKHbfDk9yi3
- 01_MTDirect_事業前提書.md／02_スプレッドシート仕様書.md／03_ChatGPTへの指示文.md／mtdirect_在庫価格マスター.xlsx
- サイト作業の前に、ここで最新の価格・掲載車を確認する。Driveの既存ファイルは上書きできないため、更新するときは日付付きの新しいファイルとして作る（例：01_MTDirect_事業前提書_2026-11-15.md）。最新は日付が一番新しいもの。

## 公開
- GitHub Pages（ドメイン mtdirect.jp、CNAMEあり）。`main` に push すると1〜2分で反映。
- コミットの作者は "Shin Hosokawa" <shinichi.hosokawa@gmail.com>。
- 同じ内容を claude.ai のアーティファクト（https://claude.ai/artifact/TmD9DN8MeduyJ7qt92K4Pw）でも公開している場合がある。アーティファクト用のソースは index.html から `<!doctype>`〜`<head>` 冒頭6行・viewport meta・`</head><body>`・`</body></html>` を除いた形。

## 構成
- `index.html`：1ファイルのSPA。ハッシュで画面切替（#home / #cars / #food / #crafts / #other）。CSS・JSはすべてこのファイル内。
- `img/brand/`：ロゴ（logo.png、「MTDirect.jp 日本からマルタに直送」透過PNG）、ファビコン（icon-32/192/512、apple-touch-icon）。`favicon.ico` はルート。
- `img/stock/{カーセンサーの物件ID}/01.jpg〜10.jpg`：車の写真。
- `img/video-poster.jpg`、`video/mtdirect-how-it-works.mp4`：車ページ上部の10秒の説明動画（クリックでその場で再生）。
- `img/crafts.jpg`, `img/garage.jpg`：トップ・各ページのバナー写真。

## 車両データ
`index.html` 内の `const vehicles = [...]`（JSON配列）。1台の項目：
- `id`, `dir`（写真フォルダ＝カーセンサーのAU番号）, `name`, `code`（グレード）, `year`, `km`, `grade`（短い説明）, `tag`（バッジ）
- 価格（€、すべてスプレッドシートの値をそのまま使う）：`sale`（当社の売値＝CIF×1.15＋€200）, `duty`, `vat`, `reg`（登録税）, `port`（400）, `local`（300）, `road`（乗り出し価格）
- `photos`：[["01","Front three-quarter"], ...], `highlights`, `equip`, `facts`
- `status`："available" 等, `real`: true
- `calc()` は `duty+vat+reg+port+local` をマルタ側費用として合計。`road` はスプレッドシートの値を優先する。

## 車の追加・更新の手順
1. ChatGPT から受け取った一覧（車種・グレード・年式・走行・sale/vat/reg/road・カーセンサーURL・装備・アピールポイント）を確認。
2. 写真：カーセンサーの画像はシェルからもCORS経由でも取れないため、Claude in Chrome で物件ページを開き、ギャラリーをスクリーンショットして切り抜き、`img/stock/{AU番号}/01..10.jpg`（約490×367px）に保存。
3. `vehicles` に追加・更新。英語で書く（サイトは英語。日本語は小見出しのみ）。
4. Playwright（`require('playwright')`、インストール済み）でPC幅1280とスマホ幅390のスクリーンショットを撮って確認してから push。

## デザインの決まり
- 色：紺 `--indigo:#3B465D`、赤 `--shu:#C41A2C`（ロゴの色）。背景は生成り。
- 見出しはセリフ体、本文はサンセリフ。総額（乗り出し価格）を必ず表示し、「Malta port price」と「Paid by you in Malta」の内訳を分ける。
- 支払いの説明：代金はマルタの弁護士が預かり、車を受け取ってから売り手（Hosokawa Pte. Ltd.）に1回で支払い。供給元は道央自動車株式会社。
- 外部ライブラリは使わない。画像は相対パス。
