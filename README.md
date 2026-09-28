# 『構文解析のしくみ』サポートリポジトリ
ZEN大学出版会から刊行された書籍『構文解析のしくみ』のサンプルコードを掲載しています。

## ディレクトリ構成

サンプルコードはすべて `code/` 以下に章ごとに置いています。各章は Gradle のサブプロジェクトで、`code/settings.gradle` でまとめて管理しています。

ディレクトリ名の番号は、書籍の章番号と一致していません。対応は次のとおりです。

| ディレクトリ | 書籍の章 |
|---|---|
| `code/ch02/` | 第3章「JSONの構文解析」 |
| `code/ch04/` | 第5章「構文解析アルゴリズム古今東西」 |
| `code/ch05/` | 第6章「構文解析器生成系の世界」 |

```
code/
├── settings.gradle   … 全サブプロジェクトの登録
├── gradlew           … ch02 の Gradle Wrapper を呼び出すスクリプト
├── ch02/             … 第3章「JSONの構文解析」
├── ch04/             … 第5章「構文解析アルゴリズム古今東西」
└── ch05/             … 第6章「構文解析器生成系の世界」
    ├── antlr/
    ├── javacc/
    ├── jcomb/
    ├── peg2java/
    └── yacc/
```

### ch02：第3章「JSONの構文解析」

パッケージ `parser`（`code/ch02/src/main/java/parser/`）に、手書きの JSON パーサを置いています。

| ファイル | 内容 |
|---|---|
| `JsonTokenizer.java` / `SimpleJsonTokenizer.java` / `Token.java` | 字句解析器のインタフェースとその実装、トークン |
| `JsonParser.java` | JSON パーサのインタフェース |
| `SimpleJsonParser.java` | 字句解析器が作ったトークン列を読む JSON パーサ |
| `PegJsonParser.java` | 字句解析器を使わず、文字列を直接読む PEG 風の JSON パーサ |
| `Ast.java` | 構文解析の結果として得られる抽象構文木 |
| `ParseResult.java` / `Pair.java` / `ParseException.java` / `TokenizerException.java` | 補助クラスと例外 |

### ch04：第5章「構文解析アルゴリズム古今東西」

`code/ch04/src/main/java/parser/` 以下に、アルゴリズムごとのパッケージを置いています。

| 場所 | 内容 |
|---|---|
| `Dyck.java` | Dyck 言語（括弧の対応）の再帰下降による認識器 |
| `DyckShiftReduce.java` / `Rule.java` / `Element.java` | 同じ言語のシフト還元による認識器と、そこで使う規則・記号の表現 |
| `ll1/` | LL(1) 認識器（`LL1Recognizer.java`） |
| `lr0/` | LR(0) 項集合と LR(0) 認識器（`LR0Recognizer.java`） |
| `slr1/` | SLR(1) パーサ（`SLR1Parser.java`） |

`ll1/` `lr0/` `slr1/` はそれぞれ独立しており、`Grammar.java` `Rule.java` などを重複して持っています。各パッケージの `Main.java` から実行例を試せます。

### ch05：第6章「構文解析器生成系の世界」

ツールや手法ごとにサブプロジェクトを分けています。

| サブプロジェクト | 内容 | 主なファイル |
|---|---|---|
| `antlr/` | ANTLR 4 による算術式と XML 風言語のパーサ | `src/main/antlr4/.../Expression.g4`、`LRExpression.g4`、`PetitXML.g4` |
| `javacc/` | JavaCC による電卓 | `src/main/java/.../calculator/Calculator.jj` |
| `jcomb/` | Java で書いたパーサーコンビネータライブラリ | `src/main/java/.../jcomb/JComb.java`、`JParser.java` |
| `peg2java/` | PEG から Java のパーサを生成する例 | `src/main/java/.../peg2java/PEG2Java.java`、`DyckParser.java` |
| `yacc/` | yacc と flex による電卓（C 言語） | `calculator.y`、`token.l`、`Makefile` |

ANTLR と JavaCC のパーサは、ビルド時に文法ファイルから生成されます（生成先は各サブプロジェクトの `build/generated/sources/` 以下）。

### テスト

各章のテストは、それぞれの `src/test/java/` 以下にあります（JUnit 5）。`yacc/` にはテストクラスはなく、`1 + 2 * 3` を入力して `Result: 7` が出力されることを確かめるスモークテストを `build.gradle` に定義しています。

## ビルドとテストの実行

Java 21 が必要です。`yacc/` のビルドには、さらに `yacc`（または `bison`）、`flex`、`gcc`、`make` が必要です。

```sh
cd code
./gradlew build                # 全章をビルドしてテストを実行
./gradlew :ch04:test           # 特定の章だけテストを実行
./gradlew :ch05:antlr:test     # ch05 のサブプロジェクトだけテストを実行
```
