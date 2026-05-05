# CHANGELOG - ホリスケアアカデミー スクール管理システム

---

## v2.0.0 - 2026/05/05（構築９後半）

### 申し込みフロー大改修（案２実装）
- クレジットカード決済フローを案２に変更
  - 申し込み確定 → Firestoreに保存 → アカウント作成・ログイン情報メール送信 → 完了画面表示 → クレジット決済へ進むボタン
- クレジット用完了画面を新設（complete-screen-credit）
  - 「💳 クレジット決済へ進む」ボタン
  - 「後で決済する（マイページへ）」ボタン
- ブラウザのconfirmダイアログを廃止
- Stripe決済画面を別ウィンドウで開くように変更
- 別ウィンドウで開いた後はボタンを元の状態に戻す

### ステータス表示統一
- pending → 「入金待ち（振込）」
- awaiting_payment → 「入金待ち（カード）」
- admin.html・mypage.html・mypage_course_shonin.html全てで統一

### awaiting_payment状態のcourse画面対応
- 受講内容・ステータスの全項目を表示
- 講座の流れを受講中まで常にopen
- Step0に「💳 クレジット決済へ」ボタンを追加（マイページからの再決済）
- eラーニング・動画教材を「決済完了後に利用できます」ロック表示
- 書類発行：見積書・請求書はアクティブ、領収書のみ非アクティブ
- 届出書セクションを表示（提出ボタンは非アクティブ）

### Cloud Functions
- お申し込みお礼のお知らせを自動送信（onApplicationCreated）
- index.jsのURL修正（holiscare-academy → academy）
- createCheckoutSessionのreCAPTCHA検証をオプション化（マイページからの再決済対応）

### 申し込みフォーム改善
- 生年月日ピッカーのデザインをソフトに改善（画面中央・角丸・選択バー細め）
- 生年月日のlocalStorage自動保存追加
- フォーム全フィールドのlocalStorage自動保存（送信完了後に削除）
- プライバシーポリシーを新しいタブで開くよう変更（入力内容が消えない）

### その他修正
- 住所フィールドの表示修正
- bdp-itemのフォントウェイトを400（normal）に変更

---

## v1.9.0 - 2026/05/05（構築９前半）

### 機能追加・変更
- eラーニングをステップ制からどのテーマからでも開始できる仕様に変更
- gakusoku.htmlをタブ式からステップ式（学則→修了要件→受講誓約書）に変更
- mypage_course_shonin.html：採点結果表示時に一番上にスクロール
- mypage_course_shonin.html：受講中かつeラーニング未完了→eラーニングトグル自動open
- mypage_course_shonin.html：受講中かつ動画教材未完了→動画教材トグル自動open
- mypage_course_shonin.html：confirmed状態→講座の流れトグル自動open
- mypage.html：お知らせをcourseと同じアコーディオン形式に統一
- mypage.html：受講講座一覧をセクションボックス化
- mypage.html：パスワード変更をトグル化（デフォルトclose）
- mypage.html：カードのステータスをcourse式カラーバッジに
- mypage.html：カードに受講開始日・仮修了期限・正式修了期限・仮修了日・修了日を追加
- mypage.html：セクション幅をcourseと統一（max-width: 680px）
- mypage.html：statusLabelにactive追加

### セキュリティ
- admin.htmlのログインをinfo@holiscare.or.jpのみに制限

### バグ修正
- gakusoku.html：誓約書送信時のエラー処理改善

### データ設計
- notices作成時にapplicationIdを保存（gakusoku.html・admin.html）
- applicationId/applicationIds両方でnotices削除に対応
- admin.htmlの受講生選択をステータスフィルター・名前/メール検索付きに改善
- 「特定の受講生（メールアドレス入力）」を廃止し「受講生を選択」に統合
- mypage_course_shonin.html：noticesをapplication作成日以降のもののみ表示

### Cloud Functions
- onApplicationDeletedトリガー追加（application削除時に紐づくnoticesを自動削除）
- applicationIds配列にも対応

---

## v1.8.0 - 2026/05/02（構築８後半）

### セクション大改修（mypage_course_shonin.html）
- 全セクションをトグル式に変更
- 1列表示に統一
- お知らせをアコーディオン形式に・ページネーション・未読バッジ実装
- 講座の流れをStep 0〜4スタイリッシュ版に
- eラーニングを4ボタン常時表示（学習/結果/再挑戦/復習）
- 受講内容セクションに申込フォーム全項目を表示
- ステータスをカラーバッジで表示
- faviconを全HTMLファイルに追加・角丸化（22%）

---

## v1.7.0 - 2026/05/02（構築８前半）

### eラーニング大改修
- 添削課題→eラーニングに名称変更
- 5回制→学習1〜10テーマ別に変更
- 一括採点・前後移動・保存して中断
- 合格の最高スコア保存機能（_bestキー）
- resultモード追加

---

## v1.6.0 - 2026/05/01（構築７）

### eラーニングシステム設計・実装
- PDFから全5回×約130問のJSONデータ作成
- 70%合格制・合格後に次の回が解放
- Firestoreに回答・進捗を保存・自動保存・途中再開・復習モード実装

---

## v1.5.0 - 2026/04/30（構築６）
- クレジットカード決済後のログイン問題修正
- マイページリダイレクト処理修正

---

## v1.4.0 - 2026/04/29（構築５）
- Firestoreのnoticesコレクション追加・セキュリティルール設定
- admin.htmlにお知らせ投稿・一覧・削除機能追加
- mypage・courseにお知らせ表示・既読管理・ページネーション追加

---

## v1.3.0 - 2026/04/28（構築４）
- mypage_login.htmlにパスワードリセット機能追加
- mypage_course_shonin.htmlにステータス別表示制御追加
- クレジットカード決済フロー修正

---

## v1.2.0 - 2026/04/27（構築３）
- CORSエラー修正・エラーバナー追加
- 性別・生年月日のバリデーション赤枠表示
- 自動返信メールの送信者修正

---

## v1.1.0 - 2026/04/26（構築２）
- SendGridのAPIキー更新・Firebase Functionsデプロイ成功
- 受講番号自動発行（4010 + 連番4桁）
- Firebase Authアカウント自動作成・自動返信メール・管理者通知メール実装

---

## v1.0.0 - 2026/04/17（構築１）
- Firebase プロジェクト設定・GitHub リポジトリ作成
- 申し込みフォーム・管理者画面・マイページ作成
- Cloud Functions初期実装・Stripe決済連携

---

## 技術スタック
- **フロントエンド：** HTML / CSS / JavaScript（GitHub Pages）
- **バックエンド：** Firebase Cloud Functions（Node.js）
- **データベース：** Firestore
- **認証：** Firebase Authentication
- **ストレージ：** Firebase Storage
- **メール送信：** SendGrid
- **決済：** Stripe Checkout
- **ホスティング：** GitHub Pages（belageapp.github.io/academy）
