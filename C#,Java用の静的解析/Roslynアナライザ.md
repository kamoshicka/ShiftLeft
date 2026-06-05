C#版のコンパイル時に差し込むESLintのような静的解析エンジン

# 検知項目
## ① 潜在バグ
null参照の可能性
使ってない変数
async/awaitのミス
## ② 危ない書き方
Dispose忘れ
非効率なLINQ
不要なボックス化
## ③ コーディング規約
名前規則（PascalCaseなど）
publicメソッドのコメント必須
フォーマット違反

# ルール設定方法
## .csproj
・ビルドルール
・厳しさ（Errorにするか）
・Analyzer有効化
```xml
<PropertyGroup>
  <TargetFramework>net8.0</TargetFramework>
  <!-- Nullable必須 -->
  <Nullable>enable</Nullable>
  <!-- 警告をエラー扱いにする -->
  <TreatWarningsAsErrors>true</TreatWarningsAsErrors>
  <!-- 分析レベル -->
  <AnalysisLevel>latest</AnalysisLevel>
</PropertyGroup>
```

## .editorconfig
ルール本体の設定
・命名規則
・null安全
・未使用コード
・コードスタイル
・複雑度ルール
・独自Analyzerルール
など
```ini
[*.cs]

# Nullable関連
dotnet_diagnostic.CS8618.severity = error
dotnet_diagnostic.CS8600.severity = warning
# 未使用コード
dotnet_diagnostic.CS0168.severity = error
# コードスタイル
dotnet_diagnostic.IDE0005.severity = warning
```

# ルールの方針
## Phase 1（初期）
→ Warning中心（CIは落としすぎない）
```xml
<TreatWarningsAsErrors>false</TreatWarningsAsErrors>
```


## Phase 2（安定後）
→ 重要ルールのみError化
```xml
<TreatWarningsAsErrors>true</TreatWarningsAsErrors>
```

## Phase 3（成熟後）
→ Warningほぼ禁止
Nullable関連（必須）
null参照系
未使用コード
明確なバグ検出系（possible null dereference）

# 導入方法
Microsoft公式を参照
https://learn.microsoft.com/ja-jp/dotnet/csharp/roslyn-sdk/tutorials/how-to-write-csharp-analyzer-code-fix


# 開発者に準備してもらうこと
1. .NET SDKバージョンの固定
global.json
```json
{
  "sdk": {
    "version": "8.0.1xx"
  }
}
```

2. Analyzerパッケージの明示追加
例：
```xml
<ItemGroup>
  <PackageReference Include="Microsoft.CodeAnalysis.NetAnalyzers" Version="8.0.0" PrivateAssets="all" />
</ItemGroup>
```

3. .editorconfigの統一
例：
```ini
[*.cs]
dotnet_diagnostic.CS8600.severity = warning
dotnet_diagnostic.CS8618.severity = error
```