# Green Holiday — Obsidian Theme

Obsidian community theme. Lime green tinted, dot grid + gradient background, dark/light parity.

## File Structure

- `manifest.json` — version, author（`id` / `description` は plugin 専用フィールドなので書かない）
- `theme.css` — 単一ファイルですべてのスタイルを管理
- `README.md`, `screenshot*.png`, `LICENSE`

## CSS Rules

- `.theme-dark` と `.theme-light` のブロックは **ファイル内に各1つだけ**
  新しい CSS 変数を追加する時は必ずファイル先頭の該当ブロックに追記する
- `!important` は背景レイヤリング（グラデーション・透明化）のケースのみ使用
- ハードコードの色より `var(--xxx)` の再利用を優先

## Dev Workflow

`~/brain/.obsidian/themes/Green Holiday Dev/` に Dev テーマを用意済み。
`theme.css` を編集すると PostToolUse フックが自動でコピーする（Obsidian がホットリロードする）。

確認手順：
1. Obsidian で `Green Holiday Dev` テーマを選択した状態にしておく
2. `theme.css` を編集する → 自動で反映される

## Release Checklist

1. `manifest.json` の `version` を更新
2. `theme.css` 冒頭コメントの `Version:` を更新
3. コミット: `git add manifest.json theme.css && git commit -m "x.x.x: 変更概要"`
4. タグは **`v` を付けない**: `git tag 0.x.x`（`v0.x.x` は Obsidian のリリース照合で弾かれる）
5. プッシュ: `git push origin main --tags`
6. リリース作成: `gh release create 0.x.x --title "0.x.x" --notes "変更概要"`
7. ダッシュボードが更新されない場合: community.obsidian.md の `…` → **Check for new releases**
