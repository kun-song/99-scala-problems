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

```Scala
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

```Scala
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

```Scala
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

## 05 (*) Reverse a list.

Example:

```Scala
scala> reverse(List(1, 1, 2, 3, 5, 8))
res0: List[Int] = List(8, 5, 3, 2, 1, 1)
```

简单递归：

```Scala
def reverse[A](xs: List[A]): List[A] =
  xs match {
    case Nil      ⇒ Nil
    case hd :: tl ⇒ reverse(tl) :+ hd
  }
```

尾递归：

```Scala
def reverse[A](xs: List[A]): List[A] = {
  @tailrec
  def aux(result: List[A], xs: List[A]): List[A] =
    xs match {
      case Nil      ⇒ result
      case hd :: tl ⇒ aux(hd :: result, tl)
    }

  aux(Nil, xs)
}
```

纯函数式（`fold`）：

```Scala
def reverse[A](xs: List[A]): List[A] =
  xs.foldLeft(List.empty[A])((l, x) ⇒ x :: l)
```

内置函数：

```Scala
def reverse[A](xs: List[A]): List[A] =
  xs.reverse
```

## 06 (*) Find out whether a list is a palindrome.

Example:

```Scala
scala> isPalindrome(List(1, 2, 3, 2, 1))
res0: Boolean = true
```

简单实现：

```Scala
def isPalindrome[A](xs: List[A]): Boolean =
  xs == xs.reverse
```

## 07 (**) Flatten a nested list structure.

Example:

```Scala
scala> flatten(List(List(1, 1), 2, List(3, List(5, 8))))
res0: List[Any] = List(1, 1, 2, 3, 5, 8)
```

使用 `flatMap`：

```Scala
def flatten(xs: List[Any]): List[Any] =
  xs.flatMap {
    case l: List[_] ⇒ l
    case x          ⇒ x :: Nil
  }
```

## 08 (**) Eliminate consecutive duplicates of list elements.

If a list contains repeated elements they should be replaced with a single copy of the element. The order of the elements should not be changed.

Example:

```Scala
scala> compress(List('a, 'a, 'a, 'a, 'b, 'c, 'c, 'a, 'a, 'd, 'e, 'e, 'e, 'e))
res0: List[Symbol] = List('a, 'b, 'c, 'a, 'd, 'e)
```

简单递归：

```Scala
def compress[A](xs: List[A]): List[A] =
  xs match {
    case Nil      ⇒ Nil
    case hd :: tl ⇒ {
      val ans = compress(tl)
      if (!ans.isEmpty && ans.head == hd) ans
      else hd :: ans
    }
  }

def compress[A](xs: List[A]): List[A] =
  xs match {
    case Nil      ⇒ Nil
    case hd :: tl ⇒ hd :: compress(xs.dropWhile(_ == hd))
  }
```

尾递归：

```Scala
def compress[A](xs: List[A]): List[A] = {
  def aux(result: List[A], xs: List[A]): List[A] =
    (result, xs) match {
      case (r, Nil)                   ⇒ r.reverse
      case (h :: t, x :: l) if h == x ⇒ aux(h :: t, l)
      case (r, x :: l)                ⇒ aux(x :: r, l)
    }

  aux(Nil, xs)
}

def compress[A](xs: List[A]): List[A] = {
  def aux(result: List[A], xs: List[A]): List[A] =
    (result, xs) match {
      case (r, Nil)     ⇒ r.reverse
      case (r, x :: l)  ⇒ aux(x :: r, l.dropWhile(_ == x))
    }

  aux(Nil, xs)
}
```

纯函数式：

```Scala
def compress[A](xs: List[A]): List[A] =
  xs.foldRight(List.empty[A]) {
    case (x, hd :: tl) if x == hd ⇒ hd :: tl
    case (x, r)                   ⇒ x :: r
  }
```

## 09 (**) Pack consecutive duplicates of list elements into sublists.

If a list contains repeated elements they should be placed in separate sublists.

Example:

```Scala
scala> pack(List('a, 'a, 'a, 'a, 'b, 'c, 'c, 'a, 'a, 'd, 'e, 'e, 'e, 'e))
res0: List[List[Symbol]] = List(List('a, 'a, 'a, 'a), List('b), List('c, 'c), List('a, 'a), List('d), List('e, 'e, 'e, 'e))
```

模式匹配：

```Scala
def pack[A](xs: List[A]): List[List[A]] =
  xs match {
    case Nil      ⇒ Nil
    case hd :: tl ⇒
      pack(tl) match {
        case t :: l if t.head == hd ⇒ (hd :: t) :: l
        case l                      ⇒ (hd :: Nil) :: l
      }
  }
```

使用 `fold`：

```Scala
def pack[A](xs: List[A]): List[List[A]] =
  xs.foldRight(List.empty[List[A]]) {
    case (x, l :: tl) if x == l.head ⇒ (x :: l) +: tl
    case (x, ll)                     ⇒ (x :: Nil) +: ll
  }
```

## 10 (*) Run-length encoding of a list.

Use the result of problem P09 to implement the so-called run-length encoding data compression method. Consecutive duplicates of elements are encoded as tuples (N, E) where N is the number of duplicates of the element E.

Example:

```Scala
scala> encode(List('a, 'a, 'a, 'a, 'b, 'c, 'c, 'a, 'a, 'd, 'e, 'e, 'e, 'e))
res0: List[(Int, Symbol)] = List((4,'a), (1,'b), (2,'c), (2,'a), (1,'d), (4,'e))
```

答案：

```Scala
def encode[A](xs: List[A]): List[(Int, A)] =
  pack(xs) map (l ⇒ (l.size, l.head))
```

## 11 (*) Modified run-length encoding.

Modify the result of problem P10 in such a way that if an element has no duplicates it is simply copied into the result list. Only elements with duplicates are transferred as (N, E) terms.

Example:

```Scala
scala> encodeModified(List('a, 'a, 'a, 'a, 'b, 'c, 'c, 'a, 'a, 'd, 'e, 'e, 'e, 'e))
res0: List[Any] = List((4,'a), 'b, (2,'c), (2,'a), 'd, (4,'e))
```

基于 `pack`：

```Scala
def encodeModified[A](xs: List[A]): List[Any] =
  pack(xs) map {
    case x :: Nil ⇒ x
    case l        ⇒ (l.size, l.head)
  }
```

基于 `encode`：

```Scala
def encodeModified[A](xs: List[A]): List[Any] =
  encode(xs) map {
    case l if l._1 == 1 ⇒ l._2
    case l              ⇒ l
  }
```

## 12 (**) Decode a run-length encoded list.

Given a run-length code list generated as specified in problem P10, construct its uncompressed version.

Example:

```Scala
scala> decode(List((4, 'a), (1, 'b), (2, 'c), (2, 'a), (1, 'd), (4, 'e)))
res0: List[Symbol] = List('a, 'a, 'a, 'a, 'b, 'c, 'c, 'a, 'a, 'd, 'e, 'e, 'e, 'e)
```

`flatMap` + tuple：

```Scala
def decode[A](xs: List[(Int, A)]): List[A] =
  xs flatMap (l ⇒ List.fill(l._1)(l._2))
```

`flatMap` + 偏函数：

```Scala
def decode[A](xs: List[(Int, A)]): List[A] =
  xs flatMap {
    case (n, x) ⇒ List.fill(n)(x)
  }
```

## 13 (**) Run-length encoding of a list (direct solution).

Implement the so-called run-length encoding data compression method directly. I.e. don't use other methods you've written (like P09's pack); do all the work directly.

Example:

```Scala
scala> encodeDirect(List('a, 'a, 'a, 'a, 'b, 'c, 'c, 'a, 'a, 'd, 'e, 'e, 'e, 'e))
res0: List[(Int, Symbol)] = List((4,'a), (1,'b), (2,'c), (2,'a), (1,'d), (4,'e))
```

答案：

```Scala
def encodeDirect[A](xs: List[A]): List[(Int, A)] =
  xs.foldRight(List.empty[(Int, A)]) {
    case (x, (n, h) :: tl) if x == h ⇒ (n + 1, x) :: tl
    case (x, l)                      ⇒ (1, x) :: l
  }
```

## 14 (*) Duplicate the elements of a list.

Example:

```Scala
scala> duplicate(List('a, 'b, 'c, 'c, 'd))
res0: List[Symbol] = List('a, 'a, 'b, 'b, 'c, 'c, 'c, 'c, 'd, 'd)
```

模式匹配：

```Scala
def duplicate[A](xs: List[A]): List[A] =
  xs match {
    case Nil  ⇒ Nil
    case hd :: tl ⇒ hd :: hd :: duplicate(tl)
  }
```

`fold`：

```Scala
def duplicate[A](xs: List[A]): List[A] =
  xs.foldRight(List.empty[A]) {
    case (x, l) ⇒ x :: x :: l
  }
```

`flatMap`：

```Scala
def duplicate[A](xs: List[A]): List[A] =
  xs flatMap {
    x ⇒ x :: x :: Nil
  }
```
