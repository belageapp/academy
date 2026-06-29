# CHANGELOG - ホリスケアアカデミー スクール管理システム

---

## v1.26.0 - 2026/06/29（構築２６）

### admin.html

#### 管理者プレビュー機能（生徒画面の閲覧）を追加
- 申し込み詳細モーダルのヘッダーに「👁 生徒画面」ボタンを追加
- ボタンを押すとiFrameモーダルで当該生徒のマイページを表示
  - iFrameサイズ：スマホ相当（最大420px幅 × 820px高さ）
  - モーダルのオレンジヘッダーに「読み取り専用 — 操作は無効です」を表示
- ✕ボタンで閉じるとiFrame srcを空にしてセッションをリセット

#### 申し込み一覧・詳細に受講番号を追加
- 申し込み一覧テーブルの先頭列に受講番号を追加（青太字表示）
- 申し込み詳細モーダルの基本情報セクション先頭に受講番号を追加

### mypage_course_shonin.html

#### 管理者プレビューモード対応
- URLパラメータ `?adminPreview=true` でプレビューモードに切り替え
- プレビューモード時の動作：
  - `click` / `submit` / `touchend` / `mousedown` / `pointerdown` をキャプチャフェーズで全ブロック
  - ボタン・リンク・フォームを `pointer-events: none` で無効化
  - カーソルを `not-allowed` 表示
- プレビュー時の氏名表示：Firebase AuthユーザーではなくFirestoreの申込データから取得
  - `data.sei + data.mei` を優先し、なければ `data.name` にフォールバック
  - 重複していたページ内バナーを削除（iFrameモーダルのヘッダーで代替）

### その他

#### `.firebase/` キャッシュをGit管理から除外
- `.gitignore` に `.firebase/` を追加
- デプロイのたびに更新される `hosting..cache` の差分表示を解消

---

## v1.25.0 - 2026/06/24（構築２５）

### admin.html

#### 申し込み詳細モーダルにOJT実習先情報を追加
- 申込者の詳細モーダルにOJT実習先情報を表示するよう対応
  - 表示項目：施設名・住所・電話番号・担当者・届出理由・届出日・実習修了報告の施設名
  - データソース：`practiceVenue`（実習先届出フォーム）および `ojtReport`（実習修了報告）
  - いずれかのデータが存在する場合のみセクションを表示

#### 詳細モーダルの全セクションのスタイルを統一
- `modal-sec` / `modal-sec-hd` クラスを新設し、全セクションの余白・罫線・タイトルスタイルを統一
  - セクション区切り：上部に `border-top: 2px solid var(--teal-light)` + `margin-top: 20px` / `padding-top: 16px`
  - セクションタイトル：`font-size: 13px` / `font-weight: 700` / `margin-bottom: 12px`
  - 適用セクション：📋 基本情報（新規追加）・🏥 実習先情報・📊 進捗状況・📬 お問い合わせ履歴

### その他

#### プロジェクト構成を整理
- `holiscare-functions` を `academy/` 配下に移動し `.gitignore` で除外
- `firebase.json` を新規作成し Firebase Hosting デプロイに対応

---

## v1.24.0 - 2026/06/21（バグ修正）

### admin.html

#### お問い合わせ返信が送信できない不具合を修正
- `replyInquiry()` 関数で、モーダルを一度開いた後にメインリストから返信しようとすると「回答を入力してください」が表示されて送信できない問題を修正
  - 原因：`modal-reply-{id}` の要素が DOM に残っている状態で `||` 演算子が要素の「存在」で短絡評価され、空のモーダル textarea の値を取得していた
  - 修正：要素の存在ではなく値の有無で判定するよう変更（`reply-{id}` → `modal-reply-{id}` の順に値をチェック）

---

## v1.23.0 - 2026/06/19（構築２３）

### mypage.html

#### お知らせのリアルタイム表示に対応
- お知らせ取得を `getDocs`（一回取得）から `onSnapshot`（リアルタイム）に変更
  - ページを開いたままでも管理者がお知らせを送信した瞬間に自動表示される
  - 従来はページをリロードしないと新着お知らせが表示されなかった
- 既読状態を `window.__addReadId()` でキャッシュに同期し、リアルタイム再レンダリング時も既読状態を維持

---

## v1.22.0 - 2026/06/19（構築２２）

### admin.html

#### 申し込み詳細モーダルにお問い合わせ履歴セクションを追加
- モーダル下部にその受講者のお問い合わせ履歴を一覧表示
  - Firestoreの `inquiries` コレクションを `applicationId` でフィルタし、新しい順に表示
  - 件名・投稿日・回答状況バッジ（回答済み/未回答）・質問本文・回答内容を表示
- 未回答の問い合わせには回答入力テキストエリア＋「返信する」ボタンを表示
  - モーダル内から直接返信操作が可能
  - 返信後にモーダル内履歴を自動リフレッシュ
- `openModal()` 表示時に `loadModalInquiries()` を自動呼び出し

---

## v1.21.0 - 2026/06/18（構築２１）

### admin.html

#### お知らせ一覧の送信対象表示を改善
- 「特定受講生」と表示されていた箇所を受講者名に変更
  - `emails` 配列を `all` キャッシュで名前解決して表示
  - 複数名の場合は「、」区切りで並べて表示
  - メールアドレスが受講者と一致しない場合はメールアドレスをそのまま表示
- 受講者名の後ろに受講番号を括弧表示（例：`田中 太郎（40100001）`）
  - 受講番号未設定の場合は `（—）` と表示

---

## v1.20.0 - 2026/06/16（構築２０）

### admin.html / holiscare-functions/index.js

#### AEGISC-LMS ID発行時の管理者通知メール
- AEGISC-LMS IDが新規発行されたタイミングで `info@holiscare.or.jp` に通知メールを自動送信
  - 送信タイミング：`saveStatus()`（手動でactiveに変更）・`approveDoc()`（書類承認によるactive移行）の両パス
  - メール内容：氏名・ふりがな・メールアドレス・電話番号・受講番号・AEGISC-LMS ID・パスワード
- Cloud Function `sendEdulioIssuedAdminMail` を追加（`holiscare-functions`）

---

## v1.19.0 - 2026/06/16（構築１９）

### admin.html

#### AEGISC-LMS ID発行ロジックをヘルパー関数に統一
- `issueEdulioId()` ヘルパー関数を追加
  - Firestore `counters/edulio` からカウンターを取得
  - `all`（全受講者キャッシュ）の既存IDをセットに持ち、`do-while` で重複をスキップして次の未使用IDを自動検出
  - counterを手動調整する必要をなくした（例：hca004が空いている場合も次の受講者が自動でhca004を取得）
- `saveStatus()`・`approveDoc()` のインラインID発行コードを `issueEdulioId()` 呼び出しに置き換え

---

## v1.18.0 - 2026/06/15（構築１８）

### admin.html

#### AEGISC-LMS IDの上書き防止
- `saveStatus()`・`approveDoc()` の両コードパスで、ID発行前にFirestoreから現在のドキュメントを取得し `edulioId` の有無をチェック
- 発行済みの場合はスキップ（コンソールログ出力）し、上書きを防止
- 背景：saveStatus() と approveDoc() の2つのパスが独立してIDを発行していたため、同一受講者に異なるIDが割り当てられる問題が発生していた（Q8RsYFZDvPfrzeXiWNpe が hca002 → hca004 に上書きされた事例）

#### 申し込み詳細にAEGISC-LMS ID/パスワードを表示
- 受講中ステータスの詳細ビュー（`.prog-active-items`）に `edulioBox` を追加
- `edulioId` が存在する場合のみ表示：ID・パスワードをモノスペースフォントで表示

### press_release.html（新規作成）

- オンライン初任者研修のプレスリリース向けA4印刷対応HTML
- 内容：受講料33,000円（税込）・STEP1〜4フロー・eラーニング10科目・動画114本
- ブラウザの印刷機能（Cmd+P）からPDF出力可能

---

## v1.17.0 - 2026/06/07〜08（構築１７）

### mypage_course_shonin.html

#### 仮修了証明書フロー刷新（PDF発行→郵送方式へ）
- 受講生が PDF をダウンロードする旧フローを廃止
- 管理者が仮修了証明書を郵送する新フローに変更
- temp-cert-modal HTML・`openTempCert`・`closeTempCert`・`downloadTempCert` 関数を削除
- tempCertItem 表示：「発行する」ボタンを廃止し、郵送ステータス（発送済み＋日付 / 郵送待ち / ロック中）を表示
- `notifyTest2Passed()` を追加：テスト２合格時に管理者への Firestore お知らせ＋`sendTest2PassedMail` を送信

#### STEP2 順序・タイトル変更
- カード表示順を AEGISC-LMS → 動画事例検討 → eラーニング に変更
- STEP2 タイトルを「動画教材・eラーニング」に変更
- 受講ガイド STEP2 の説明文を「AEGISC-LMS動画、動画教材（事例検討）、eラーニング」の順に更新
- 次のステップバナーの優先順位を AEGISC → 動画 → eラーニング に変更

#### 実習先届出 理由選択肢変更（4択→3択）
- 「受け入れ同意済み」を削除し、以下の3択に統一
  - 現在勤務中
  - 勤務予定（詳細入力欄あり：いつから）
  - その他（詳細入力欄あり）
- バリデーション：勤務予定・その他のどちらも詳細未入力はエラー

#### 取り掛かりフラグ実装
- `markStarted(field)` ヘルパー関数追加（初回のみ Firestore に書き込み）
- `openTask()` 呼び出し時に `taskStarted` を記録
- `markVideoWatched()` 初回視聴時に `videoStarted` を記録
- AEGISC-LMS リンクのクリック時に `aegiscStarted` を記録（`window.__markAegiscStarted`）

#### 重複お知らせ送信の修正
- eラーニング完了・動画完了時の `sendCompletionNotice` 直接呼び出しを削除
- onSnapshot の `__isFirstSnapshot` ガードに一本化し二重送信を解消

---

### admin.html

#### 仮修了証明書発行管理セクション追加
- サイドバーメニューに「仮修了証明書発行」を追加（実習先届出の直後）
- `loadTempCertList()`：`test2Passed == true` の申し込みを取得し、未発送／発送済みグループに分けて表示
- `markTempCertSent()`：`tempCertIssued: true`・`tempCertSentAt` を保存し、受講生にお知らせ＋`sendTempCertSentMail` 送信
- `initAllBadges()` に仮修了証明書バッジ（`test2Passed && !tempCertIssued` の件数）を追加

#### 実習先届出一覧 改善
- 受講生名の表示を `r.name` フィールド優先に修正（`r.sei + r.mei` のみでは空欄になっていた問題を解消）
- 「対応済みにする」ボタン（`markVenueChecked`）を追加
- フィルターをボタン3つから `<select>` ドロップダウンに変更（テキスト発注一覧と同スタイル：対応待ち / すべて / 対応済み）
- `setVenueFilter()` をシンプル化（ボタン style 更新ロジックを削除）

#### 取り掛かりフラグ表示（申し込み詳細）
- `pi()` 関数に `'started'` 状態を追加（青バッジ「▶ 取り掛かり中」）
- CSS 追加：`.prog-dot.started`・`.prog-badge.started`（`#E3F2FD` / `#1565C0`）
- eラーニング・動画事例検討・AEGISC の各項目に started 状態を反映
  - `eLearningState = taskAllDone ? 'done' : taskStarted ? 'started' : 'none'`
  - `videoState = videoAllDone ? 'done' : videoStarted ? 'started' : 'none'`
  - `aegiscState = externalVideoDone ? 'done' : aegiscStarted ? 'started' : 'none'`

#### 申し込み詳細 受講中ステータス表示順変更
- 受講中の進捗表示順を AEGISC → 動画事例検討 → eラーニング に変更（mypage と統一）

#### UI デザイン統一
- 全セクション（実習先届出・実習修了報告・修了レポート・仮修了証明書発行・正式修了証明書・問い合わせ・変更届）を `.tcard` / `.thead` / `.thead-title` / `.thead-acts` 構造に統一
- 全テーブルからインラインスタイルを除去し、グローバル CSS（`table`・`thead tr`・`th`・`td`）を適用
- 受講生名表示を `<strong>` タグで統一し `a.name || ((a.sei||'')+(a.mei||''))` フォールバックを追加

#### グレー表示の廃止
- 実習先届出一覧：対応済み行の `opacity:0.65` を削除
- 仮修了証明書発行管理：発送済み行の `opacity:0.6` を削除

---

## v1.16.0 - 2026/05/23（構築１６）

### Firestore 定期バックアップ設定
- Google Cloud CLI（gcloud）インストール・初期設定
- Homebrew・Python 3.11インストール（CLOUDSDK_PYTHON設定）
- `gcloud firestore backups schedules create` で毎日バックアップ・7日間保持を設定

### admin.html ブラッシュアップ

#### 申込者進捗管理（モーダル内チェックリスト）
- ステータスに応じて「入金待ち」「受講確定」「受講中」の3ブロックを表示
- 各項目に ✓完了 / …確認中 / –未完了 のバッジ表示
- テキスト発注はtextbookフィールド（purchase / 購入（6,600円））で判定・購入者のみ表示

#### テキスト発注セクション新規追加
- サイドバーにメニュー追加（お知らせ管理と書類確認の間）
- 発注待ち件数をバッジ表示（ログイン時に自動取得）
- 一覧表示：受講番号・氏名・電話番号・発送先住所（altAddressがあれば優先表示・別途発送先ラベル付き）
- フィルター：発注待ち / すべて / 発注済み
- 「発注済みにする」ボタン・発注日時を記録（textOrderedAt）

#### サイドバーバッジ統一・追加
- 全バッジ色を #F06292（mypageお知らせと同色）に統一
- 新規バッジ追加：届出書・実習先届出・実習修了報告・修了レポート・正式修了証明書
- バッジ初期処理を initAllBadges() 関数に統合し onAuthStateChanged 後に実行（リロード時も即時表示）

#### テストデータ対応（isTest フィールド）
- isTest: true のデータを申込一覧・統計・CSV出力から除外
- 「🧪 テストデータを表示」チェックボックスで切替可能
- 進捗チェックリストにトグルボタン追加（isTestのみ表示）・ピンクの破線バナーで識別
- 対応フィールド：pledgeSigned・practiceVenueReported・taskAllDone・videoAllDone・externalVideoDone・test1Passed・test2Passed・tempCertIssued・ojtReported・reportSubmitted・certificateIssued

#### ロゴエリアデザイン変更
- ロゴエリア背景を薄グレー（#f7f7f7）に変更・グレーボーダーで区切り
- ADMINバッジをティール色（#1A9AB0）に変更
- サイドバー幅を220px → 240pxに拡大
- ロゴサイズ調整（height: 30px・max-width: 180px・object-fit: contain）

### index.js 振込先口座追加
- 広島信用金庫 八丁堀支店（普通 0592372 / 名義：トクヒ）ソーシャルケアグッド）を追加
- ゆうちょ銀行を2パターンに分けて案内
  - 他行からの振込：店名 五一八（ゴイチハチ）・普通 4637012 / 名義：ホリスケアアカデミー
  - ゆうちょからの振替：記号 15140・番号 46370121 / 名義：ホリスケアアカデミー
- BANK_INFO テンプレートと申込メール本文の両方を更新・デプロイ完了

### mypage_course_shonin.html
- 銀行振込モーダルの振込先を複数口座対応に更新（4口座表示）

---

## v1.15.2 - 2026/05/17（構築１５ 続き）

### mypage.html ヘッダー改善
- course画面と同様のヘッダーデザインに統一
- 氏名表示を削除
- スマホ：正方形ロゴ（logo_square.png）、PC：横長ロゴ（logo.png）に切り替え
- `header-name` 要素削除に伴うJSエラーを修正（null チェック追加）

### 申し込み確認メール 修正
- テキスト発送先の住所が郵便番号のみ表示されていた問題を修正
  - `address1` → `address` フィールドに修正
  - 管理者向け申し込み詳細メールの住所表示も同様に修正
- 既存ユーザーへのログイン方法案内を修正
  - 「初期パスワード」→「以前設定したパスワード」に変更（`isNewUser` フラグで分岐）

---

## v1.15.1 - 2026/05/17（構築１５ 続き）

### course画面 ヘッダー全面改善
- 氏名表示をヘッダーから削除
- ヘッダーを「← 戻る」｜ロゴ（中央）｜「ログアウト」のシンプルな3カラム構成に変更
- スマホ：正方形ロゴ（logo_square.png）を使用
- PC：横長ロゴ（logo.png）を使用
- CSSメディアクエリでロゴを自動切り替え
- `logo_square.png` を既存の `logo.png` から切り出して作成（58x62px）

### コースヘッダー 改善
- 受講番号の下に「お名前：〇〇〇〇」を追加
- 長い名前でも `word-break:break-all` で折り返し対応
- 東南アジアなど長い名前の受講生にも対応

### お知らせページネーション 改善
- ページ数が多い時に「…」で省略表示
- `flex-wrap:wrap` でボタンが崩れないよう修正
- 前へ・次へボタンのスタイルを統一

---

## v1.15.0 - 2026/05/17（構築１５）

### Stripe本番モード 修正
- 価格ID（受講料・テキスト代）を本番用に修正
- `cancel_url` を申し込み画面からcourse画面（`mypage_course_shonin.html?id=${docId}`）に変更
- `payment_method_types` を削除してStripe自動判定に変更（Apple Pay・Google Pay対応）

### お知らせフィルタリング 修正
- course画面のお知らせに `where('applicationId', '==', docId)` フィルタを追加
- Firestoreインデックスを作成（notices: applicationId + createdAt）
- `where` をFirestoreインポートに追加

### 申し込みメール 修正
- 既存ユーザーへのパスワード表示を `isNewUser` フラグで制御
- 既存ユーザーの場合はパスワードを表示しない

### 休講・退講・キャンセル時のアクセス制限
- `cancelled`・`suspended`・`withdrawn` の場合、course画面に「アクセスできません」メッセージを表示
- admin.htmlのステータス選択肢に「休講中」「退講」を追加
- mypage.htmlのステータスラベル・CSSバッジに「休講中」「退講」を追加
- 届出書承認時に種類に応じてステータスを自動変更
  - 休講届承認 → `status: suspended`・受講生にお知らせ送信
  - 退講届承認 → `status: withdrawn`・受講生にお知らせ送信
- 書類差し戻し時に `status: confirmed` に自動で戻す処理を追加

### 修了後の表示制限
- `status: completed` の場合、eラーニング・テスト・実習・届出書・受講ガイドセクションを非表示
- 修了メッセージをバナーに表示

### 次のステップバナー 改善
- 修了レポート提出後・確認中の状態でメッセージを追加
- 実習修了報告提出後・確認中の状態でメッセージを追加
- `ojtOk` かつ修了レポート未提出の状態でメッセージを追加
- 証明書発行済み時に「修了おめでとうございます！」メッセージを表示
- `pending`/`awaiting_payment` 時のターゲットを `payment-item` に修正

### 次のステップ ハイライト 実装
- セクションヘッダーへのバッジ表示を廃止
- 左ボーダー点滅アニメーション（`next-highlight` クラス）に変更
- STEP1：最初の未完了タスクにハイライト（入金待ち時はご入金の確認タスク）
- STEP2：eラーニング・動画・AEGISCカードにハイライト
- STEP3：対象テストカードにハイライト
- STEP4：対象タスク（実習修了報告・修了レポート・証明書発行）にハイライト

### STEP2解放条件 修正
- `isActive` 時もSTEP2を解放するよう修正
- 動画視聴カード・AEGISCカードも同様に修正
- `checkAllVideosWatched` を `videoAllDone: true` の場合はスキップ

### 正式修了証明書 送付先確認フロー 実装
- 修了レポートモーダルに「正式修了証明書の送付先確認」セクションを追加
- デフォルト選択：別途郵送先あり→別途郵送先、なし→自宅住所
- 選択肢：自宅住所・別途郵送先（入力がある場合のみ）・その他（新しい住所入力）
- 「その他」選択時：郵便番号入力で住所自動表示（zipcloud API）
- 送信時に `certDeliveryAddress` をFirestoreに保存
- admin.htmlの正式修了証明書発行メニューに「送付先」列を追加

### 修了レポートモーダル 改善
- textareaのフォントサイズを13px→16pxに変更（スマホ自動ズーム防止）
- ボタンエリアを下部固定（スクロール可能エリアとボタンを分離）
- ボタンのフォントサイズを12pxに縮小・`white-space:nowrap` 追加

### 実習修了報告モーダル 改善
- 入力フィールドのフォントサイズを14px→16pxに変更（スマホ自動ズーム防止）

### ご入金の確認タスク 改善
- 銀行振込の場合「お振込をお願いします」ラベルを表示
- クレジットカード・コンビニ払いの `pending` 状態でもクレジット決済ボタンを表示

### SFIタスクバッジ 修正
- 「対応中」バッジを削除（完了時のみ「完了」バッジを表示）

### お知らせ未読バッジ
- 色をオレンジ（#F57C00）→ ピンク（#F06292）に変更
- 未読が0になったらお知らせトグルを自動でclose

### ご質問・お問い合わせ 改善
- 内容入力テキストエリアのフォントサイズを16pxに変更
- 過去の問い合わせを最新3件表示・「もっと見る」ボタンで全件表示

### マイページ 修正
- お知らせセクションを非表示
- `statusColors` のスコープ問題を修正（`moreBtn.onclick` 内で参照できなかった問題）
- 「折りたたむ」ボタンを追加（すべて表示後に折りたたみ可能）
- 受講講座一覧の説明文を「受講中の講座をクリックして学習を開始できます」に変更

### 別途郵送先 表示修正
- `altAddress` フィールドで正しく表示するよう修正
- テキスト不要でも別途郵送先を表示（修了証明書の送付先としても使用）

### 学則・受講誓約書 ナビゲーションボタン
- スマホでフォントサイズを11pxに縮小（二行防止）
- `white-space:nowrap` 追加

---

## v1.14.0 - 2026/05/14〜16（構築１４）

### 受講ガイドセクション 実装
- course画面にお知らせの直後に「受講ガイド」セクションを追加
- 受講の流れ（STEP1〜4）をカード形式で表示
- 届出書の説明（変更届・休講届・退講届）を追加
- よくある質問（6項目）をアコーディオン形式で追加

### ご質問・お問い合わせセクション 実装
- course画面に「ご質問・お問い合わせ」セクションを追加（受講ステップの直後）
- 件名選択式（5カテゴリ）＋内容テキスト入力で送信
- 送信後：受講生にお知らせ送信・`inquiries` コレクションに保存
- 過去の問い合わせ・回答履歴を表示
- admin.html：「📬 問い合わせ」メニューを追加
  - 未回答件数バッジをサイドバーに表示
  - 返信ボックスから直接返信可能
  - 返信時：受講生のお知らせ＋`sendInquiryReplyMail` 送信
- Cloud Functions：`sendInquiryReplyMail` を追加・デプロイ
- Firestoreルール：`inquiries` コレクションのルールを追加

### Stripe本番モード切り替え完了
- `STRIPE_SECRET_KEY` を本番キーに更新（version 8）
- `STRIPE_WEBHOOK_SECRET` を本番用に設定
- 本番用価格ID（受講料 `price_1TQ1tb2HjMKbPXKpFoHCb6sY`・テキスト代 `price_1TQ1tX2HjMKbPXKpVxWICuiU`）を設定
- Cloud Functions（`stripeWebhook`・`createCheckoutSession`）を再デプロイ
- Stripeダッシュボードに本番Webhookエンドポイントを登録

### Firestoreセキュリティルール更新
- `inquiries` コレクションのルールを追加
- `notices` のフィルタリング修正（`where('applicationId', '==', docId)` を追加）
- Firestoreインデックスを作成（notices: applicationId + createdAt）
- `/{path=**}/todoke` collectionGroupルールを追加

### 修了レポート 下書き保存機能
- 「保存して中断」ボタンを追加
- Firestoreに `reportDraft` フィールドで保存
- 次回モーダルオープン時に下書きを読み込む

---

## v1.13.0 - 2026/05/13〜14（構築１３）

### 仮修了証明書 実装
- テストセクションに「仮修了証明書の発行（PDF）」タスクを追加
- テスト２合格前はロック表示・合格後に「発行する」ボタンが有効化
- 仮修了証明書モーダル：氏名・受講番号・仮修了日・有効期限（正式修了期限）を動的生成
- 発行元テキストの背面にseal.png（印影）を配置
- PDFダウンロード機能（html2canvas + jsPDF）
- 初回発行時：`tempCertIssued: true` を保存・お知らせ送信・`sendTempCertMail` 送信
- 発行済み後はボタンが「表示する」に変わる
- 仮修了証明書発行後にテストセクションをclose・実習セクションを解放
- Cloud Functions：`sendTempCertMail` を追加・デプロイ
- テスト２合格後、仮修了証明書発行まではテストトグルをopen維持

### 表記統一
- 「修了証」→「正式修了証明書」に統一（admin・course画面）
- 「仮修了証」→「仮修了証明書」に統一（全画面）
- 実習セクションSTEP4タイトルを「実習・正式修了証明書の郵送」に変更

### 受講内容セクション 日付修正
- 仮修了日：`test2PassedAt` から取得
- 修了日：`reportApprovedAt` から取得

### 次のステップバナー・バッジ 実装
- ページ上部に「次のステップ」バナーを常時表示
- 現在の状態に応じて適切なアクションを案内
- 進行中セクションのヘッダーに「👆 次はここ」バッジを点滅表示（CSSアニメーション）

### マイページ自動遷移
- 登録講座が1件のみの場合、ログイン後に自動的にcourse画面へ遷移

### パスワード関連改善
- course画面にパスワード変更セクションを追加（mypage経由不要に）
- パスワード変更フォームに注記追加（メモ・パスワードマネージャー推奨）
- ログイン画面のパスワードリセットリンク近くに注記追加
- `sendPasswordResetEmail` にリダイレクト先（mypage_login.html）を設定
- Google Cloud Console APIキーに `https://holiscare-academy.firebaseapp.com/*` を追加しパスワードリセットエラーを解消

### 申し込みメール改善
- ログイン手順（3ステップ）を申し込みメールに追加

### セクション順序変更
- 届出書セクションをパスワード変更セクションの前に移動

### 届出書 実装
- **変更届**：氏名（姓・名・せい・めい）・住所・郵送先住所・電話番号・メールアドレス変更希望を入力して送信
  - 氏名・住所変更時は本人確認書類提出モーダルを自動表示
  - Firestoreの `applications/{id}/todoke` サブコレクションに保存
- **休講届**：休講希望期間（開始日・終了日）・理由を入力して送信
  - 生徒番号・氏名・住所・電話番号を自動表示
- **退講届**：誓約文・確認チェックボックス2つで送信
  - 生徒番号・氏名・住所・電話番号を自動表示
  - 受講料返還請求なしの確認を2段階で実施
- 全届出：送信後にお知らせ送信・adminで確認可能

### admin.html 届出書メニュー追加
- サイドバーに「📝 届出書」メニューを追加
- 変更届・休講届・退講届を種類別に一覧表示
- 「対応済みにする」ボタン → `status: 'done'` に更新・受講生にお知らせ送信

### Firestoreルール更新
- `applications/{docId}/todoke/{todId}` サブコレクションのルールを追加
  - 本人・管理者のみ読み書き可能

---

## v1.12.0 - 2026/05/12（構築１２）

### 実習先届出 実装
- mypage_course_shonin.html：受講準備セクションに「介護実習先の有無届出」タスクを追加
- 届出モーダル実装：「届け出る施設がない（当校指定）」「届け出る施設がある」の分岐
- 施設情報入力フォーム：施設名・郵便番号・住所・電話番号・担当者部署・担当者氏名・届出理由
- 届出理由：現在勤務中・勤務予定（いつからか入力）・受け入れ同意済み・その他
- `practiceVenueReported: true` で届出完了（当校指定の場合も同様）
- admin.html：「実習先届出」メニューを追加（届け出る施設がある場合のみ表示）
- Firestoreインデックス作成（practiceVenueReported + practiceVenueReportedAt）

### active切り替え条件の変更
- 顔写真承認・本人確認書類承認・誓約書同意・実習先届出の4つが揃った時点でactive
- admin.html `approveDoc`・gakusoku.html 誓約書同意・mypage_course_shonin.html `submitVenue` の3箇所で判定
- `prevData` の管理を `__prevData` 変数に変更し、onSnapshot誤発火を修正

### 実習修了報告 実装
- mypage_course_shonin.html：STEP4に実習修了報告ボタンを追加
- 実習修了報告モーダル：実習施設名・実習期間・備考を入力して送信
- 送信時：`ojtReported: true` を保存・受講生にお知らせ送信
- admin.html：「実習修了報告」メニューを追加
- 承認時：`ojtPassed: true`・お知らせ送信・`sendOjtApprovedMail` 送信
- 差し戻し時：`ojtReported: false` に戻す・理由付きお知らせ・`sendOjtRejectedMail` 送信
- Cloud Functions：`sendOjtApprovedMail`・`sendOjtRejectedMail` を追加・デプロイ

### 実習セクション フロー変更（3タスク構成に）
- タスク①：実習修了報告（報告する→確認中→承認済み→完了）
- タスク②：修了レポート（記入する→確認中→承認済み→完了）
- タスク③：正式修了証の発行（ojtPassed + reportApproved 両方揃ったら発行待ち→完了）
- 各タスクに状態バッジを追加（完了・確認中・承認済み・発行待ち）
- ボタンを右側配置に統一

### 修了レポート 実装
- mypage_course_shonin.html：修了レポートモーダルを追加
- 設問5問（①尊厳・②利用者理解・③コミュニケーション・④安全と配慮・⑤今後の目標）
- 各設問100〜200文字・文字数カウンター付き（範囲外は赤表示）
- 送信時：`reportSubmitted: true` を保存・受講生にお知らせ送信
- admin.html：「修了レポート」メニューを追加
- レポート内容を全設問表示・文字数表示
- 承認時：`reportApproved: true`・`sendReportApprovedMail` 送信
- 差し戻し時：`reportSubmitted: false` に戻す・理由付きお知らせ送信
- Cloud Functions：`sendReportApprovedMail` を追加・デプロイ

### 正式修了証発行 フロー変更
- `ojtPassed: true` かつ `reportApproved: true` の両方が揃った時点で発行メニューに登場
- 両方承認が揃った時点で受講生に「全ての課程が完了」お知らせ＋`sendCertificateIssuedMail` 送信
- 発行済みボタン押下時：`certificateIssued: true`・`status: completed`・お知らせ送信

### gakusoku.html 学則・修了要件・誓約書の改訂
- 学則：3.研修名に（オンライン初任者研修）追加
- 学則：6.定員（10名）削除
- 学則：8.使用教材をeラーニング・動画教材に差し替え
- 学則：9.カリキュラム内容を差し替え
- 学則：11.イ・ウを差し替え（eラーニング・70%合格基準）
- 学則：12.認定方法を更新
- 学則：13.修了証交付を「介護施設などでの実習実施後」に変更
- 学則：17.修了期限を「仮修了3ヶ月・正式8か月」に更新
- 修了要件：リード文変更（1完了→受講開始日確定、2.3を3ヶ月以内、4を8ヶ月以内）
- 修了要件：フローをタイムライン形式に刷新（期限ラベル付き）
- 修了要件：必要書類に「介護実習先の有無届出」を追加
- 修了要件：学習カリキュラムをeラーニング・動画視聴の説明に差し替え
- 修了要件：「確認テスト」→「テスト」に変更
- 修了要件：OJT→実習・内容を差し替え（当校指定・勤務予定対応）
- 修了要件：修了期限セクションに受講開始日の定義を追加
- 誓約書：第一項「退学させられても」→「退学に至っても」
- switchTab未定義エラーを修正

### eラーニング・受講内容セクション改善
- 合格時の「次のテーマが解放されました！」メッセージを削除
- 順不同グループ採点対応（theme4・8・9）
- 正解番号の修正（theme1・theme9）
- 受講内容セクション：別途郵送先を常に表示（なしの場合は「（なし）」）
- 受講内容セクション：文字サイズを12px/1.6に統一

### edulio パスワード仕様変更
- ランダム生成から固定テーブル方式に変更（hca001→hca001aa 等、20パターン循環）
- admin.html の2箇所（saveStatus・approveDoc）を修正

### 受講準備セクション タスクバッジ追加
- 各タスクに完了・対応中バッジを追加
- 「学則・修了要件・誓約書を確認する」リンクを誓約書同意完了後のみ表示

---

## v1.11.0 - 2026/05/11（構築１１）

### eラーニング 採点・問題修正
- 合格時の「次のテーマが解放されました！」メッセージを削除
- 順不同グループ採点に対応
  - theme4：問題11-1〜11-4、11-5〜11-8
  - theme8：問題10-1〜10-5
  - theme9：問題2-2〜2-3
- theme1 問題1：正解を3→1に修正
- theme9 問題2-2：正解を1→4に、問題2-3：正解を4→8に修正
- 採点結果・復習画面の「Q1.」プレフィックスを削除

### 受講内容セクション 表示改善
- 「別途郵送先」を常に表示するよう変更（入力なしの場合は「（なし）」と表示）
- お勤め先・ご質問等・別途郵送先の値テキストサイズを `font-size:12px; line-height:1.6` に統一

---

## v1.10.0 - 2026/05/06（構築１０）

### テスト機能 実装
- テスト１（34問）・テスト２（40問）のJSON作成（context形式・語群削除）
- `quiz/test1.json` / `quiz/test2.json` を `academy/quiz/` に配置
- `openTestModal` をalertから本実装に置き換え
- テストモーダル実装：問題表示・選択・自動保存・採点・結果表示
- 合格時に `applications` の `test1Passed` / `test2Passed` を更新
- 正答率70%以上で合格、合格基準を結果画面に明示

### テスト・eラーニング 問題番号UI改善
- ドットナビゲーションを大問グループ対応に変更
- `buildGroups`（テスト用）・`buildQuizGroups`（eラーニング用）関数を実装
- 穴埋め問題のcontext冒頭・番号形式を統一
- 採点結果・復習モードの問題番号を統一

### テストセクション トグル制御
- `step2Done` かつ `test2Passed` 未完了の間、テストセクションのトグルを常にopen

### 外部動画（edulio）承認フロー
- ユーザーが「視聴完了を報告する」→ `externalVideoPending: true`
- admin.html の書類確認一覧に外部動画承認欄を追加
- 承認時：`externalVideoDone: true`・`sendEdulioApprovedMail` 送信
- 差し戻し時：`sendEdulioRejectedMail` 送信
- Cloud Functions に `sendEdulioApprovedMail`・`sendEdulioRejectedMail` を追加・デプロイ

### バグ修正
- `admin.html` の `saveStatus()` 内のブラケット不整合（SyntaxError）を修正
- `active` ステータス変更時に edulio ID が発行されない問題を修正
- `sendNotice` のエラーで後続のメール送信が止まっていた問題を修正（try/catch）

---

## v1.9.0 - 2026/05/05（構築９）

### 機能追加・変更
- eラーニングをステップ制からどのテーマからでも開始できる仕様に変更
- gakusoku.htmlをタブ式からステップ式（学則→修了要件→受講誓約書）に変更
- mypage_course_shonin.html：採点結果表示時に一番上にスクロール
- mypage_course_shonin.html：受講中かつeラーニング未完了→eラーニングトグル自動open
- mypage_course_shonin.html：受講中かつ動画教材未完了→動画教材トグル自動open
- mypage_course_shonin.html：confirmed状態→講座の流れトグル自動open
- mypage.html：お知らせをcourseと同じアコーディオン形式に統一
- mypage.html：受講講座一覧をセクションボックス化
- mypage.html：カードのステータスをcourse式カラーバッジに
- mypage.html：カードに受講開始日・仮修了期限・正式修了期限・仮修了日・修了日を追加
- 申し込みフローをクレジット案２に変更（保存→メール送信→完了画面→決済）
- ステータス表示統一（pending→入金待ち（振込）、awaiting_payment→入金待ち（カード））
- awaiting_payment状態のcourse画面対応（再決済ボタン・ロック表示）
- 振込情報モーダル新設（Step0「🏦 振込情報」ボタン）
- admin.html振込確認・saveStatus時にメール・お知らせ自動送信
- edulio ID・パスワード表示・コピーボタン・視聴完了報告ボタン追加
- active変更時にedulio IDを自動発行（hca001〜連番、counters/edulioで管理）

### セキュリティ
- admin.htmlのログインをinfo@holiscare.or.jpのみに制限

### Cloud Functions
- sendConfirmationEmailHttp 追加
- sendPledgeSignedMail 追加
- sendActiveNotifyMail 追加
- onApplicationDeleted トリガー追加

---

## v1.8.0 - 2026/05/04（構築８）

### eラーニング機能強化
- テーマ別クイズ（学習1〜10）実装
- 一問ずつ表示・前後移動・保存して中断ボタン追加
- 自動保存機能（Firestore quizProgress）
- 合格70%以上・不合格時は再挑戦可能
- 復習モード（合格後に正解一覧表示）
- 結果表示：ベストスコア保存・合格/不合格バッジ
- 穴埋め問題のcontext表示対応
- questions_r1〜r5_final.json → theme1〜10.json に変換・配置

---

## v1.7.0 - 2026/05/03（構築７）

### 外部動画（edulio）機能
- 外部動画セクション追加
- edulio視聴完了報告ボタン実装
- videoAllDoneフラグ管理

### テストセクション（準備）
- テストセクションのロックデザイン実装
- step2Done条件（taskAllDone && videoAllDone && externalVideoDone）設定

---

## v1.6.0 - 2026/05/02（構築６）

### eラーニング基盤
- quiz/theme1〜10.json 生成スクリプト作成
- task-modalのHTML・CSS実装
- openTask関数・closeTask関数実装
- FirestoreへのquizProgress保存

### mypage_course_shonin.html 大幅改修
- セクションをトグル化（eラーニング・動画教材・書類・テスト）
- 講座の流れStep 0〜4 スタイリッシュ版
- お知らせアコーディオン化・ページネーション
- 受講内容セクション全項目表示

---

## v1.5.0 - 2026/05/01（構築５）

### マイページ設計
- mypage.html TOP：お知らせ・受講講座一覧・パスワード変更
- mypage_course_shonin.html 詳細ページ新規作成
- 既存ユーザーも毎回パスワード再発行する仕様に変更

---

## v1.4.0 - 2026/04/30（構築４）

### 管理画面
- admin.html 管理画面作成
- 受講生一覧・ステータス管理
- 書類確認・承認フロー
- お知らせ送信機能

---

## v1.3.0 - 2026/04/29（構築３）

### Cloud Functions
- SendGridメール送信実装（APIキー設定・シークレット管理）
- onApplicationCreatedトリガー
- 申し込み確認メール・パスワード通知メール送信

---

## v1.2.0 - 2026/04/28（構築２）

### Firebase設定
- Firestore・Firebase Auth・Storage設定
- shonin_online_form.html 申し込みフォーム作成
- Stripe決済連携（createCheckoutSession）

---

## v1.1.0 - 2026/04/27（構築１）

### 初期構築
- GitHubリポジトリ作成（holiscare-academy / holiscare-functions）
- Firebase プロジェクト作成（holiscare-academy）
- GitHub Pages設定
- 基本HTMLページ作成
