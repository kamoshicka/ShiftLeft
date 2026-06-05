C#版のコンパイル時に差し込むESLintのような静的解析エンジン
検知項目
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

# 導入方法
Microsoft公式を参照
https://learn.microsoft.com/ja-jp/dotnet/csharp/roslyn-sdk/tutorials/how-to-write-csharp-analyzer-code-fix