---
name: java-test-generate-skill
description: > 
  Java実装に対してJUnit5の単体テストを設計・作成する。
  テストコード生成前にテストケース設計とレビューを行い、
  保守性と網羅性を重視したテストコードを生成する。
------------------------

# 目的

Java実装から
* テスト観点抽出
* テストケース設計
* 分岐分析
* 境界値分析
* モック設計
を行い、JUnit5による単体テストコードを生成する。
コード生成よりも先にテスト設計を行うことを優先する。

---

# 基本原則

テストコードを先に生成してはいけない。
必ず以下の順序で進める。
1. 実装解析
2. 実装範囲確認
2. テスト観点抽出
3. テストケース設計
4. レビュー
5. テストコード生成
6. テスト実行

---

# 全体フロー
```text
[実装解析]
↓
[実装範囲確認]
[テスト観点抽出]
↓
[テストケース設計]
↓
[レビュー]
↓
[JUnitコード生成]
↓
[テスト実行]
↓
[結果報告]
```
開発者承認前にテストコードを生成しない。

---

# ステップ1 実装解析
以下を抽出する。

## テスト対象
* publicメソッド
* package-privateメソッド
* Serviceクラス
* Utilityクラス

---

## 入出力
* 引数
* 戻り値
* Optional
* Collection

---

## 条件分岐
以下を列挙する。
```java
if
else if
switch
switch expression
?:演算子
&&
||
```
全分岐について
* true
* false
両方を確認する。

---

## 例外

以下を検出する。
```java
throw
orElseThrow
Objects.requireNonNull
Integer.parseInt
Long.parseLong
Enum.valueOf
Optional.get
```
検出した例外ごとにテストケースを作成する。

---

## 外部依存

以下を検出する。
* Repository
* JdbcTemplate
* RestTemplate
* WebClient
* FeignClient
* File
* Path
* Clock
* UUID.randomUUID()
* LocalDateTime.now()
* LocalDate.now()
検出した場合はMockito利用を提案する。

---

# ステップ2 テスト観点抽出
以下を確認する。

## 正常系
期待結果が返却されること

---

## 異常系
不正入力時に適切な例外となること

---

## 境界値
比較演算子ごとに確認する。
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

## null

以下を確認する。

* null
* Optional.empty()

---

## String
以下を確認する。
* null
* ""
* " "
* 最大長
* 最大長超過

---

## Collection
以下を確認する。
* null
* empty
* 1件
* 複数件

---

## Enum
以下を確認する。
* 全列挙値
* 不正値

---

## Optional
以下を確認する。
* Optional.of(...)
* Optional.empty()

---

## Stream
以下を確認する。
* 0件
* 1件
* 複数件

---

# ステップ3 テストケース作成
表形式で出力する。
| ID | 分類 | 入力 | 期待値 | 理由 |
| -- | -- | -- | --- | -- |

例
|TC-001|正常|20|true|成人判定|
|TC-002|境界値|19|false|境界確認|
|TC-003|異常|null|NullPointerException|引数検証|
理由列は必須。

---

# ステップ4 分岐対応確認

全分岐について対応表を作成する。
| 分岐 | 対応TC |
| -- | ---- |
未対応分岐がある場合はテストケースへ追加する。

---

# ステップ5 レビュー用チェックリスト

```text
□ 正常系
□ 異常系
□ 境界値
□ null
□ Optional.empty
□ 空文字
□ 空白文字
□ 空コレクション
□ Enum不正値
□ Stream空データ
□ 分岐網羅
□ 例外
□ 戻り値
□ 副作用
□ Repository呼び出し
□ 外部API呼び出し
□ トランザクション
□ 日付依存処理
□ UUID依存処理
```

開発者の承認を得る。

---

# ステップ6 テストコード生成

## 使用ライブラリ
* JUnit5
* Mockito

---

## AAAパターン必須

```java
@Test
void 正常系() {

    // Arrange

    // Act

    // Assert
}
```

---

## ParameterizedTest優先

複数入力パターンはParameterizedTestを利用する。
```java
@ParameterizedTest
@CsvSource({
    "19,false",
    "20,true",
    "21,true"
})
void 成人判定(int age, boolean expected) {

    // Arrange

    // Act

    // Assert
}
```

---

## 例外テスト

```java
@Test
void nullなら例外() {

    // Arrange

    // Act

    // Assert
}
```
```java
assertThrows(
    IllegalArgumentException.class,
    () -> service.execute(null)
);
```

---

## Mockito

外部依存はMockitoでモック化する。
```java
verify(repository, times(1))
    .save(any());
```
確認対象
* 呼び出し回数
* 引数
* 戻り値

---

## Spring Bootの場合

以下を優先する。
```java
@ExtendWith(MockitoExtension.class)
```
原則として
```java
@SpringBootTest
```
は使用しない。
単体テストではSpringコンテナを起動しない。

---

# テスト不要項目

原則テスト対象外
* Entity
* DTO
* Lombok Getter
* Lombok Setter
* Record
* Mapperのみの処理
ただし業務ロジックを含む場合は除く。

---

# 完了条件

以下を全て満たすこと。
* テストケース承認済み
* 全分岐に対応するテストケースあり
* JUnitコード生成済み
* テスト成功
* 結果報告済み

```
```