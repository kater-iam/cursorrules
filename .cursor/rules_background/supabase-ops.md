# Supabaseの運用ルール背景

このドキュメントは[supabase-ops.mdc](./../rules/supabase-ops.mdc)のルールの背景を説明するものです。

## 1. 専用Terminalの使用
- 「supabase」という名前の専用Terminalを使用することで：
  - Supabase関連の操作履歴を一元管理
  - 他の開発作業との混在を防止
  - ログの追跡が容易に

## 2. バックグラウンド実行
- Supabaseのローカル操作をバックグラウンドで実行することで：
  - 他の開発作業を妨げない
  - ターミナルのブロックを防止
  - 長時間の操作も安全に実行可能

## 3. ローカル関数の起動方法
```bash
pkill -f \"supabase functions serve\" ; supabase functions serve > supabase/functions.log 2>&1 &
```
- このコマンドの利点：
  - 既存のプロセスを確実に終了
  - ログファイルへの出力で追跡が容易
  - エラー出力も含めて記録

## 4. デプロイの制限
- ローカル開発を優先することで：
  - 開発環境での十分なテストが可能
  - 本番環境への影響を防止
  - チーム内でのレビューを確実に実施

## 5. マイグレーションファイルの作成
マイグレーションには以下を含める必要があります：
```sql
-- 変更内容の説明
-- 作成者：名前
-- 作成日：YYYY-MM-DD

-- 変更内容
CREATE TABLE example (
    id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    name TEXT NOT NULL
);

-- ロールバック手順
-- DROP TABLE example;
```
- 初期バージョン（概ね v1.0 より前）については、既存のマイグレーションファイルを編集してまとめることで履歴の見通しが良くなる。

## 6. マイグレーションファイルの命名
- `YYYYMMDDHHMMSS_descriptions.sql` 形式の例：
  - `20240215143000_create_users_table.sql`
  - `20240215143500_add_profile_columns.sql`
- 時系列での管理が容易に
- 変更内容が名前から推測可能

## 7. タイムスタンプカラム
```sql
created_at TIMESTAMP WITH TIME ZONE DEFAULT (current_timestamp AT TIME ZONE 'JST' AT TIME ZONE 'Asia/Tokyo') NOT NULL,
updated_at TIMESTAMP WITH TIME ZONE DEFAULT (current_timestamp AT TIME ZONE 'JST' AT TIME ZONE 'Asia/Tokyo') NOT NULL
```
- JSTでの一貫した時刻管理
- 自動更新トリガーの設定

## 8. カラムコメント
```sql
-- 良い例
COMMENT ON COLUMN users.email IS 'ユーザーのメールアドレス（主要な連絡先として使用）';
COMMENT ON COLUMN profiles.user_id IS 'ユーザーID（usersテーブルの外部キー）';

-- 避けるべき例
COMMENT ON COLUMN users.email IS 'email address';
```

## 9. Dockerイメージの管理
```bash
# 新しいイメージ取得後の確認と削除
docker images | grep supabase
docker rmi [古いイメージID]
```
- ディスク容量の効率的な管理
- 不要なイメージの蓄積を防止
- システムリソースの最適化

## 10. psqlコマンドの利用
```bash
psql \"$(supabase status --output json | jq -r '.DB_URL')\"
```
- ローカルDBへの接続を容易に
- 環境変数や設定ファイルへの依存を排除 