# Working with lists

## 01 (*) Find the last element of a list.

Example:

```Scala
scala> last(List(1, 1, 2, 3, 5, 8))
res0: Int = 8
```

正规的函数式解法是使用递归，Scala 的模式匹配正好适合：

```Scala
def last[A](xs: List[A]): A =
  xs match {
    case Nil       ⇒ throw new NoSuchElementException
    case x :: Nil  ⇒ x
    case _ :: tl   ⇒ last(tl)
  }
```

内置函数：

```Scala
def last[A](xs: List[A]): A = xs.last
```

还有一种不使用模式匹配的递归做法，尽量避免，很丑陋：

```Scala
def last[A](xs: List[A]): A =
  if (xs.isEmpty)
    throw new NoSuchElementException
  else if (xs.tail.isEmpty)
    xs.head
  else
    last(xs.tail)
```

## 02 (*) Find the last but one element of a list.

Example:

```
scala> penultimate(List(1, 1, 2, 3, 5, 8))
res0: Int = 5
```

模式匹配：

```Scala
def penultimate[A](xs: List[A]): A =
  xs match {
    case Nil | _ :: Nil ⇒ throw new NoSuchElementException
    case x :: _ :: Nil  ⇒ x
    case _ :: tl        ⇒ penultimate(tl)
  }
```

内置函数 `init` 和 `last`：

```Scala
def penultimate[A](xs: List[A]): A = xs.init.last
```

## 03 (*) Find the Kth element of a list.

By convention, the first element in the list is element 0.

Example:

```
scala> nth(2, List(1, 1, 2, 3, 5, 8))
res0: Int = 2
```

模式匹配：

```Scala
def nth[A](n: Int, xs: List[A]): A =
  (n, xs) match {
    case (0, hd :: _) ⇒ hd
    case (k, _ :: tl) ⇒ nth(k - 1, tl)
    case _            ⇒ throw new NoSuchElementException
  }
```

内置函数 `drop` 和 `head`：

```Scala
def nth[A](n: Int, xs: List[A]): A =
  xs.drop(n).head
```

`List.apply` 函数：

```Scala
def nth[A](n: Int, xs: List[A]): A =
  xs(n)
```

## 04 (*) Find the number of elements of a list.

Example:

```
scala> length(List(1, 1, 2, 3, 5, 8))
res0: Int = 6
```

简单递归：

```Scala
def length[A](xs: List[A]): Int =
  xs match {
    case _ :: tl  ⇒ 1 + length(tl)
    case Nil      ⇒ 0
  }
```

尾递归：

```Scala
import scala.annotation.tailrec

def length[A](xs: List[A]): Int = {
  @tailrec
  def aux(n: Int, xs: List[A]): Int =
    xs match {
      case Nil     ⇒ n
      case _ :: tl ⇒ aux(n + 1, tl)
    }

  aux(0, xs)
}
```

更函数式的做法，使用 `fold`：

```Scala
def length[A](xs: List[A]): Int =
  xs.foldLeft(0)((n, _) ⇒ n + 1)
```

使用 `List.length` 函数：

```Scala
def length[A](xs: List[A]): Int =
  xs.length
```
