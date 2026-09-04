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
