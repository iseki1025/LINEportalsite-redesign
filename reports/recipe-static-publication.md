# レシピ公開ページの固定運用（2026-09-15）

管理職の監査後に追加指示を受けて公開する運用に合わせ、`friend-nutrition-main.html` の公開レシピ一覧を固定データに変更した。公開ページからFirebase SDK・Firestoreの取得処理を外し、初期の「読み込み中...」が外部データ待ちで残らないようにした。同ページで使うPapaParseも、外部CDNから既存のローカル版 `js/papaparse.min.js` に変更した。季節タブ、件数、カード、PDFプレビュー、表示順は変更していない。公開中の18件（春6・夏0・秋6・冬6）と同名のPDF18件がサイト内に存在することを確認した。

既存レシピのPDFリンク先は今回変更せず、旧SakuraのURLを維持した。Firebase上にのみ存在する公開済みレシピがないかは管理データとの照合が必要で、データベースやStorageの削除はしていない。`admin-recipe-manager.html` は旧アップロード方式の管理画面として残しており、レシピ以外のFirebase利用には触れていない。

今後レシピを追加するときは、承認内容を確認し、PDFとWebPプレビューを `files/pdf/nutrition/recipes/{spring,summer,autumn,winter}/` の該当季節に配置して、`friend-nutrition-main.html` の `publishedRecipes` にタイトル・URL・`season`・公開日を追加する。季節判定用の `recipeSeasonByFile` も必要に応じて更新する。公開前に `python scripts/check_text_encoding.py`、`git diff --check`、全季節タブ・PDFリンク・画像表示を確認する。
