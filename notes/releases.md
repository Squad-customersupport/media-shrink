# デプロイ履歴 — media-shrink

デプロイした内容・日付・環境を記録する（デプロイとログ更新は1セット）。

## 2026-09-04 Cloudflare Pages へ初回デプロイ

- **環境**: Cloudflare Pages `media-shrink`（Squad社内アカウント `ce95fa768c0c138ecccca0a902fd2989`）
- **URL**: https://media-shrink.pages.dev
- **方式**: ダイレクトアップロード（`wrangler pages deploy`）。GitHub連携なし
- **アップロード対象**: `index.html` / `mediabunny.min.mjs` / `LICENSE-mediabunny.txt` の3ファイル。
  `README.md` は公開ディレクトリから除外
- **ビルド**: なし（静的ファイルのみ）

### 内容
GitHub Pages（https://squad-customersupport.github.io/media-shrink/）で公開していたものを、
中身を変更せずそのまま Cloudflare Pages にも載せた。GitHub Pages 側も稼働したまま残っている。

### 確認したこと
- トップページ 200 / `text/html; charset=utf-8`
- `mediabunny.min.mjs` 200 / `application/javascript`
  （`index.html` は静的 `import` でこのファイルを読むため、MIMEタイプが違うとページ全体が動かない）
- 実ブラウザで読み込み、コンソールエラーなし＝ESモジュールの解決に成功している

### 未実施
GitHub と Cloudflare Pages は連携していない。GitHub で `index.html` を直しても
Cloudflare 側は古いまま。反映には再アップロードが必要（`notes/decision-log.md` 参照）。

## 2026-09-04 ② GitHub push で自動デプロイされるようにした／旧URLを案内ページに

### 内容
- `.github/workflows/deploy.yml` を追加。`main` の公開ファイルが更新されると
  GitHub Actions が `cloudflare/wrangler-action` で Cloudflare Pages へアップロードする。
  公開するのは index.html / mediabunny.min.mjs / LICENSE-mediabunny.txt のみ（README・notes は載せない）。
- GitHub Pages の公開元を `main` から `gh-pages` ブランチへ変更。
  `gh-pages` には https://media-shrink.pages.dev への案内ページ（meta refresh + JS）だけを置いた。
  旧URLを案内済みの人が新URLにたどり着けるようにするため、公開自体は止めていない。

### 環境・設定
- 認証は組織シークレット `CLOUDFLARE_API_TOKEN` / `CLOUDFLARE_ACCOUNT_ID`
  （Squad-customersupport の Organization secrets、対象リポジトリは media-shrink と domain-check のみ）。
- Cloudflare のトークンは「アカウント > Cloudflare Pages > 編集」の権限だけを持つカスタムトークン。

### 確認したこと
- `main` の `index.html` にマーカーを入れて push → Actions 成功 → https://media-shrink.pages.dev にマーカーが出ることを確認。
  マーカーを消して push → 再び消えることも確認（両方向で反映される）。
- 旧URL（https://squad-customersupport.github.io/media-shrink/）が案内ページを返し、
  転送先が https://media-shrink.pages.dev になっていることを確認。

### つまずいた点（次回のため）
シークレット登録で3回失敗した。順に「値に `Secret: ` が混入」「トークン欄にアカウントIDを貼っていた」
「Cloudflareのコピーボタンが効かず、直前のクリップボード（アカウントID）が貼られていた」。
GitHub側の値は読み出せないので、`${#TOKEN}` の文字数と `/user/tokens/verify` の応答を出す
一時ワークフローを置いて切り分けた（原因判明後に削除済み）。
