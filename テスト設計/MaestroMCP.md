モバイルアプリ（Android/iOS）のUIテストツール
C#であれば以下の例で有効
- .NET MAUI アプリ
- Xamarinアプリ
- Unityアプリ（モバイル向け）
- C#製バックエンドと連携するモバイルアプリ

1. 環境構築
Java 17+ の確認

# openjdk 17以上が表示されればOK
```cmd
java --version
```

Maestroのインストール

# Maestro CLI をインストール
```cmd
curl -Ls "https://get.maestro.mobile.dev" | bash
```

# パスを通す（zsh の場合）
```cmd
echo 'export PATH="$PATH:$HOME/.maestro/bin"' >> ~/.zshrc
source ~/.zshrc
```

# インストール確認
```cmd
maestro --version
```

2. Claude DesktopでMCPを設定
{
  "mcpServers": {
    "maestro": {
      "command": "/Users/※YOUR_USERNAME/.maestro/bin/maestro",
      "args": ["mcp"]
    }
  }
}

3. Claudeに与えるプロンプト例

現在 iOSシミュレーターで起動しているアプリのログイン機能について、
Maestroを使ったE2Eテストを作成してください。

## テストシナリオ
### 正常系：ログイン成功
1. アプリを起動（状態クリア）
2. メールアドレス「test@example.com」を入力
3. パスワード「password123」を入力
4. ログインボタンをタップ
5. 「ようこそ！」が表示されることを確認

### 異常系：ログイン失敗
1. アプリを起動（状態クリア）
2. 誤ったメールアドレスを入力
3. 誤ったパスワードを入力
4. ログインボタンをタップ
5. エラーメッセージが表示されることを確認

まず現在の画面を確認してから、テストを生成してください。

---
YMLファイルがプロジェクトのディレクトリ配下、.maestroに格納される

4. テスト実行
  # 正常系テストを実行
  maestro test .maestro/login_success.yaml

  # 異常系テストを実行
  maestro test .maestro/login_failure.yaml

  # 両方のテストを実行
  maestro test .maestro/