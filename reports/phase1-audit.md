# Phase 1 現状確認・基盤整理レポート

実施日: 2026-09-14  
対象: `C:\pythonapp\LINEportalsite-main - redesign` / `redesign` ブランチ

## 1. 結論

- 作業場所とブランチは仕様書どおりで、開始時の作業ツリーは clean だった。
- 現行ルート直下の HTML は公開・ポータル系 22件、管理系 9件。`old_design/` と `index_old.html` に旧 HTML が22件ある。
- 現行 HTML から静的に参照されるローカル HTML/CSS/JS/画像/PDFに参照切れはない。
- 管理画面は公開側と完全分離されていない。公開側の `disaster-main.html` と `friend-nutrition-main.html` も Firebase/Firestore を使用する。
- `node_modules` は本番配信に不要。新規混入防止のため `.gitignore` に追加したが、既存の追跡ファイルは削除していない。
- PDF 58件、画像50件はすべて読み取り可能。PDF内容、医療文章、遷移、管理機能、ゲームロジックには変更を加えていない。
- Phase 1 の方針どおり、大幅な見た目変更は行っていない。

機械可読な全件データは `reports/phase1-inventory.json`、再検査手順は `scripts/audit_site.py` に保存した。

## 2. ファイル棚卸し

`node_modules/` を除く Git 追跡ファイルの主要内訳:

| 種別 | 件数 | 合計サイズ | 主な役割 |
| --- | ---: | ---: | --- |
| HTML | 53 | 699,221 B | 現行31件、旧22件 |
| CSS | 8 | 131,441 B | 現行4件、旧4件 |
| JavaScript | 16 | 98,855 B | 遷移、Q&A、Firebase、管理機能 |
| PNG | 50 | 65,634,617 B | UI、医療説明、PDFサムネイル |
| SVG | 1 | 10,549 B | 病院ロゴ |
| PDF | 58 | 44,846,563 B | ガイド、栄養資料、レシピ |
| CSV | 3 | 123,366 B | Q&Aデータ |

### 現行 HTML

公開・ポータル系22件:

- ポータル: `index.html`
- 患者向け: `friend-main.html`、シャント2件、運動1件、栄養2件、フットケア1件、ゲーム5件
- 災害: `disaster-main.html` と詳細7件
- Q&A: `qa-main.html`

管理系9件:

- `admin.html`
- `admin-dashboard.html`
- `admin-login.html`
- `admin-setup.html`
- `admin-user-manager.html`
- `admin-qa-editor.html`
- `admin-recipe-manager.html`
- `admin-dialysis-status-editor.html`
- `migrate-csv-to-firestore.html`

旧 HTML は `old_design/` に21件、ルートに `index_old.html` が1件ある。現行ページから旧 HTML への参照はない。

## 3. CSSの現状

| CSS | 現行参照元 | 判断 |
| --- | ---: | --- |
| `portal-design.css` | 18ページ | 公開系の共通基盤。Phase 2の主対象 |
| `style.css` | 12ページ | 管理系8、ゲーム3、移行ツール1で共有。管理系への波及に注意 |
| `components.css` | 11ページ | 管理系8、ゲーム3で共有。管理系への波及に注意 |
| `mobile-layout.css` | 1ページ | 現行では `admin.html` のみ。整理候補 |
| `style_old.css` | 0ページ | 削除・archive候補 |
| `old_design/*.css` | 旧ページのみ | archive候補 |

現行公開・ポータル系には `style` 属性が81箇所、`<style>` が20個ある。Phase 2以降で共通CSSへ寄せる対象だが、ページ固有UIとゲーム固有描画は分離して扱う。

## 4. JavaScriptと依存関係

- `js/page-transition-bootstrap.js` と `js/page-transition.js` は現行31ページすべてが参照する。全体影響が大きいため維持する。
- `js/update-display.js` は `index.html` と `friend-main.html` が参照する。
- Q&A関連の `qa-source.js` と `qa-data-embedded.js` は4ページ、`qa-script.js` は3ページが参照する。
- `qa-main.html` はページ内スクリプトで検索・絞り込み・Accordionを構成している。
- `disaster-main.html` は透析状況、`friend-nutrition-main.html` はレシピ取得に Firestore を使用する。
- 管理9ページは Firebase Authentication / Firestore / Storage と `js/admin/` に依存する。
- `friend-game-animal-merge.html` は Matter.js 0.19.0 CDN、Q&A系4ページと移行ツールは Papa Parse 5.3.2 CDNを使用する。

HTMLから参照が見つからないJS候補:

- `js/firebase.js`
- `js/qa/qa-loader.js`
- `js/tab/tab-script.js`
- `js/papaparse.min.js`

動的読み込みの可能性をPhase 3まで再確認し、現時点では削除しない。

## 5. ページ遷移とスタッフ入口

- ポータルから患者向け、災害、Q&Aへの導線は存在する。
- 患者向けトップから各健康カテゴリー、各カテゴリーから患者向けトップへの導線は存在する。
- 災害トップから詳細7ページ、各詳細から災害トップへの導線は存在する。
- ミニゲーム4件はゲーム一覧へ戻る導線を持つ。
- `index.html` のCopyrightに `admin-login.html` への `stealth-link` があり、隠し入口は維持されている。
- `index.html` にはGoogle Sheets管理リンクと `admin-user-manager.html` への通常表示リンクもある。ポータルの利用対象と公開範囲をPhase 3のデザイン確認時に再確認する。
- 現行ページ間の静的ローカル参照切れは0件。旧ページは旧フォルダ内にCSS/JS/画像を複製していないため15件の参照切れがあり、公開対象外として扱う。

## 6. PDF・画像

### PDF

- 58ファイル、135ページ、約42.8 MiB。
- 全件を `pypdf` で開けること、暗号化なし、構造エラーなしを確認した。
- `about-emergency-food-stockpile.pdf` と `stocked-food.pdf` は内容が完全一致する。名称・既存URL維持のため現時点では統合しない。
- 公開HTMLには Sakura Server 上のPDFを指す `http://` URLが28箇所ある。HTTPS配信時のMixed Contentリスクと、同一PDFがリポジトリ内にもある点をPhase 3で確認する。URL変更は独断で行わない。

### 画像

- PNG 50件、約62.6 MiB。Pillowによるデコード検査は全件成功した。
- 最大は `preview-disaster-guide-book-2025.png` の約3.67 MiB（2880x1620）。1 MiB超の画像が複数あり、Phase 5の軽量化対象になる。
- 静的HTML/CSSから直接参照されない候補は44件。ただし栄養レシピのサムネイルはJavaScriptでファイル名を組み立てるため、未参照候補をそのまま削除してはいけない。全候補はJSONを参照する。

## 7. node_modules

- Git追跡中は23ファイル、263,992 B。
- 内容は `papaparse@5.5.3` のみ。
- 現行HTMLは `node_modules` を参照せず、Q&A系は CDN の Papa Parse 5.3.2を参照する。
- 静的本番サイトの実行には不要と判断した。
- `.gitignore` に `node_modules/` を追加した。既存追跡分の削除は大量削除に準じるため、今回は行わず削除候補に留める。

## 8. 旧資産・不要候補の分類

### archive候補

- `old_design/`
- `index_old.html`
- `style_old.css`

過去デザインの比較資料として意味があるため、即時削除より `archive/` への移動が安全。

### toolsまたは記録用フォルダへの移動候補

- `gemini_response_shunt.txt`
- `icons.txt`
- `pdf_content.txt`
- `pdf_content_fixed.txt`
- `jpn_files.txt`
- `jpn_files_list.txt`
- `inspect_filename.py`
- `scripts/list_jpn_files.py`
- `scripts/rename_pdf_files.py`
- `update_qa.bat`

### 削除候補

- 追跡中の `node_modules/`
- 内容が空で一致する `jpn_files.txt` と `jpn_files_list.txt` の片方
- 未参照JS 4件。ただし動的参照を再確認してから判断する

実際に削除・移動したファイルはない。

## 9. 文字コード

開始時に次の3ファイルがUTF-8ではなかったため、基盤整理として修正した。

- `gemini_response_shunt.txt`: Shift_JISからUTF-8へ内容保持変換
- `pdf_content.txt`: Shift_JISからUTF-8へ内容保持変換
- `jpn_files.txt`: 文字化けしたUTF-16の旧一覧を、現行ファイル名からUTF-8で再生成（該当0件）

`python scripts/check_text_encoding.py` は修正後に成功している。

## 10. Phase 2以降への注意事項

1. `portal-design.css` を共通デザインシステムの中心にし、ゲームと管理画面が共有する `style.css` / `components.css` は同時に大改修しない。
2. `disaster-main.html` と `friend-nutrition-main.html` のDOM変更ではFirestore取得・fallback表示を必ず回帰確認する。
3. `qa-main.html`、`disaster-faq.html`、シャント・栄養ページのQ&Aスクリプトは既存DOM契約を先に記録する。
4. 外部PDFのHTTP URL、CDN、Google Drive動画、Google Sheets、Firebaseはローカル静的検査だけでは保証できないため、代表ページ実装時に実ブラウザで確認する。
5. 旧資産、未参照候補、`node_modules` の削除は、Phase 3の代表ページ確認後または明示承認後にまとめて行う。

## 11. Phase 1変更サマリー

- 変更ファイル: `gemini_response_shunt.txt`、`pdf_content.txt`、`jpn_files.txt`
- 新規ファイル: `.gitignore`、`scripts/audit_site.py`、`reports/phase1-inventory.json`、`reports/phase1-audit.md`
- 移動ファイル: なし
- 実際に削除したもの: なし
- CSS共通化: なし
- JS機能変更: なし（監査スクリプトのみ追加）
- 画像変更: なし
- 既存機能への影響: なし
- URL変更: なし
- 未解決事項: 外部URL実地確認、旧資産・未参照候補の処遇、公開PDFのHTTP URL、ポータル管理メニューの公開範囲
- 手動確認項目: Firebase認証/取得、Q&A検索と絞り込み、動画モーダル、PDF外部遷移、全ゲーム、スマホ/PC/キーボード
- 現在ブランチ: `redesign`
- `main`: 別Worktreeのまま未変更
- commit / merge / rebase / push: 未実施

