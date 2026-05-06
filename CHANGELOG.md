# CHANGELOG

## v1.6.0 - 2026/05/06（構築10）

### バグ修正
- `admin.html` 837行目の `SyntaxError: Missing catch or finally after try` を修正
  - `saveStatus()` 内の余分な `}` を削除
- 書類承認（顔写真・本人確認・誓約書の3点完了）による自動 `active` 化時に edulio ID が発行されていなかった問題を修正
- `sendNotice` のエラーで後続の fetch（メール送信）が止まっていた問題を修正（try/catch で囲む）

### 機能追加
- 外部動画（edulio）視聴完了報告フローを管理者承認制に変更
  - ユーザーが「視聴完了を報告する」→ `externalVideoPending: true` → 管理者が承認
  - admin.html の書類確認一覧に外部動画承認欄を追加（承認・差し戻し・承認取消）
  - 承認時：`externalVideoDone: true` に更新 + ユーザーへメール送信（sendEdulioApprovedMail）
  - 差し戻し時：理由をユーザーへメール送信（sendEdulioRejectedMail）
  - 承認済みボタンは薄く（opacity: 0.4）非アクティブ表示
- Cloud Functions に `sendEdulioApprovedMail`・`sendEdulioRejectedMail` を追加・デプロイ
- 申し込み確認メールにテキスト発送先（住所）をクレジットカード決済時も表示するよう修正

---

## v1.5.0 - 2026/05/06（構築９後半）

### バグ修正
- `saveStatus()` で `confirmed` → `active` に手動変更した際の edulio ID 自動発行が機能しない問題を修正
- 書類承認フローで余分な `}` によるブラケット不整合を修正

### 機能追加
- `active` ステータス変更時（手動・自動両方）に edulio ID・パスワードを自動発行
  - `counters/edulio` で連番管理（hca001, hca002...）
  - Firestore に `edulioId`・`edulioPassword`・`edulioIssuedAt` を保存

---

## v1.4.0 - 2026/05/02（構築９）

### 機能追加
- edulio（外部動画）ID・パスワード表示機能
  - mypage_course_shonin.html の動画教材セクションに表示
  - コピーボタン付き
  - 「edulioで視聴する →」ボタン
  - 「視聴完了を報告する」ボタン
- eラーニング学習1〜10（テーマ別）に変更
- 一括採点・前後移動・「保存して中断」ボタン追加
- mypage・course セクションのトグル化
- 講座の流れ Step 0〜4 スタイリッシュ版
- お知らせアコーディオン化・ページネーション
- 受講内容セクション全項目表示
- ステータス装飾（カラーバッジ）

### バグ修正
- 誓約書送信エラーの修正
- favicon 角丸化

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

---

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

---

## メール送信一覧
| タイミング | 送信先 | 関数 |
|---|---|---|
| 申し込み完了 | ユーザー・管理者 | onApplicationCreated |
| 受講確定（Stripe） | ユーザー | stripeWebhook |
| 受講確定（振込確認） | ユーザー | sendConfirmationEmailHttp |
| 受講開始（active） | ユーザー | sendActiveNotifyMail |
| 誓約書同意 | ユーザー | sendPledgeSignedMail |
| 顔写真承認 | ユーザー | sendDocApprovedMail |
| 本人確認承認 | ユーザー | sendDocApprovedMail |
| edulio視聴承認 | ユーザー | sendEdulioApprovedMail |
| edulio視聴差し戻し | ユーザー | sendEdulioRejectedMail |

---

## v1.3.0 - 2026/04/28（構築８）
- eラーニング自動保存・復習モード・再挑戦機能
- 参照ページ表示・問題番号ナビゲーション
- 「保存して中断」ボタン追加

## v1.2.0 - 2026/04/27（構築３〜７）
- CORSエラー修正・エラーバナー追加
- バリデーション赤枠表示・自動返信メール修正
- マイページ・ログイン機能実装
- ステータス別表示制御
- Firestore セキュリティルール（notices）追加
- eラーニング（添削課題）システム設計・実装
- 書類確認（顔写真・本人確認）承認フロー実装
- 振込入金確認ボタン・confirmBankPayment 実装

## v1.1.0 - 2026/04/26（構築２）
- Firebase Functionsデプロイ成功
- 受講番号自動発行（4010 + 連番4桁）
- Firebase Authアカウント自動作成・自動返信メール・管理者通知メール実装

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
- **外部動画：** edulio（https://tlp.edulio.com/aegisc/）
- **ホスティング：** GitHub Pages（belageapp.github.io/academy）
