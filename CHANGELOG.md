# CHANGELOG - ホリスケアアカデミー スクール管理システム

---

## v2.1.0 - 2026/05/05（構築９完了）

### 申し込みフロー・決済
- 銀行振込完了画面の文面変更（お申し込み内容・お振込金額・お振込先・ログイン情報を明記）
- 銀行振込完了画面に「マイページへ →」ボタン追加
- payment_success.html（クレジット決済完了画面）のタイトルを「お支払いが完了しました！」に変更
- payment_success.htmlに「マイページへ →」ボタン追加（docIdからURLを動的生成）
- ユーザーへのメールにお振込金額を明示（テキストあり：39,600円、なし：33,000円）
- メール内お振込金額を目立つデザインに変更

### 受講確定通知（銀行振込）
- admin.htmlの振込確認ボタン押下時にメール・お知らせを自動送信
- admin.htmlのsaveStatusでconfirmedに変更時もメール・お知らせを自動送信
- `sendConfirmationEmailHttp`関数をCloud Functionsに追加

### course画面改善
- 銀行振込pending時にStep0に「🏦 振込情報」ボタンを追加
- 振込情報モーダル（お振込金額・明細・お振込先）を新設
- お知らせのアコーディオン開閉をclassベースに修正
- お知らせ未読バッジをmarkRead時に即時更新
- eラーニング・動画教材のロックメッセージを「受講開始後に解放されます」に統一
- 講座の流れのopen/close制御：
  - pending・awaiting_payment・confirmed → 自動open
  - active・completed → デフォルトclose

### Cloud Functions
- `onNoticeCreated`関数を削除（不要な通知メールを廃止）
- `sendConfirmationEmailHttp`関数を追加

### mypage.html
- パスワードフォームを`<form>`タグで囲みブラウザ警告を解消
- markReadのnullチェックを追加

---

## v2.0.0 - 2026/05/05（構築９後半）

### 申し込みフロー大改修（案２実装）
- クレジットカード決済フローを案２に変更
  - 申し込み確定 → Firestoreに保存 → アカウント作成・ログイン情報メール送信 → 完了画面表示 → クレジット決済へ進むボタン
- クレジット用完了画面を新設（complete-screen-credit）
- ブラウザのconfirmダイアログを廃止・カスタム完了画面に
- Stripe決済画面を別ウィンドウで開くように変更
- 別ウィンドウで開いた後はボタンを元の状態に戻す

### ステータス表示統一
- pending → 「入金待ち（振込）」
- awaiting_payment → 「入金待ち（カード）」
- admin.html・mypage.html・mypage_course_shonin.html全てで統一

### awaiting_payment状態のcourse画面対応
- 受講内容・ステータスの全項目を表示
- 講座の流れを受講中まで常にopen
- Step0に「💳 クレジット決済へ」ボタンを追加
- eラーニング・動画教材を「受講開始後に利用できます」ロック表示
- 書類発行：見積書・請求書はアクティブ、領収書のみ非アクティブ
- 届出書セクションを表示（提出ボタンは非アクティブ）

### Cloud Functions
- お申し込みお礼のお知らせを自動送信（onApplicationCreated）
- 受講確定お知らせを自動送信（stripeWebhook）
- index.jsのURL修正（holiscare-academy → academy）
- createCheckoutSessionのreCAPTCHA検証をオプション化
- webhookのステータスをpaid→confirmedに変更

### 申し込みフォーム改善
- 生年月日ピッカーのデザインをソフトに改善
- 生年月日のlocalStorage自動保存追加
- フォーム全フィールドのlocalStorage自動保存
- プライバシーポリシーを新しいタブで開くよう変更

---

## v1.9.0 - 2026/05/05（構築９前半）

### 機能追加・変更
- eラーニングをどのテーマからでも開始できる仕様に変更
- gakusoku.htmlをタブ式からステップ式に変更
- mypage_course_shonin.html：採点結果表示時に一番上にスクロール
- mypage_course_shonin.html：受講中かつeラーニング未完了→自動open
- mypage_course_shonin.html：受講中かつ動画教材未完了→自動open
- mypage_course_shonin.html：confirmed状態→講座の流れ自動open
- mypage.html：お知らせをcourseと同じアコーディオン形式に統一
- mypage.html：受講講座一覧をセクションボックス化
- mypage.html：パスワード変更をトグル化（デフォルトclose）
- mypage.html：カードのステータスをcourse式カラーバッジに
- mypage.html：カードに受講開始日・期限・修了日を追加
- mypage.html：セクション幅をcourseと統一（max-width: 680px）

### セキュリティ
- admin.htmlのログインをinfo@holiscare.or.jpのみに制限

### データ設計
- notices作成時にapplicationIdを保存
- applicationId/applicationIds両方でnotices削除に対応
- admin.htmlの受講生選択をステータスフィルター・名前/メール検索付きに改善
- 「特定の受講生」を廃止し「受講生を選択」に統合
- mypage_course_shonin.html：noticesをapplication作成日以降のもののみ表示

### Cloud Functions
- onApplicationDeletedトリガー追加

---

## v1.8.0 - 2026/05/02（構築８後半）

### セクション大改修（mypage_course_shonin.html）
- 全セクションをトグル式に変更
- お知らせをアコーディオン形式に・ページネーション・未読バッジ実装
- 講座の流れをStep 0〜4スタイリッシュ版に
- eラーニングを4ボタン常時表示（学習/結果/再挑戦/復習）
- 受講内容セクションに申込フォーム全項目を表示
- ステータスをカラーバッジで表示
- faviconを全HTMLファイルに追加・角丸化（22%）

---

## v1.7.0 - 2026/05/02（構築８前半）

### eラーニング大改修
- 5回制→学習1〜10テーマ別に変更
- 一括採点・前後移動・保存して中断
- 合格の最高スコア保存機能（_bestキー）
- resultモード追加

---

## v1.6.0 - 2026/05/01（構築７）
- PDFから全5回×約130問のJSONデータ作成
- 70%合格制・Firestoreに進捗保存・自動保存・途中再開・復習モード

---

## v1.5.0 - 2026/04/30（構築６）
- クレジットカード決済後のログイン問題修正
- マイページリダイレクト処理修正

---

## v1.4.0 - 2026/04/29（構築５）
- Firestoreのnoticesコレクション追加
- admin.htmlにお知らせ投稿・一覧・削除機能追加
- mypage・courseにお知らせ表示・既読管理・ページネーション追加

---

## v1.3.0 - 2026/04/28（構築４）
- mypage_login.htmlにパスワードリセット機能追加
- mypage_course_shonin.htmlにステータス別表示制御追加

---

## v1.2.0 - 2026/04/27（構築３）
- CORSエラー修正・エラーバナー追加
- バリデーション赤枠表示・自動返信メール修正

---

## v1.1.0 - 2026/04/26（構築２）
- Firebase Functionsデプロイ成功
- 受講番号自動発行（4010 + 連番4桁）
- Firebase Authアカウント自動作成・自動返信メール・管理者通知メール実装

---

## v1.0.0 - 2026/04/17（構築１）
- Firebase プロジェクト設定・GitHub リポジトリ作成
- 申し込みフォーム・管理者画面・マイページ作成
- Cloud Functions初期実装・Stripe決済連携

---

## ステータス定義
| 値 | 表示名 | 意味 |
|---|---|---|
| `pending` | 入金待ち（振込） | 銀行振込申込後 |
| `awaiting_payment` | 入金待ち（カード） | クレジットカード申込後・決済前 |
| `confirmed` | 受講確定 | 入金確認済み |
| `active` | 受講中 | 受講開始後 |
| `completed` | 修了 | 全課程修了 |
| `cancelled` | キャンセル | キャンセル済み |

## course画面のステータス別表示
| セクション | pending | awaiting_payment | confirmed | active | completed |
|---|---|---|---|---|---|
| 講座の流れ | 自動open | 自動open | 自動open | close | close |
| eラーニング | ロック | ロック | ロック | 解放 | 解放 |
| 動画教材 | ロック | ロック | ロック | 解放 | 解放 |
| 書類発行（見積・請求） | 非アクティブ | 非アクティブ | アクティブ | アクティブ | アクティブ |
| 書類発行（領収書） | 非アクティブ | 非アクティブ | 非アクティブ | アクティブ | アクティブ |
| 届出書 | 非表示 | 表示（ボタン無効） | 表示 | 表示 | 表示 |

## 技術スタック
- **フロントエンド：** HTML / CSS / JavaScript（GitHub Pages）
- **バックエンド：** Firebase Cloud Functions（Node.js）
- **データベース：** Firestore
- **認証：** Firebase Authentication
- **ストレージ：** Firebase Storage
- **メール送信：** SendGrid
- **決済：** Stripe Checkout
- **ホスティング：** GitHub Pages（belageapp.github.io/academy）
