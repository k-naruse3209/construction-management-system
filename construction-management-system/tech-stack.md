# 建設現場管理システム 技術仕様書

本書は README と requirements.md を基に、Vibe Coding 手法で開発を加速させるための推奨技術と進行計画を取りまとめたものである。

## 1. 開発前提
- Vibe Coding を活用し、要件整理 → モック → 実装を短サイクルで回す（README.md:28-33）。
- 建設現場向け案件・シフト・支払い管理を対象とし、従業員アプリと管理者 Web の両面を統合する（README.md:5-19）。
- 招待コード付き電話番号認証と役割ベースのアクセス制御をシステム全体で共通化する（construction-management-system/requirements.md:5-12）。

## 2. 推奨技術スタック
### 2.1 バックエンド基盤
- 言語/フレームワーク: TypeScript + Node.js (NestJS) — API と認可ロジックを統合し、電話番号 + 招待コード認証や役割分岐を実装しやすくする（construction-management-system/requirements.md:5-12）。
- データベース: PostgreSQL + Prisma もしくは TypeORM — 案件・マスタ・支払いデータを正規化し、DB 層でのバリデーションも担保する（construction-management-system/requirements.md:16, 35）。
- リアルタイム通信: WebSocket/SSE と Redis Pub/Sub — 案件更新やシフト締切の即時同期・通知を支える（README.md:21, construction-management-system/requirements.md:20, 31, 46）。
- バックグラウンド処理: BullMQ 等のジョブキュー — シフト締切リマインドや請求書生成を非同期化する（construction-management-system/requirements.md:26, 37）。

### 2.2 フロントエンド / モバイル
- 従業員アプリ: React Native または Flutter — シフト提出、ホーム表示、通知を単一コードベースで提供する（README.md:5, construction-management-system/requirements.md:24, 43-46）。
- 管理者 Web: Next.js + React (TypeScript) — 役割別ルーティングと高速 CSR/SSR を両立させる（README.md:11, construction-management-system/requirements.md:10, 28）。
- UI コンポーネント: AG Grid / Handsontable + TanStack Table — スプレッドシート風の案件管理とマスタ編集に対応する（README.md:12, construction-management-system/requirements.md:16, 19）。
- グラフ可視化: ECharts / Recharts — 売上・コストグラフやカスタム期間分析を実現する（construction-management-system/requirements.md:49-53）。
- 書類/データ出力: react-pdf / Puppeteer + Papaparse — 請求書 PDF と会計 CSV 出力を自動化する（construction-management-system/requirements.md:37, 52）。

### 2.3 業務連携 / 通知
- 認証基盤: Firebase Authentication + Twilio Verify または Amazon SNS — 電話番号ベースの招待認証と多要素対応を実現する（construction-management-system/requirements.md:5-12）。
- プッシュ通知: Firebase Cloud Messaging / APNs / FCM Topics — 従業員へのお知らせ・シフト更新通知を配信する（construction-management-system/requirements.md:26, 29, 45）。
- 会計連携: CSV スキーマ定義 + dbt / Airbyte — 会計ソフト連携用フォーマットを整備し、ETL を自動化する（README.md:18-19, construction-management-system/requirements.md:52-53）。
- インフラ: AWS (ECS Fargate + RDS + S3) または GCP (Cloud Run + Cloud SQL + GCS) — セキュリティとスケールを確保する（README.md:21-23, construction-management-system/requirements.md:59）。

### 2.4 Vibe Coding 支援体制
- デザイン統合: Figma → Design Tokens → Style Dictionary / shadcn UI — LLM 生成コードと UI を同期する（construction-management-system/requirements.md:54-58）。
- プロンプト管理: Cursor / Copilot / v0 + PromptLayer / OpenAI evals — 仕様 → モック → 実装の高速サイクルを構築する（README.md:28-33）。
- UI 検証: Storybook + Playwright Visual Regression / Chromatic — AI 生成コンポーネントの視覚回帰テストを自動化する（construction-management-system/requirements.md:56-58）。
- ナレッジ管理: Supabase pgvector / Weaviate — テキスト仕様 (EARS) を embedding 管理し、プロンプトへ自動反映する（construction-management-system/requirements.md:5-60）。

### 2.5 品質・運用
- CI/CD: GitHub Actions + Nx / Turborepo — Lint / UT / E2E / ビルドを並列最適化する（construction-management-system/requirements.md:60）。
- テスト: Jest / Vitest、Detox / Appium、Playwright — シフト提出や支払計算を含むユースケースを多層テストする（construction-management-system/requirements.md:24-39）。
- 監視: OpenTelemetry + Sentry / Datadog — 実行時トレーシングとエラー監視を統合する（construction-management-system/requirements.md:59-60）。
- インフラ自動化: Terraform / Pulumi — 設計フェーズから IaC モジュールを組み込み、再現性を担保する（README.md:32, construction-management-system/spec.json:1）。

## 3. 開発スケジュール（想定 4.5 か月）
1. **0.5 か月** — Vibe Coding テンプレート整備と設計ワークショップ（README.md:28-33）。
2. **1.0 か月** — アーキテクチャ設計、モノレポ雛形、CI プロトタイプ構築。
3. **1.5 か月** — コア機能実装（認証、案件/シフト管理、通知、支払い処理）。
4. **0.75 か月** — レポート・CSV/PDF 機能実装と結合テスト。
5. **0.75 か月** — E2E テスト、運用準備、リリース。

## 4. 見積条件（税別）
- 想定金額: 360 万円。
- 体制: 週 5 稼働の専任エンジニア 3 名（バックエンド / フロント / モバイル）。
- 提供物: Vibe Coding 用プロンプト環境、Figma デザイン資産、検証用デバイスは発注側が提供。
- 条件: 要件凍結後の追加機能は別途見積もりとし、スケジュール・コストに影響する可能性がある。

## 5. 次のアクション
1. Vibe Coding 用の設計テンプレートとプロンプトライブラリを作成し、仕様から設計への変換を自動化。
2. 推奨技術を前提としたベースアーキテクチャ図とモノレポ構成案（apps/api, apps/web, apps/mobile, packages/shared 等）を策定。
3. GitHub Actions による CI/CD パイプラインのプロトタイプ（Lint + Unit Test）を準備し、設計成果物の検証サイクルを確立。
