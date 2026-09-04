# 意思決定ログ — media-shrink

<!--
フォーマット
## YYYY-MM-DD 決めたこと（見出しは「何を決めたか」）
- **背景**: なぜ決める必要があったか
- **決定**: どうすることにしたか
- **理由**: なぜそれを選んだか / 他の案を採らなかった理由
- **確度**: 事実 / 推測 / 確定仕様
-->

## 2026-09-04 Cloudflare Pages はダイレクトアップロードで開始する

- **背景**: 社内の他ツール（cxreport / channeltalk-auto-tag / kickoff など）を Cloudflare に
  集約しているため、この2ツールも同じ場所に置きたい、という依頼。
- **決定**: `wrangler pages deploy` によるダイレクトアップロードで公開した。
  GitHub リポジトリとの連携（push で自動デプロイ）は設定していない。
- **理由**: 社内の既存 Pages プロジェクトはすべてダイレクトアップロード（Git Provider: No）で揃っている。
  GitHub 連携は Cloudflare ダッシュボード上で GitHub の認可が必要で、CLI からは設定できない。
- **確度**: 事実（`wrangler pages project list` で全プロジェクトが Git Provider: No）。

## 2026-09-04 README の「直しかた」は現状と食い違うので書き換えた

- **背景**: README には「GitHubで index.html を編集 → 1分で GitHub Pages に反映」とだけ書いてあった。
  Cloudflare を Git 連携していないので、この手順だけでは Cloudflare 側が古いまま取り残される。
- **決定**: 公開URLを2つ併記し、GitHub で直したあと Cloudflare に再アップロードが要る旨を README に明記した。
- **確度**: 確定仕様（今のデプロイ構成に対する記述）。

## 2026-09-04 公開ディレクトリに README.md を含めない

- **決定**: Cloudflare にアップロードするのは `index.html` / `mediabunny.min.mjs` /
  `LICENSE-mediabunny.txt` のみ。
- **理由**: `LICENSE-mediabunny.txt` は Mediabunny（MPL-2.0）のライセンス表示として同梱が必要。
  README は公開ページに置く必要がない。リポジトリ自体が public なので秘匿目的ではなく、
  公開物を必要なものだけにするため。
- **確度**: 事実（MPL-2.0 はライセンス全文の添付を求める）。

## 2026-09-04 Cloudflare純正のGit連携ではなく GitHub Actions で自動デプロイする

- **背景**: GitHubで `index.html` を直す運用なので、Cloudflare が自動で追従しないと
  「直したのに反映されない」が起きる。
- **決定**: GitHub Actions（`cloudflare/wrangler-action`）から `wrangler pages deploy` を実行する。
- **理由**: Cloudflare の仕様で、ダイレクトアップロードで作ったプロジェクトは後から Git 連携に変更できない
  （[公式ドキュメント](https://developers.cloudflare.com/pages/get-started/direct-upload/)に明記）。
  純正連携にするならプロジェクトを作り直す必要があり、domain-check は自動採番のURLが変わってしまう。
  Actions 方式なら今のプロジェクトとURLのまま自動化できる。
- **確度**: 事実。

## 2026-09-04 旧URL（GitHub Pages）は止めずに案内ページに差し替える

- **背景**: 旧URLは既に顧客・社内に案内済み。止めるとその人たちがエラー画面に当たる。
- **決定**: GitHub Pages はオンのまま、公開元を `gh-pages` ブランチに変更し、
  そこには新URLへ転送する案内ページだけを置いた。`main` はツール本体のまま。
- **理由**: 静的ホスティングなのでサーバー側の301転送は使えず、meta refresh + JS が唯一の手段。
  `main` の `index.html` を差し替える案は、ツール本体を壊すので採らなかった。
  ブランチを分ければ、編集する人の手順（`main` を直す）は今まで通りで済む。
- **確度**: 確定仕様。
- **副作用**: ブラウザのブックマークは自動では書き換わらないため、案内ページに
  「ブックマークを登録し直してください」を表示している。
