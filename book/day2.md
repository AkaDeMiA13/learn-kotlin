# 2日目

## 制御構文
### `if` 式
Kotlin では `if` は式であり，値を返す:

```kt
var max = a
if (a < b)
    max = b

// else付き
var max: Int
if (a > b)
    max = a
else
    max = b

// if式
val max = if (a > b) a else b
```

`if` の分岐はブロックにすることができ，最後の式がそのブロックの値になる:

```kt
val max = if (a > b) {
    println("Choose a")
    a
} else {
    println("Choose b")
    b
}
```

`if` を文ではなく式として使用するとき，その式には `else` 節が必要である．

`else if` で複数の分岐を作ることができる:

```kt
if (a > b) {
    println("a is larger than b")
} else if (a < b) {
    println("a is smaller than b")
} else {
    println("a equals to b")
}
```

### `when` 式
`when` は1つの変数に対して複数の分岐があるときに使われる:

```kt
when (x) {
    1 -> println("x == 1")
    2 -> println("x == 2")
    else -> { // ブロックも使える
        println("x is neither 1 nor 2")
    }
}
```

`if` と同様に式として使うことができ，式として使う場合，分岐条件が網羅できているとコンパイラが証明できない限り `else` 節は必須である．

複数の条件で同じ処理をすることもできる:

```kt
val y = when (x) {
    0, 1 -> 2 * x
    else -> 3 * x
}
```

`in` や `!in` を利用することで範囲をチェックすることができる:

```kt
when (x) {
    in 1..10 -> println("x is between 1 and 10")
    !in 10..20 -> println("x is not between 10 and 20")
    else -> println("x is between 10 and 20)
}
```

`when` は `if-else if` の連鎖を代替できる:

```kt
when {
    x.isOdd() -> println("x is odd")
    x.isEven() -> println("x is even")
    else -> println("x is funny")
}
```

### `For` ループ
`for` ループは繰り返し実行するときに使われる．構文は次の通り:

```kt
for (item in collection)
    foo()
```

本文をブロックにすることもできる:

```kt
for (i in 1..1000000) {
    // ...
}
```

配列をインデックス付きでループさせるときは次のようにする:

```kt
for (i in array.indeces) {
    println(array[i])
}
```

### `while` ループ
`while` 文は次の通り:

```kt
while (/* condition */) {
    // body
}
```

`while` 文は条件式が真である限り本文を実行し続ける．

`while` 文の他に `do while` 文も存在し，次の構文で書かれる:

```kt
do {
    // body
} while (/* condition */)
```

これは必ず最初の1回は本文を実行し，それ以外は `while` と同様である．

## クラス
Kotlin でのクラスは `class` キーワードを使って宣言される:

```kt
class Invoice {
}
```

クラスに本体がない場合は波括弧を省略できる:

```kt
class Empty
```

### コンストラクタ
Kotlin のクラスは，1つのプライマリコンストラクタと複数のセカンダリコンストラクタを持つことができる．プライマリコンストラクタはクラス名の後に続く:

```kt
class Person constructor(firstName: String) {
}
```

プライマリコンストラクタが修飾子などを持たない場合は `constructor` キーワードを省略できる:

```kt
class Person(firstName: String) {
}
```

プライマリコンストラクタには，いかなるコードも含むことはできない．代わりに，初期化コードは `init` キーワードが付いている初期化ブロック内に書くことができる:

```kt
class Customer(name: String) {
    init {
        logger.info("Customer initialized with value ${name}")
    }
}
```

プライマリコンストラクタの引数は初期化ブロックで使うことができ，クラス本体内で宣言されたプロパティの初期化処理に使うこともできる:

```kt
class Customer(name: String) {
    val customerKey = name.toUpperCase()
}
```

Kotlin にはプロパティの宣言と初期化をプライマリコンストラクタから行うための構文が存在する:

```kt
class Person(val firstName: String, val lastName: String, var age: Int) {
    // ...
}
```

プロパティはプライマリコンストラクタの中で可変値（`var`）または固定値（`val`）で宣言することができる．

#### セカンダリコンストラクタ
クラスは `constructor` キーワードを使ってセカンダリコンストラクタを宣言することができる:

```kt
class Person {
    constructor(parent: Person) {
        parent.children.add(this)
    }
}
```

クラスがプライマリコンストラクタを持つ場合，それぞれのセカンダリコンストラクタは直接または間接的にプライマリコンストラクタを呼ぶ必要がある．同クラスの別のコンストラクタへの委譲は `this` キーワードを使う:

```kt
class Person(val name: String) {
    constructor(name: String, parent: Person) : this(name) {
        parent.children.add(this)
    }
}
```

クラスがコンストラクタを持たない場合は，プライマリコンストラクタが引数なしで生成される．

#### クラスインスタンスの生成
クラスのインスタンスを生成するには，コンストラクタを普通の関数のように呼び出す:

```kt
val invoice = Invoice()
val customer = Customer("John Doe")
```

## 継承
Kotlin のすべてのクラスは共通の `Any` スーパークラスを持つ:

```kt
// Anyからの暗黙の継承
class Example
```

クラスヘッダ内のコロンの後に型を書くと，明示的にスーパータイプを宣言できる:

```kt
// openを付けることで継承できるようになる
open class Base(p: Int)

class Derived(p: Int) : Base(p)
```

スーパータイプを宣言したクラスがプライマリコンストラクタを持つ場合，その引数でスーパークラスを初期化しなければならない．

スーパータイプを宣言したクラスがプライマリコンストラクタを持たない場合，セカンダリコンストラクタはそれぞれスーパークラスを `super` キーワードを使って直接初期化するか，スーパークラスを初期化する他のコンストラクタを呼ばなければならない:

```kt
class MyView : View {
    constructor(ctx: Context) : super(ctx) {
    }

    constructor(ctx: Context, attrs: AttributeSet) : super(ctx, attrs) {
    }
}
```

### メンバのオーバーライド
次のようにクラスのメソッドをオーバーライドすることができる:

```kt
open class Base {
    open fun v() {}
    fun nv() {}
}
class Derived() : Base() {
    override fun v() {}
}
```

`override` 修飾子は `Derived.v()` のために必要である．`Base.nv()` のように `open` 修飾子が関数になければ，メソッドをサブクラス内で同じ名前で宣言することはできず，これは `override` の有無によらない．`open` 修飾子がないクラスの中では `open` メンバは禁止されている．
