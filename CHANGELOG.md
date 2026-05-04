# CHANGELOG - ホリスケアアカデミー スクール管理システム

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
- mypage.html：パスワード変更をトグル化（デフォルトclose）
- mypage.html：カードのステータスをcourse式カラーバッジに
- mypage.html：カードに受講開始日・仮修了期限・正式修了期限・仮修了日・修了日を追加
- mypage.html：セクション幅をcourseと統一（max-width: 680px）
- mypage.html：statusLabelにactive追加

### セキュリティ
- admin.htmlのログインをinfo@holiscare.or.jpのみに制限

### バグ修正
- gakusoku.html：誓約書送信時のエラー処理改善（お知らせ送信失敗でもメイン処理に影響しないよう修正）

### データ設計
- notices作成時にapplicationIdを保存するよう変更（gakusoku.html・admin.html）
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
- 全セクションをトグル式に変更（開閉可能）
- 1列表示に統一（grid-2廃止）
- セクション順序変更：お知らせ→受講内容→講座の流れ→eラーニング→動画→書類発行→届出書
- お知らせをアコーディオン形式に（タイトル表示→クリックで本文展開）
- ページネーション・未読バッジ実装
- 講座の流れをStep 0〜4のスタイリッシュ版に（絵文字廃止）
- eラーニングを4ボタン常時表示（学習/結果/再挑戦/復習）
- 受講内容セクションに申込フォーム全項目を表示
- ステータスをセクションタイトル横にカラーバッジで表示
- faviconを全HTMLファイルに追加
- favicon角丸化（22%）

---

## v1.7.0 - 2026/05/02（構築８前半）

### eラーニング大改修
- 添削課題→eラーニングに名称変更
- 5回制→学習1〜10テーマ別に変更
- テーマ別JSONファイル（theme1.json〜theme10.json）生成スクリプト作成
- 問題番号ナビゲーション追加（タップで移動）
- 前の問題へ／次の問題へボタン並立
- 採点するボタンを全問題画面に常時表示
- 一括採点方式に変更（全問回答後に採点）
- 採点結果一覧表示（全問の正解・不正解）
- 保存して中断ボタン追加
- 穴埋め問題のcontext表示
- 合格の最高スコア保存機能（_bestキー）
- resultモード追加（過去の合格結果を表示）
- 不合格表示をステータスに追加
- 再挑戦は完全リセット
- 採点後のモーダル閉じでリロード

---

## v1.6.0 - 2026/05/01（構築７）

### eラーニングシステム設計・実装
- PDFから全5回×約130問のJSONデータ作成
- 問題形式を5択自動採点に統一（穴埋めも5択に変換）
- 70%合格制
- 合格後に次の回が解放される仕組み
- Firestoreに回答・進捗を保存
- 自動保存・途中再開機能
- 参照ページ表示
- 復習モード（正解一覧表示）
- 再挑戦機能

---

## v1.5.0 - 2026/04/30（構築６）

### バグ修正
- クレジットカード決済後のログイン問題修正
- パスワードリセットメールが即時期限切れになる問題の調査・修正
- マイページログイン後にリダイレクト処理を修正

### 機能追加
- mypage_course_shonin.htmlにURLパラメータ（?id=）受け取り処理追加

---

## v1.4.0 - 2026/04/29（構築５）

### お知らせ機能
- Firestoreのnoticesコレクション追加
- セキュリティルールにnotices書き込み権限追加
- admin.htmlにお知らせ投稿・一覧・削除機能追加
- mypage.htmlにお知らせ表示・既読管理・ページネーション追加
- mypage_course_shonin.htmlにお知らせ表示追加

---

## v1.3.0 - 2026/04/28（構築４）

### マイページ機能
- mypage_login.htmlにパスワードリセット機能追加
- mypage_course_shonin.htmlにステータス別表示制御追加
  - 入金待ち：動画・eラーニングをロック
  - 受講確定：書類提出・誓約書を解放
  - 受講中：全機能を解放
- クレジットカード決済フロー修正
- 確認画面（shonin_online_form.html）の改善

---

## v1.2.0 - 2026/04/27（構築３）

### フォーム・決済
- CORSエラー修正（cors: trueに変更）
- Stripeチェックアウトセッション作成機能の修正
- エラーバナー追加（画面上部に赤いエラー表示）
- 性別・生年月日のバリデーション赤枠表示
- 自動返信メールの送信者メールアドレス修正
- マイページURLの案内文修正（既存ユーザー対応）

---

## v1.1.0 - 2026/04/26（構築２）

### インフラ・環境構築
- SendGridのAPIキー更新（SG.kan4bzi...）
- FROM_EMAILの重複設定バグ修正
- Firebase Functionsデプロイ成功
- 受講番号自動発行（4010 + 連番4桁）
- Firebase Authアカウント自動作成
- 自動返信メール・管理者通知メール実装

---

## v1.0.0 - 2026/04/17（構築１）

### 初期構築
- Firebase プロジェクト設定（holiscare-academy）
- GitHub リポジトリ作成（academy / holiscare-functions）
- 申し込みフォーム作成（shonin_online_form.html）
  - 講座選択・テキスト・支払方法・個人情報・住所・別途郵送先
  - 入力確認→Stripe決済→完了フロー
- 管理者画面作成（admin.html）
  - 申込一覧・ステータス管理・書類確認
- マイページ作成（mypage.html / mypage_login.html / mypage_course_shonin.html）
- Cloud Functions初期実装（onApplicationCreated）
  - Firebase Authアカウント自動作成
  - 自動返信メール（SendGrid）
  - 管理者通知メール
- Stripe決済連携（createCheckoutSession / stripeWebhook）
- gakusoku.html（学則・修了要件・受講誓約書）
- payment_success.html（決済完了ページ）

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
