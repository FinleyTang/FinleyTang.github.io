## Java 和 Kotlin 代码的基本查询

学习编写并运行一个简单的 CodeQL 查询。

学习如何使用 Visual Studio Code 中的 CodeQL 扩展来编写和运行一个简单的 CodeQL 查询。

有关安装 Visual Studio Code 的 CodeQL 扩展的信息，请参见“为 Visual Studio Code 安装 CodeQL”。

### 关于查询

我们将运行的查询搜索效率低下的空字符串测试。例如，如下 Java 代码：

```java
public class TestJava {
    void myJavaFun(String s) {
        boolean equals = s.equals("");
    }
}
```
或 Kotlin 代码：

```kotlin
fun myKotlinFun(s: String) {
    val equals = s.equals("")
}

```
在任一情况下，将 s.equals("") 替换为 s.isEmpty() 将更有效率。

### 寻找 CodeQL 数据库进行实验
在开始为 Java/Kotlin 代码编写查询之前，您需要一个 CodeQL 数据库来运行它们。最直接的方法是从 GitHub.com 直接下载使用 Java/Kotlin 的存储库的数据库。

- 在 Visual Studio Code 中，点击左侧边栏中的 QL 图标以显示 CodeQL 扩展。

- 点击 CodeQL 扩展顶部的 From GitHub 或 GitHub 徽标，以打开输入字段。

- 将存储库的 URL 复制到字段中，并按键盘 Enter 键。例如，https://github.com/apache/activemq 

- 可选地，如果存储库有多个 CodeQL 数据库可用，请选择 java 以下载由 Java/Kotlin 代码创建的数据库。

Visual Studio Code 右下角将显示数据库下载进度的信息。下载完成后，数据库将在 CodeQL 扩展的 Databases 部分显示一个勾号（见下面的截图）。