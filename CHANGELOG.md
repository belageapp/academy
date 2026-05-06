# CHANGELOG - ホリスケアアカデミー スクール管理システム

---

## v2.3.0 - 2026/05/06（構築１０完了）

### 全未解決項目を解決

#### activeへの自動遷移
- gakusoku.htmlの誓約書同意後に顔写真・本人確認が既に承認済みなら自動でactiveに変更
- 逆順（書類承認→誓約書同意）でもactiveになるよう対応

#### edulio ID自動発行
- adminでステータスをactiveに変更した時点でedulio IDを自動発行
- Firestoreのcounters/edulioで連番管理（hca001〜）
- Firestoreセキュリティルールにcountersの読み書き権限を追加

#### メール送信の修正
- FROM_EMAILに`|| 'info@holiscare.or.jp'`を全関数に追加（Invalid from email address エラー解消）
- 誓約書同意メール（sendPledgeSignedMail）が正常に届くよう修正
- 顔写真・本人確認書類承認時のメール送信を実装（sendDocApprovedMail）
- SendGrid APIキーをFull Accessに変更

#### お知らせのapplicationId修正
- sendNotice関数に第4引数`applicationId`を追加
- approveDoc・rejectDoc・受講開始通知全てに`docId`を渡すよう修正
- これによりapplication削除時のnotices自動削除が正常に動作

#### Firestoreセキュリティルール
- noticesの書き込みを認証済みユーザー全員に許可（gakusoku.htmlからの書き込み対応）
- countersの読み書きを認証済みユーザーに許可

#### その他修正
- 届出セクションをpending・awaiting_payment・confirmed時に表示
- adminのメニュー切り替え時に各セクションのデータをリロード
- serverTimestampのバグ修正（__serverTimestamp→__serverTs）

---

## v2.2.0 - 2026/05/05（構築１０前半）

### 動画教材セクション
- 自社動画・外部動画（edulio）の2種類に分割表示
- edulioログインID・パスワードの表示（コピーボタン付き）
- 「edulioで視聴する →」ボタン（別タブで開く）
- 「視聴完了を報告する」ボタン（Firestoreに記録）

### 受講確定通知（銀行振込）
- sendNotice関数にapplicationIdを追加

### 誓約書同意（gakusoku.html）
- 誓約書同意時のメール送信処理を追加
- Cloud FunctionsにsendPledgeSignedMail関数を追加

### admin.html
- showSection切り替え時に各セクションのデータをリロード
- serverTimestampのバグ修正

---

## v2.1.0 - 2026/05/05

### 申し込みフロー・決済
- 銀行振込完了画面の文面変更・マイページへボタン追加
- payment_success.htmlのタイトル変更・マイページへボタン追加
- ユーザーへのメールにお振込金額を明示
- メール内お振込金額を目立つデザインに変更

### 受講確定通知（銀行振込）
- admin.htmlの振込確認ボタン・saveStatus時にメール・お知らせ自動送信
- sendConfirmationEmailHttp関数をCloud Functionsに追加

### course画面改善
- 銀行振込pending時にStep0に「🏦 振込情報」ボタン追加・モーダル新設
- お知らせのアコーディオン開閉をclassベースに修正
- お知らせ未読バッジをmarkRead時に即時更新
- eラーニング・動画教材のロックメッセージを統一
- 講座の流れのopen/close制御（pending/awaiting_payment/confirmed→open、active/completed→close）

### Cloud Functions
- onNoticeCreated関数を削除
- sendConfirmationEmailHttp関数を追加

---

## v2.0.0 - 2026/05/05

### 申し込みフロー大改修（案２実装）
- クレジットカード決済フローを案２に変更
- クレジット用完了画面を新設
- Stripe決済画面を別ウィンドウで開くように変更
- ステータス表示統一（pending→入金待ち（振込）、awaiting_payment→入金待ち（カード））
- awaiting_payment状態のcourse画面対応
- Cloud Functions：webhookのステータスをpaid→confirmedに変更
- 申し込みフォームのlocalStorage自動保存
- 生年月日ピッカーのデザイン改善

---

## v1.9.0 - 2026/05/05

- eラーニングをどのテーマからでも開始できる仕様に変更
- gakusoku.htmlをタブ式からステップ式に変更
- mypage.html・mypage_course_shonin.htmlの大幅改修
- admin.htmlのセキュリティ強化・受講生選択改善
- onApplicationDeletedトリガー追加

---

## v1.8.0 - 2026/05/02

- mypage_course_shonin.htmlの全セクションをトグル式に変更
- 講座の流れをStep 0〜4スタイリッシュ版に
- eラーニングを4ボタン常時表示
- faviconを全HTMLファイルに追加・角丸化

---

## v1.7.0 - 2026/05/02

- eラーニング大改修（5回制→学習1〜10テーマ別）
- 一括採点・合格の最高スコア保存・resultモード追加

---

## v1.6.0 - 2026/05/01
- eラーニングシステム設計・実装

---

## v1.5.0 - 2026/04/30
- クレジットカード決済後のログイン問題修正

---

## v1.4.0 - 2026/04/29
- Firestoreのnoticesコレクション追加
- admin.htmlにお知らせ投稿・一覧・削除機能追加

---

## v1.3.0 - 2026/04/28
- mypage_login.htmlにパスワードリセット機能追加
- mypage_course_shonin.htmlにステータス別表示制御追加

---

## v1.2.0 - 2026/04/27
- CORSエラー修正・エラーバナー追加・バリデーション改善

---

## v1.1.0 - 2026/04/26
- Firebase Functionsデプロイ成功
- 受講番号自動発行・Firebase Auth自動作成・自動返信メール実装

---

## v1.0.0 - 2026/04/17
- Firebase プロジェクト設定・申し込みフォーム・管理者画面・マイページ作成
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
| 動画教材（自社） | ロック | ロック | ロック | 解放 | 解放 |
| 動画教材（edulio） | ロック | ロック | ロック | ID表示 | ID表示 |
| 書類発行（見積・請求） | 非アクティブ | 非アクティブ | アクティブ | アクティブ | アクティブ |
| 書類発行（領収書） | 非アクティブ | 非アクティブ | 非アクティブ | アクティブ | アクティブ |
| 届出書 | 表示（ボタン無効） | 表示（ボタン無効） | 表示 | 表示 | 表示 |

## メール送信一覧
| タイミング | 送信先 | 関数 |
|---|---|---|
| 申し込み完了 | ユーザー | onApplicationCreated |
| 申し込み完了 | 管理者 | onApplicationCreated |
| 受講確定（Stripe） | ユーザー | stripeWebhook |
| 受講確定（振込確認） | ユーザー | sendConfirmationEmailHttp |
| 受講確定（手動） | ユーザー | sendConfirmationEmailHttp |
| 誓約書同意 | ユーザー | sendPledgeSignedMail |
| 顔写真承認 | ユーザー | sendDocApprovedMail |
| 本人確認承認 | ユーザー | sendDocApprovedMail |

## 技術スタック
- **フロントエンド：** HTML / CSS / JavaScript（GitHub Pages）
- **バックエンド：** Firebase Cloud Functions（Node.js）
- **データベース：** Firestore
- **認証：** Firebase Authentication
- **ストレージ：** Firebase Storage
- **メール送信：** SendGrid
- **決済：** Stripe Checkout
- **外部動画：** edulio（https://tlp.edulio.com/aegisc/）
- **ホスティング：** GitHub Pages（belageapp.github.io/academy）
