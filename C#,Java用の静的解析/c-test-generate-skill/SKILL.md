---
name: c-test-generate-skill
description: >
  C#の実装に対して、xUnit用の適切なテストコードを作成するSKILL
---
---

name: c-test-generate-skill
description: >
C#実装に対してxUnitの単体テストを設計・作成する。
コード生成よりも先にテスト観点の抽出とレビューを行い、
保守性の高いテストコードを作成する。

------------------

# 目的
実装コードからテスト観点を抽出し、
* テストケースの抜け漏れ防止
* 境界値の網羅
* 例外処理の確認
* 分岐網羅
* 副作用の検証
を行ったうえでxUnitテストコードを作成する。

---

# 基本原則
テストコードを先に生成してはいけない。
必ず
1. 実装解析
2. テストケース設計
3. レビュー
4. テストコード生成
の順で進める。

---

# 全体フロー
```text
[実装解析]
↓
[テスト観点抽出]
↓
[テストケース作成]
↓
[レビュー用チェックリスト出力]
↓
[開発者承認]
↓
[テストコード生成]
↓
[テスト実行]
↓
[結果報告]
```
承認を得る前にテストコードを生成してはいけない。

---

# ステップ1 実装解析
対象コードを解析し以下を抽出する。
## メソッド一覧
* publicメソッド
* internalメソッド
* テスト対象メソッド

---

## 入力
* 引数
* 戻り値
* nullable有無

---

## 条件分岐
以下を全て列挙する。
```c#
if
else if
switch
switch expression
?:演算子
&&
||
```
例
```c#
if(age >= 20)
```
↓
```text
条件:
age >= 20

分岐:
true
false
```

---

## 例外
明示的または暗黙的に発生する例外を列挙する。
例
```csharp
throw new ArgumentNullException()
```
```csharp
int.Parse()
```
```csharp
First()
```
```csharp
Single()
```

---

## 外部依存
以下を検出する。
* Repository
* DbContext
* HttpClient
* API Client
* File
* Directory
* Environment
* DateTime.Now
* DateTime.UtcNow
* Guid.NewGuid
* Random
検出した場合はモック化方針を提示する。

---

# ステップ2 テスト観点抽出
以下の観点で確認する。
## 正常系
期待結果が返ること

---

## 異常系
不正入力時に適切な例外または結果となること

---

## 境界値
比較演算子ごとに境界値を抽出する。
### >
n-1
n
n+1
### >=
n-1
n
n+1
### <
n-1
n
n+1
### <=
n-1
n
n+1

---

範囲条件
```csharp
0 <= age <= 100
```
↓
```text
-1
0
1
99
100
101
```

---

## null
nullable引数がある場合
* null
を確認する。

---

## 文字列
以下を確認する。
* null
* ""
* " "
* 最大長
* 最大長超過

---

## コレクション
以下を確認する。
* null
* 空
* 1件
* 複数件

---

## Enum
以下を確認する。
* 全列挙値
* 定義外値

---

# ステップ3 テストケース作成
テストケースは表形式で出力する。
| ID | 分類 | 入力 | 期待値 | 理由 |
| -- | -- | -- | --- | -- |
例
|TC-001|正常|20|true|成人判定|
|TC-002|境界値|19|false|下限確認|
|TC-003|境界値|20|true|境界確認|
|TC-004|異常|null|ArgumentNullException|引数検証|

理由列は必須。

---

# ステップ4 分岐対応確認
実装中の全分岐について
| 分岐 | 対応TC |
| -- | ---- |

を作成する。

例
| 分岐              | 対応TC   |
| --------------- | ------ |
| age >= 20 true  | TC-001 |
| age >= 20 false | TC-002 |

未対応分岐がある場合はテストケースへ追加する。

---

# ステップ5 レビュー用チェックリスト
以下を出力する。
```text
□ 正常系
□ 異常系
□ 境界値
□ null
□ 空文字
□ 空白文字
□ 空コレクション
□ 分岐網羅
□ 例外
□ 戻り値
□ 副作用
□ Repository呼び出し
□ API呼び出し
□ ファイル操作
□ 日付依存処理
□ GUID依存処理
```
開発者の承認を得る。

---

# ステップ6 テストコード生成
xUnitを使用する。

---

## AAAパターン必須
```csharp
[Fact]
public void 正常系()
{
    // Arrange

    // Act

    // Assert
}
```

---

## Theory優先
複数入力パターンはTheoryを利用する。
```csharp
[Theory]
[InlineData(19,false)]
[InlineData(20,true)]
[InlineData(21,true)]
public void 成人判定(int age,bool expected)
{
    // Arrange

    // Act

    // Assert
}
```

---

## InlineData件数
5件を超える場合はMemberDataを利用する。

---

## 例外テスト
```csharp
[Fact]
public void Nullなら例外()
{
    // Arrange

    // Act

    // Assert
}
```

Assert.Throws<T>()を利用する。

---

## モック利用
外部依存はモック化する。
以下を検証する。
* 呼び出し回数
* 引数
* 戻り値

例
```csharp
mockRepository.Verify(
    x => x.Save(It.IsAny<User>()),
    Times.Once
);
```

---

# テスト不要項目
以下は原則テスト対象外とする。
* DTO
* Entity
* AutoProperty
* 単純なGetter
* 単純なSetter
* Mapperのみの処理
ただし業務ロジックを含む場合は対象とする。

---

# 完了条件
以下を全て満たした場合に完了とする。
* テストケース承認済み
* 全分岐に対応するテストケースあり
* テストコード生成済み
* テスト成功
* テスト結果報告済み

```
```