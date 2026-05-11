# CHANGELOG - ホリスケアアカデミー スクール管理システム

---

## v1.11.0 - 2026/05/11（構築１１）

### eラーニング 採点・問題修正
- 合格時の「次のテーマが解放されました！」メッセージを削除
  （全テーマが最初から開放されているため不要）
- 順不同グループ採点に対応
  - theme4：問題11-1〜11-4、11-5〜11-8
  - theme8：問題10-1〜10-5
  - theme9：問題2-2〜2-3
  - グループ内の回答セットが正解セットと一致すれば全問正解
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
- 合格時「テスト２が解放されました！」メッセージ表示
- 正答率70%以上で合格、合格基準を結果画面に明示

### テスト・eラーニング 問題番号UI改善
- ドットナビゲーションを大問グループ対応に変更
  - 単独問題：`1`、`2`、`3`
  - 穴埋めグループ：`8-1`、`8-2`（PDF問題番号ベース）
- `buildGroups`（テスト用）・`buildQuizGroups`（eラーニング用）関数を実装
- 穴埋め問題のcontext冒頭を `【問題1-8】` → `【問題8】` に統一
- 単独問題の問題文冒頭に `【問題1】`〜 を追加
- context内の穴埋め番号を `（１）` → `（8-1）` 形式に統一
- questionの指示文を `（１）に入る言葉として` → `（8-1）に入る言葉として` に統一
- 採点結果・復習モードの問題番号を `Q1.` → `問題1.` / `問題8-1.` 形式に統一
- 各テーマの問題番号をPDF通し番号からテーマ内連番（1〜）に振り直し
- `quiz_fixed3/` スクリプトで theme1〜10.json を一括更新

### テストセクション トグル制御
- `step2Done`（eラーニング・動画すべて完了）かつ `test2Passed` 未完了の間、テストセクションのトグルを常にopen
- `test2Passed: true` 時もトグルopen維持

### Firestore フィールド確認・整理
- `taskAllDone`：eラーニング完了フラグ
- `videoAllDone`：自社動画完了フラグ（`internalVideoDone` への変更は見送り）
- `externalVideoDone`：外部動画完了フラグ
- 3つすべて `true` で `step2Done` が成立しテスト解放

### 外部動画（edulio）承認フロー
- ユーザーが「視聴完了を報告する」→ `externalVideoPending: true`
- admin.html の書類確認一覧に外部動画承認欄を追加（承認・差し戻し・承認取消）
- 承認時：`externalVideoDone: true` に更新 + ユーザーへメール送信（sendEdulioApprovedMail）
- 差し戻し時：理由をユーザーへメール送信（sendEdulioRejectedMail）
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
- mypage.html：パスワード変更をトグル化（デフォルトclose）
- mypage.html：カードのステータスをcourse式カラーバッジに
- mypage.html：カードに受講開始日・仮修了期限・正式修了期限・仮修了日・修了日を追加
- mypage.html：セクション幅をcourseと統一（max-width: 680px）
- mypage.html：statusLabelにactive追加
- 申し込みフローをクレジット案２に変更（保存→メール送信→完了画面→決済）
- ステータス表示統一（pending→入金待ち（振込）、awaiting_payment→入金待ち（カード））
- awaiting_payment状態のcourse画面対応（再決済ボタン・ロック表示）
- 銀行振込完了画面・payment_success.htmlにマイページへボタン追加
- 振込情報モーダル新設（Step0「🏦 振込情報」ボタン）
- admin.html振込確認・saveStatus時にメール・お知らせ自動送信
- 誓約書同意時のメール送信（sendPledgeSignedMail）
- edulio ID・パスワード表示・コピーボタン・視聴完了報告ボタン追加
- active変更時にedulio IDを自動発行（hca001〜連番、counters/edulioで管理）
- noticesにapplicationIdを保存・applicationId/applicationIds両方で削除対応

### セキュリティ
- admin.htmlのログインをinfo@holiscare.or.jpのみに制限

### バグ修正
- gakusoku.html：誓約書送信エラーの修正
- __serverTimestamp → __serverTs のバグ修正
- mypage.html：markReadのnullチェック追加

### Cloud Functions
- sendConfirmationEmailHttp 追加
- sendPledgeSignedMail 追加
- sendActiveNotifyMail 追加
- onApplicationDeleted トリガー追加
- onNoticeCreated 削除（不要な通知メールを廃止）

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

### UI改善
- 復習ボタン：オレンジ系グラデーション
- 再挑戦ボタン：明るいカラー
- 学習するボタン：ブルー系に統一

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
