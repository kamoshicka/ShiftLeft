* jsやjQuery等で実装された機能関連

* 要件定義で文言が固定化できているページの文言

* レスポンシブ
PlaywrightでSS取得し差分比較
```javascript
await page.setViewportSize({ width: 375, height: 812 })
await expect(page).toHaveScreenshot()
```

* セキュリティ（wp-login）
Playwrightで以下へアクセスできないことを確認する
```javascript
/wp-login.php
/wp-admin
/xmlrpc.php
```

* 管理画面の露出
以下を巡回し、意図しない画面が表示されないことを確認する
```javascript
/admin
/administrator
/wp-admin
/login
/dashboard
```

* SEO設定
const title = await page.title()
確認項目：
titleタグ
description
og:image
canonical
h1タグ
robots

* アナリティクス
```javascript
expect(html).toContain("G-")
```
GA4タグやGTMコンテナの埋め込みを検査する

＊イベント設計やコンバーション設定は人が確認する必要あり

### 追加検討項目
リンク切れ（内部リンク・主要外部リンク）
画像の読み込み失敗（404画像の検出）
フォーム送信（問い合わせフォームや入力チェック）
Cookieバナー（表示・同意・拒否）
パンくずリスト
サイト内検索
404ページ（存在しないURLへのアクセス）
主要ページのHTTPステータス（200が返ること）
コンソールエラー（JavaScriptエラーが発生していないこと）
アクセシビリティ（Playwrightに加えて axe-core を利用）