# Phase 2 共通デザインシステム構築レポート

実施日: 2026-09-14  
対象: `C:\pythonapp\LINEportalsite-main - redesign` / `redesign` ブランチ

## 1. 結論

`portal-design.css` をVersion 3.0へ更新し、Phase 3以降の代表ページ実装に利用できる共通デザインシステムを構築した。既存クラスの互換性を維持しながら、新しい共通部品、レスポンシブ基盤、アクセシビリティ基盤を追加している。

公開ページの医療文章、ページ遷移、URL、Firebase、Q&Aロジック、ゲームロジック、管理画面には変更を加えていない。

## 2. 共通化した内容

### Brand Colors

- Primary: `#11A6B8`
- Secondary: `#3215C3`
- シャント、運動、食事、フットケア、ゲーム、災害、Q&Aのカテゴリー色
- Info / Success / Warning / Dangerの状態色
- 背景、境界線、テキスト、フォーカスリングのSemantic Token

カテゴリー色は識別用に限定し、リンク、フォーカス、Primary Buttonなどはブランドカラーを基準にした。

### Typography

- 日本語本文: `Noto Sans JP`
- 英数字・見出し補助: `Poppins`
- 本文基準: 16px
- 流動的な見出しサイズ、本文行間1.75、見出し行間1.35
- 375px前後でもルート文字サイズを縮小しない

### Layout / Responsive Grid

- Narrow: 760px
- Default: 960px
- Wide: 1180px
- スマートフォン: 1列
- 640px以上: 2列
- 1024px以上: 最大3列
- `minmax(0, 1fr)` と折り返し指定により横スクロールを抑止

### Components

- Header
- Footer
- Hero
- Card / Category Card
- Primary / Secondary / Danger Button
- Tabs
- Accordion（`details` / `summary`対応）
- Info / Success / Warning / Danger Alert
- PDF Card
- Modal
- Bottom Navigation
- Responsive Grid
- Badge / Update Chip
- Search UI
- Skip Link / Visually Hidden

## 3. 既存クラス互換

既存ページで利用中の次のクラスは維持した。

- `.app-header`、`.header-logo`、`.header-title`
- `.hero-section`、`.hero-title`、`.hero-subtitle`
- `.card-grid`、`.card`、カテゴリーアクセント
- `.btn`、`.btn-primary`、`.btn-secondary`
- `.tab-nav`、`.tab-btn`
- `.collapsible`
- `.bottom-nav`、`.nav-item`
- `.status-panel`
- `.qa-search-*`

既存の `.warning-box` やページ固有Modalは固有CSSを優先し、今回追加した共通Alertと不用意に衝突させていない。

## 4. Accessibility

- 共通操作領域を44px以上に設定
- `:focus-visible` の明瞭なアウトラインとリングを追加
- `prefers-reduced-motion` でAnimation、Transition、Smooth Scrollを抑制
- Forced Colors環境向け境界線を追加
- Skip LinkとVisually Hidden Utilityを追加
- 色だけに依存しないAlert、Badge、Accordion表現を用意
- 共通CSSを利用する18ページから `maximum-scale=1.0` と `user-scalable=no` を撤去
- 小画面では正式ロゴとページタイトルが重ならないよう、ページ名をHero側に任せる構成にした

独立したゲーム4ページは操作ジェスチャーとの競合を検証してからズーム制限を撤去するため、Phase 4の確認対象として残した。

## 5. Background / Motion

- 大きな背景画像への依存をなくし、軽いCSS Gradientと単色背景へ変更
- Glass表現はHeaderとBottom Navigationの控えめな半透明に限定
- Card HoverはマウスなどHover可能な端末に限定
- Parallaxや常時Animationは追加していない

## 6. 実ブラウザ検証

確認用ページ: `reports/phase2-design-system.html`

| Viewport | Grid | Main Width | 横スクロール |
| --- | --- | ---: | --- |
| 390x844 | 1列 | 画面幅内 | なし |
| 768x900 | 2列 | 画面幅内 | なし |
| 1280x900 | 3列 | 1180px | なし |

スマートフォンで確認した共通値:

- 本文: 16px
- Header: 68px
- 正式ロゴ高: 32px
- Button / Tab / Bottom Navigation: 47〜59px

操作確認:

- Modal: 開く、閉じる、背景クリック、Escape、開閉時のフォーカス復帰が正常
- Accordion: 開閉が正常
- コンソールError / Warning: なし

既存ページ回帰確認:

- `friend-main.html`: 390pxで1列、1280pxで3列、横スクロールなし
- `qa-main.html`: 138件表示、検索語「シャント」で10件へ絞り込み、エラーなし
- `disaster-main.html`: 透析状況パネルとカード5件を表示、エラーなし

## 7. 変更ファイル

- `portal-design.css`
- 共通CSSを利用するHTML 18件のViewport設定
  - `index.html`
  - `friend-main.html`
  - `friend-shunt-main.html`
  - `friend-shunt-quiz.html`
  - `friend-exercise-main.html`
  - `friend-nutrition-main.html`
  - `friend-nutrition-quiz.html`
  - `friend-footcare-main.html`
  - `friend-game-main.html`
  - `disaster-main.html`
  - `disaster-evacuation.html`
  - `disaster-faq.html`
  - `disaster-furniture.html`
  - `disaster-hazardmap.html`
  - `disaster-recipe.html`
  - `disaster-safety.html`
  - `disaster-stock.html`
  - `qa-main.html`

## 8. 新規ファイル

- `reports/phase2-design-system.html`: 全共通部品の目視・操作確認用ページ
- `reports/phase2-report.md`: 本レポート

## 9. 完了時サマリー

- 移動ファイル: なし
- 削除候補: Phase 1レポートから変更なし
- 実際に削除したもの: なし
- CSS共通化: Brand、Typography、Header、Footer、Hero、Card、Button、Tabs、Accordion、PDF Card、Alert、Modal、Bottom Navigation、Responsive Grid、Accessibility
- JS変更: 公開サイトは変更なし。確認用ページの操作コードのみ追加
- 画像変更: なし
- 既存機能への影響: DOMと機能ロジックは変更なし。共通CSS利用ページの表示幅とカード列数を改善
- URL変更: なし
- 未解決事項: 独立ゲーム4件のズーム制限、ページ固有CSSの共通部品への移行、外部HTTP PDF URL
- 手動確認項目: Phase 3で代表4ページの全機能、キーボード順序、200% Zoom、実機Touch、Firebase実データを再確認
- 現在ブランチ: `redesign`
- `main`: 別Worktreeのまま未変更
- commit / merge / rebase / push: 未実施

