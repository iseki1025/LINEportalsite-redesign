# Phase 5 画像刷新・軽量化（2026-09-15）

## 結果

公開ページのUI用PNG参照をなくし、主要画像をWebPへ切り替えた。シャントの「見る・聴く・触る」は既存教材の動作を参照して新テイストで再制作し、元画像は保存した。病院ロゴ `files/common/kajimoto_rogo2.svg` は無変更。

## 変更ファイル・新規ファイル

変更したHTML: `friend-main.html`, `friend-shunt-main.html`, `friend-exercise-main.html`, `friend-footcare-main.html`, `friend-game-main.html`, `friend-nutrition-main.html`, `friend-nutrition-quiz.html`, `friend-shunt-quiz.html`, `disaster-main.html`, および災害詳細7ページ（`disaster-evacuation.html`, `disaster-faq.html`, `disaster-furniture.html`, `disaster-hazardmap.html`, `disaster-recipe.html`, `disaster-safety.html`, `disaster-stock.html`）。共通CSS `representative-pages.css`, `public-pages.css` とSVGスプライト `assets/icons/support-icons.svg` を更新した。

新規ファイル:

- `assets/icons/caution.svg`
- `files/shunt/shunt-look-v2.webp`, `files/shunt/shunt-listen-v2.webp`, `files/shunt/shunt-touch-v2.webp`
- `files/common/patient-support-hero-v2.webp`, `files/common/clinic-support-illustration-v2.webp`
- `files/pdf/preview-disaster-guide-book-2026.webp`, `files/pdf/preview-footcare-guide-book.webp`, `files/pdf/preview-dietary-therapy-for-dialysis-patients.webp`
- `files/pdf/nutrition/recipes/{spring,autumn,winter}/preview-*.webp` 18点
- 本レポート

移動・実際の削除はなし。旧PNGは元画像やロールバック用として残した。

## CSS・JS・画像の変更内容

注意アイコンをPNGからSVGに置き換えた。Game一覧の4アイコン、脳トレ案内、レシピの季節・タイトルアイコン、フットケアの案内アイコン、シャントの電話アイコン、防災の詳細ボタン、詳細13ページの下部ナビを共通SVGへ置き換えた。ゲーム盤面・障害物・結果演出の絵文字はGameロジックや演出と結び付くため、このPhaseでは変更していない。

シャント3画像は640×640pxのWebPとして表示し、「見る→聴く→触る」の既存順序とチェック文を維持した。画像生成は組み込みimagegenを使用し、元の3画像をそれぞれ内容参照・編集対象とした。生成プロンプトは下記。トップ2画像、冊子プレビュー3画像、公開レシピプレビュー18画像は内容を変えずWebP化した。レシピプレビュー18点は合計15.58MBから4.06MB、トップ・冊子プレビュー5点は合計10.12MBから0.85MBになった。元のシャントPNG3点はPhase 4時点では表示に使っていなかったため、旧PNGとの単純比較を通信量削減とは扱わない。

JavaScriptは `friend-nutrition-main.html` 内のローカルレシピプレビューURL生成を `.webp` に変更し、既存の欠落していたデフォルトプレビュー参照を実在する冬レシピ画像へ修正した。Firebaseから明示的に渡される `previewUrl` / `thumbnailUrl` はそのまま使用する。`disaster-main.html` 内の詳細ボタンは絵文字をSVGへ置換した。Gameロジック、Firebase設定、管理機能、医療本文、ページリンク、PDF内容には変更なし。ページURL変更なし（CSS/SVGのキャッシュ更新用クエリのみ付与）。

## 不要画像の整理・背景画像

公開ページに残る `.png` 参照は0件。公開ページの大型背景PNG参照も0件。`mobile-layout.css` の `background-main.png` は `admin.html` から使用されるため、管理画面非改修方針に従い残した。

削除候補として、置換前の `files/common/caution-icon.png`、トップ2点のPNG、今回WebP化した冊子・レシピプレビューPNG、古いカテゴリ/UI PNGを監査で識別した。ただし、旧シャント3画像と運動説明画像は医療教材の元資料であり削除しない。その他も参照検査だけで削除を判断せず、実際の削除は行っていない。

## 確認と残件

ブラウザーで公開22ページを幅390pxで巡回し、横スクロール・読み込み済み画像の欠落は0件。シャント3点のPC/スマホ表示、注意SVG、トップ2点、冊子プレビュー3点、春・秋・冬のレシピ18点、下部ナビ、シャント連絡先モーダルを確認した。`python scripts/check_text_encoding.py`、`git diff --check` は通過。`python scripts/audit_site.py` の active HTML参照欠落は0件。WebP 26点とSVG 2点をデコード・XML検証した。

手動確認: 新しいシャント教材3画像の動作・解剖表現は医療担当者の確認を受けること。Game演出絵文字や旧画像の最終削除判断、Firebase上の任意 `previewUrl`、全ブラウザー・ズーム・キーボード・Console・全PDF/動画はPhase 7で確認する。画像生成の結果は教育用の視覚補助であり、チェック本文は変更していない。

作業ブランチは `redesign`。`main` worktreeの `git status --short` は空で未変更。commit・pushは未実施。`redesign` worktreeにはPhase 1〜5の変更および別の既存作業ファイルが未コミットで残る。

## imagegen生成プロンプト（組み込みモード）

### 見る

> Use case: scientific-educational. Asset type: patient-support website illustration for the 'look at your dialysis shunt' check. Input image 1 is the existing medical-action illustration and is the content reference/edit target. Recreate the same action and information in a cleaner, calmer 2026 Japanese clinic website illustration: a patient looking carefully at their own shunt forearm, with the forearm and visible shunt access area clearly foregrounded. Preserve the left/right orientation and general pose from the original. Soft light-blue and mint background, restrained pastel accents, crisp outlines, gentle rounded shapes, professional rather than childish. Square artwork with ample margin, no text, no logo, no watermark. Do not invent medical devices, wounds, arrows, hearts, or extra anatomy; do not depict a different action.

### 触る

> Use case: scientific-educational. Asset type: patient-support website illustration for the 'gently touch your dialysis shunt' check. Input image 1 is the existing medical-action illustration and is the content reference/edit target. Recreate the same action: one hand uses two fingertips gently on the shunt forearm to feel it. Preserve the orientation, anatomy, and gesture from the reference. Match a clean, calm 2026 Japanese clinic illustration style: crisp minimal outlines, soft light-blue and mint background, restrained pastel accents, professional rather than childish. Square artwork with ample margin, no text, no logo, no watermark. Do not add medical devices, excessive pressure, wounds, hearts, arrows, or other anatomy; do not depict a different action.

### 聴く

> Use case: scientific-educational. Asset type: patient-support website illustration for the 'listen to your dialysis shunt' check. Input image 1 is the existing medical-action illustration and is the content reference/edit target. Recreate the same action and information: one hand gently holds a stethoscope head on the shunt forearm, with the tube visible. Preserve the orientation, anatomy, and gesture from the reference. Match the other shunt illustrations in a clean, calm 2026 Japanese clinic style: crisp minimal outlines, soft light-blue and mint background, restrained pastel accents, professional rather than childish. Square artwork with ample margin, no text, no logo, no watermark. Do not add extra medical devices, wounds, arrows, hearts, or other anatomy; do not depict a different action.

## Shunt anatomy correction (2026-09-15)

The first v2 "look" illustration showed an implausible arm angle, and the three v2 images did not share one composition. These are preserved as revision history, but `friend-shunt-main.html` now displays `files/shunt/shunt-look-v3.webp`, `files/shunt/shunt-listen-v3.webp`, and `files/shunt/shunt-touch-v3.webp`. All three use the same seated adult, green shirt, table, pastel blue/mint palette, and relaxed palm-up left forearm. Only the right-hand action changes between panels. The source PNGs are preserved in the imagegen output directory; each displayed WebP was mechanically resized to 640 x 640 pixels. The imagegen edit prompts used were:

### Look v3

> Create a square 1:1 polished Japanese clinic website medical self-check illustration, the LOOK panel of a matched three-panel dialysis shunt series. Use the supplied image only for its friendly clean illustration style, green shirt and blue/mint palette; completely rebuild its incorrect human pose. A single adult patient sits naturally at a small table, shown from waist/chest up. Their LEFT forearm with shunt is resting comfortably on the tabletop, palm up, elbow bent about 90 degrees and close to their torso, forearm extending gently toward the viewer's right. The left upper arm is visibly and continuously connected to the left shoulder, elbow, forearm, wrist and hand. They lean slightly forward and look down with their eyes at the inner side of this resting forearm, clearly inspecting it. Their right arm and hand rest naturally on the table without touching the left arm. Both shoulders level, no arm raised or crossing the chest. Exactly one person, two anatomically plausible arms and two hands, realistic joints and fingers. Calm expression, soft pastel blue background, subtle mint accents, delicate dark-teal outlines, flat-vector-like editorial illustration with slight shading, consistent single camera view, no text, symbols, labels, logos, watermark, equipment, medical staff, or extra limbs. Keep ample margins so this can be displayed in a card. Priority: obvious physically natural arm angle and gaze directed at the arm.

### Touch v3

> EDIT THE SUPPLIED IMAGE as the TOUCH panel of the same three-panel medical self-check illustration set. Preserve the exact same single adult patient identity and face, hair, light green shirt, seated position, camera framing, tabletop, pastel blue/mint background, drawing style, and especially the anatomically natural LEFT shunt arm position: patient-left elbow beside torso, left forearm relaxed palm-up on tabletop extending toward viewer right, all joints continuously connected. Make only the patient's RIGHT hand move gently across the table so the pads of the right index and middle fingers rest lightly on the inner side of the LEFT forearm around the mid-forearm, indicating feeling the shunt thrill. The right elbow and right wrist must remain visibly connected to the patient's right shoulder in a natural reachable angle; do not add a disembodied hand. Keep patient eyes looking down at the contact point. Exactly two arms, two hands, anatomically plausible fingers (five per hand), no arm through the torso, no hand overlap confusion. This should look almost identical to the reference apart from the subtle two-fingertip touch action. Clean friendly clinic educational illustration, square 1:1, no text, logos, watermark, stethoscope or other objects.

### Listen v3

> EDIT THE SUPPLIED IMAGE as the LISTEN panel of the same three-panel medical self-check illustration set. Preserve the exact same single adult patient identity/face/hair, light green shirt, seated position, camera framing, table, blue/mint background, clean friendly illustration style and, most importantly, the anatomically natural LEFT shunt arm position: left elbow beside torso, forearm resting palm-up on the table toward viewer right; the left shoulder, upper arm, elbow, wrist and hand must stay visibly connected. Only move the patient's RIGHT hand to naturally hold a small stethoscope chest piece against the inner side of their own LEFT forearm, near the vein at mid-forearm. Add a simple dark teal stethoscope: chest piece held by right fingertips, tubing extending plausibly to earpieces in both of the same patient's ears. The right shoulder, arm, elbow, wrist and hand are visibly continuously connected, no floating hand, no impossible crossing. Patient gently tilts head and listens while eyes look toward forearm. Exactly one person, two arms, two hands, correct fingers and joints. Keep same composition and facial identity as reference; change only right-hand gesture and stethoscope. Square 1:1, no words, labels, logos, watermark, medical staff, extra limbs, extra equipment.
