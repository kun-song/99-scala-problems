# Arithmetic

本部分需要定义 `S99Int` 类，以及从普通 `Int` 到 `S99Int` 的隐式转换。

可以用 `implicit class` 定义 `S99Int`：

```Scala
implicit class S99Int(value: Int) {
  def isPrime: Boolean = true
}
```

也可以用 `implicit conversion function` 定义 `S99Int`：

```Scala
class S99Int(value: Int) {
  def isPrime: Boolean = true
}

object S99Int {
  implicit def int2S99Int(n: Int): S99Int = new S99Int(n)
}
```

## P31 (**) Determine whether a given integer number is prime.

```Scala
scala> 7.isPrime
res0: Boolean = true
```

`Stream`：

```Scala
class S99Int(value: Int) {
  def isPrime: Boolean =
    (Stream from 2) take Math.sqrt(value).toInt forall (value % _ != 0)
}
```


