# 3日目

## 拡張関数
Kotlin ではクラスを継承や委譲したりしなくても，クラスに新しい機能を追加して拡張することができる．次の例では `MutableList<Int>` に `swap` 関数を追加している:

```kt
fun MutableList<Int>.swap(index1: Int, index2: Int) {
    val tmp = this[index1]
    this[index1] = this[index2]
    this[index2] = tmp
}
```

拡張関数内での `this` キーワードは，レシーバオブジェクト（ドットの前に渡されたもの）に対応する．拡張関数はメソッドと同じように呼ぶことができる:

```kt
val l = mutableListOf(1, 2, 3)
l.swap(0, 2) // l == mutableListOf(3, 2, 1)
```

任意の `MutableList<T>` について[ジェネリクス](#ジェネリクス)を作ることができる:

```kt
fun <T> MutableList<T>.swap(index1: Int, index2: Int) {
    val tmp = this[index1]
    this[index1] = this[index2]
    this[index2] = tmp
}
```

## ジェネリクス
Kotlin ではテンプレートを使用できる

```kt
class Box<T>(t: T) {
    val value = t
}
```

このようなクラスを生成するためには，型引数を指定する必要がある:

```kt
val box = Box<Int>(1)
```

引数から推論できる場合は省略できる:

```kt
val box = Box(1)
```

テンプレートは関数にも適用できる:

```kt
fun <T> singletonList(item: T): List<T> {
    // ...
}

fun <T> T.basicToString(): String {
    // ...
}
```

## 高階関数とラムダ式
### 高階関数
高階関数は引数として関数を受け取るか，関数を返す関数である．`lock()` 関数がこの関数の良い例で，これは `lock` オブジェクトと関数を受け取り，lock をかけ，関数を実行して lock を解放する:

```kt
fun <T> lock(lock: Lock, body: () -> T): T {
    lock.lock()
    try {
        return body()
    } finally {
        lock.unlock()
    }
}
```

上記のコードでは `body` は関数型 `() -> T` を持つ．つまり，引数を持たず，型 `T` の値を返す関数である．`body` は `lock` によって保護されながら，`try` ブロック内で呼び出され，その結果は `lock()` 関数によって返される．

`lock()` を呼び出す場合は，引数として別の関数を渡すことができる:

```kt
fun toBeSynchronized() = sharedResource.operation()

val result = lock(lock, ::toBeSynchronized)
```

関数名の先頭に `::`（コロン2つ）を付けることで，関数を参照する．

多くの場合，[ラムダ式](#ラムダ式)を使うことでより便利になる:

```
val result = lock(lock, { sharedResource.operation() })
```

Kotlinでは，関数の最後の引数が関数である場合，その引数は括弧の外に指定することができる:

```
val result = lock(lock) {
    sharedResource.operation()
}
```

### ラムダ式
ラムダ式と無名関数は「関数リテラル」である．つまり，その関数は宣言されたのではなく，表現として即時に渡される．次の例を考える:

```kt
max(strings, { a, b -> a.length < b.length })
```

`max()` 関数は高階関数であり，2番目の引数に関数をとる．2番目の引数はそれ自体が関数である式，つまり，関数リテラルである．関数としては次と等価である:

```kt
fun compare(a: String, b: String): Boolean = a.length < b.length
```

#### 関数型
関数が引数として別の関数を受け入れられるようにするために，その引数の関数型を指定する必要がある．例えば，前述の `max()` 関数が次のように定義されているとする:

```kt
fun <T> max(collection: Collection<T>, less: (T, T) -> Boolean): T? {
    var max: T? = null
    for (it in collection) {
        if (max == null || less(max, it)) {
            max = it
        }
    }
    return max
}
```

引数 `less` は `(T, T) -> Boolean` 型，つまり，2つの `T` 型の引数をとり，前者が後者より小さければ `true` を返す関数である．

本体の4行目では `less` は型 `T` の引数を2つ受け取って，関数として呼び出されている．

#### ラムダ式の構文
ラムダ式，つまり関数リテラルの構文は次のようになる:

```kt
val sum = { x: Int, y: Int -> x + y }
```

ラムダ式は常に波括弧で囲まれ，引数宣言は括弧内にあり，本体は `->` の後に置かれる．必須でない注釈をすべて省略した場合，残っているものは次のようになる:

```kt
val sum: (Int, Int) -> Int = { x, y -> x + y }
```

ラムダ式がパラメータを1つだけ持っていることはよくある．Kotlin では唯一の引数の宣言を省略することができ，暗黙のうちにそれを `it` という名前で宣言する:

```kt
ints.filter { it > 0 } // このリテラルは `(it: Int) -> Boolean` 型
```

#### クロージャ
ラムダ式はその外側のスコープで宣言された変数にアクセスすることができる:

```kt
var sum = 0
ints.filter { it > 0 }.forEach {
    sum += it
}
```
