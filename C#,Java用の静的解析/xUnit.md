NuGet パッケージマネージャーコンソールで xunit をインストール
dotnet add package xunit
dotnet add package xunit.runner.visualstudio
dotnet add package Microsoft.NET.Test.Sdk
xUnitの基本構造
```c#
[Fact]
public void テスト名()
{
 // Arrange（準備）

 // Act（実行）

 // Assert（検証）
}
```
① テスト名は「何を期待するか」を書く
　例：ユーザーが有効な場合trueを返すこと
② Arrange / Act / Assert を崩さない
```c#
[Fact]
public void Add_ReturnsSum()
{    
    // Arrange    
    var calc = new Calculator();    
    // Act    
    var result = calc.Add(2, 3);    
    // Assert    
    Assert.Equal(5, result);}
```
よく使うAssert
```c#
Assert.Equal(expected, actual);
Assert.True(result);
Assert.False(result);
```
nullチェック
```c#
Assert.NotNull(obj);
Assert.Null(obj);
```
例外チェック
```c#
Assert.Throws<InvalidOperationException>(() =>
{
 service.DoSomething();
});
```