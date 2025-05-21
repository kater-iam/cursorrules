# Supabaseのテストルール背景

このドキュメントは[supabase-tests.mdc](./../rules/supabase-tests.mdc)のルールの背景を説明するものです。

## 1. Supabaseクライアントの初期化設定
```typescript
const client = createClient(url, key, {
  auth: {
    persistSession: false,    // セッションの永続化を無効化
    autoRefreshToken: false,  // トークンの自動更新を無効化
    detectSessionInUrl: false // URLからのセッション検出を無効化
  }
});
```
これらの設定により、テスト間での状態の干渉を防ぎ、独立したテストの実行が可能になります。

## 2. クライアントの使い分け
```typescript
// 管理者クライアント（ユーザー作成用）
const adminClient = createClient(url, serviceRoleKey);
const { data: user } = await adminClient.auth.admin.createUser({...});

// 一般ユーザークライアント（テスト実行用）
const generalClient = createClient(url, anonKey);
const { data } = await generalClient.auth.signInWithPassword({...});
```
権限の明確な分離により、テストの意図が明確になります。

## 2-1. ユーザーのログイン検証
```typescript
// サービスロールは権限が強すぎるため、実際のユーザー権限のテストには使用しない
// 代わりに auth.signInWithPassword でログインしたクライアントを使用する
const userClient = createClient(url, anonKey, {
  auth: {
    persistSession: false,
    autoRefreshToken: false,
    detectSessionInUrl: false
  }
});

// 管理者としてログイン
const { data: adminData, error: adminError } = await userClient.auth.signInWithPassword({
  email: 'admin@example.com',
  password: 'password'
});

// 一般ユーザーとしてログイン
const { data: userData, error: userError } = await userClient.auth.signInWithPassword({
  email: 'user@example.com',
  password: 'password'
});

// ログインしたクライアントで操作を実行
const { data, error } = await userClient.from('table').select();
```
サービスロールは RLS をバイパスしてしまうため、実際のユーザー権限のテストには `auth.signInWithPassword` でログインしたクライアントを使用します。これにより、RLS ポリシーが正しく機能しているかを適切にテストできます。

## 3. RLSポリシーのテストパターン
エラーが返される場合：
```sql
REVOKE SELECT ON table_name FROM authenticated;
```
```typescript
expect(error).not.toBeNull();
expect(error.code).toBe("42501"); // 権限エラー
```

空配列が返される場合：
```sql
CREATE POLICY "policy_name" ON table_name FOR SELECT USING (false);
```
```typescript
expect(data).toHaveLength(0);
```

## 4. テストデータの一意性
```typescript
const testEmail = `test-${Date.now()}@example.com`;
const testCode = `TEST${Date.now().toString(36)}`;
```
タイムスタンプを利用することで、テストの並列実行時でもデータの衝突を防ぎます。

## 5. クリーンアップの実装
```typescript
afterAll(async () => {
  await adminClient.from("child_table").delete().eq("parent_id", parentId);
  await adminClient.from("parent_table").delete().eq("id", parentId);
  await adminClient.auth.admin.deleteUser(userId);
});
```
外部キー制約を考慮した順序でのクリーンアップが重要です。

## 6. タイムアウト設定
```typescript
describe("認証テスト", () => {
  jest.setTimeout(30000);
  // テストケース
});
```
ネットワーク遅延やデータベース処理時間を考慮した設定が必要です。

## 7. エラーコードの検証
```typescript
it("権限エラー", async () => {
  const { error } = await client.from("table").insert({...});
  expect(error.code).toBe("42501"); // Permission denied
});
```
具体的なエラーコードの検証により、エラーの原因を明確に特定できます。

## 8. トランザクション制御
```typescript
try {
  await supabase.rpc("begin");
  // テストコード
  await supabase.rpc("commit");
} catch (e) {
  await supabase.rpc("rollback");
  throw e;
}
```
テストの一貫性を保証し、副作用を防ぎます。

## 9. 環境変数の設定
```typescript
process.env.SUPABASE_URL = "http://localhost:54321";
process.env.SUPABASE_ANON_KEY = "your-anon-key";
process.env.SUPABASE_SERVICE_ROLE_KEY = "your-service-role-key";
```
テスト環境固有の設定を明示的に行います。

## 10. RLSテストの網羅性
```typescript
it("管理者アクセス", async () => {...});
it("一般ユーザーアクセス", async () => {...});
it("未認証アクセス", async () => {...});
```
すべての権限パターンをテストすることで、セキュリティを担保します。 