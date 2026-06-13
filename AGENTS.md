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

## 公式ガイドライン（docs.obsidian.md）

[Theme guidelines](https://docs.obsidian.md/Themes/App+themes/Theme+guidelines) が掲げる4原則:

1. **CSS 変数を使う** — `body` に一般設定、`.theme-light` / `.theme-dark` に色を上書きする
2. **詳細度の低いセレクタを使う** — Obsidian がクラス名や DOM 構造を変えても壊れにくくするため、特定クラスを深く狙う複雑なセレクタは避ける
3. **アセットはローカルに同梱** — フォント・画像などのリモート読み込みは禁止（オフラインで使えること。[Developer policies](https://docs.obsidian.md/Developer+policies) のハード要件）
4. **`!important` を避ける** — ユーザーが snippet でテーマを上書きできなくなるため

[Developer policies](https://docs.obsidian.md/Developer+policies) のテーマ向け要件:
- ネットワークアセット読み込み・クライアントサイドテレメトリ・コード難読化・自動アップデート機構は禁止
- LICENSE ファイル必須（本リポジトリは MIT で対応済み）

## 提出・リリース要件（[Submit your theme](https://docs.obsidian.md/Themes/App+themes/Submit+your+theme)）

- リポジトリルートに必須: `README.md` / `LICENSE` / スクリーンショット / `manifest.json`
- スクリーンショットは **512×288 px**（サイズ違いはボットに弾かれる）
- `manifest.json` の `version` は semver `x.y.z` 形式
- **リリースタグは manifest の version と完全一致**させ、`manifest.json` と `theme.css` をバイナリアセットとして添付する
  （タグに `v` を付けると一致しなくなる → Release Checklist 参照）
- ディレクトリはデフォルトブランチ HEAD の `manifest.json` を読む → 提出・更新前に必ず main へコミットしておく

## Lint 対策（community テーマ審査）

Obsidian の community linter が警告する主なルール:

### `!important` を使わない実装パターン

| やりたいこと | NG | OK |
|---|---|---|
| ハイライト背景色の変更 | `mark { background: #xxx !important }` | `.theme-dark { --text-highlight-bg: #xxx }` で変数上書き |
| タグ色の変更 | `.tag { color: #xxx !important }` | `.theme-dark { --tag-color: #xxx }` で変数定義 |
| コードブロック色 | `.cm-keyword { color: #xxx !important }` | `.theme-dark { --code-keyword: #xxx }` で変数定義 |
| 数式色の変更 | `.cm-math { color: #xxx !important }` | セレクタ詳細度を上げて `.theme-light .cm-math { color: #xxx }` |

**`!important` が技術的に必要なケース（維持してよい）:**
- 背景グラデーション + ドットグリッド（`.view-content { background: ... !important }`）
  → Obsidian のデフォルト background を上書きするために必須
- エディタの透明化（`.cm-scroller { background: transparent !important }`）

### コントラスト比（WCAG AA）

- 通常テキスト: **4.5:1 以上**
- 大きなテキスト（H1〜H3 相当）: **3:1 以上**
- グリーン系テーマは背景色と文字色が近くなりやすい → 見出し色は背景から遠いニュートラル色を選ぶ
  - Dark mode: 見出しは `#e2f5e2`（ほぼ白）
  - Light mode: 見出しは `#031f04`（ほぼ黒）
- グリーンのグラデーション背景に緑系の見出し色を置くとコントラスト不足になる

### セレクタの重複

- linter は同一セレクタブロックの重複を警告する
- `.theme-dark {}` と `.theme-light {}` は **必ずファイル内に1つずつ**
- CSS 変数の追記は常に先頭の1つのブロックに集約する

### CM6（edit view）固有の注意

- `---`（HR）の edit view は `.hr.cm-line hr`（`.cm-line.hr` の内側にある `<hr>` 要素）を直接ターゲットにする
  - **正解**: `.hr.cm-line hr { border-top: 2px dotted var(--hr-color); }`
  - `.cm-line.hr` や `.HyperMD-hr` に border を設定すると二重線になる（div 要素に当たるため）
- heading の edit view 上マージンは Obsidian デフォルト → テーマで触らない方が安全

## Release Checklist

1. `manifest.json` の `version` を更新
2. `theme.css` 冒頭コメントの `Version:` を更新
3. コミット: `git add manifest.json theme.css && git commit -m "x.x.x: 変更概要"`
4. タグは **`v` を付けない**: `git tag 0.x.x`（`v0.x.x` は Obsidian のリリース照合で弾かれる）
5. プッシュ: `git push origin main --tags`
6. リリース作成: `gh release create 0.x.x --title "0.x.x" --notes "変更概要"`
7. ダッシュボードが更新されない場合: community.obsidian.md の `…` → **Check for new releases**
