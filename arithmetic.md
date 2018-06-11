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

>质数（prime number）又称素数，有无限个。质数定义为在大于 1 的自然数中，除了 1 和它本身以外不再有其他因数。

```Scala
scala> 7.isPrime
res0: Boolean = true
```

`Stream`：

```Scala
class S99Int(value: Int) {
  def isPrime: Boolean = v > 1 && (2 to Math.sqrt(v).toInt).forall(v % _ != 0)
}
```

借助 `isPrime` 可以创建包含所有质数的 `Stream`：

```Scala
object S99Int {
  val primes = Stream.from(2).filter(_ isPrime)
}
```

## P32 (**) Determine the greatest common divisor of two positive integer numbers(Use Euclid's algorithm).

```Scala
scala> gcd(36, 63)
res0: Int = 9
```

将 `gcd` 定义在 `S99Int` 伴生对象中：

```Scala
def gcd(a: Int, b: Int): Int = if (b == 0) a else gcd(b, a % b)
```

## P33 (*) Determine whether two positive integer numbers are coprime.

Two numbers are coprime if their greatest common divisor equals 1.

```Scala
scala> 35.isCoprimeTo(64)
res0: Boolean = true
```

定义在 `S99Int` 类中：

```Scala
implicit class S99Int(v: Int) {
  import S99Int._

  def isCoprimeTo(that: Int): Boolean = gcd(v, that) == 1
}
```

## P34 (**) Calculate Euler's totient function phi(m).

Euler's so-called totient function phi(m) is defined as the number of positive integers r (1 <= r <= m) that are coprime to m.

```Scala
scala> 10.totient
res0: Int = 4
```

答案：

```Scala
def totient: Int = (1 to v).count(_ isCoprimeTo v)
```

## P35 (**) Determine the prime factors of a given positive integer.

Construct a flat list containing the prime factors in ascending order.

```Scala
scala> 315.primeFactors
res0: List[Int] = List(3, 3, 5, 7)
```


