# State monad

Imagine you have some reach domain object with some state and logic:

```scala
final class Wallet{
    private var _amount:BigDecimal = BigDecimal("0")

    def amount = _amount

    def deposit(x: BigDecimal): BigDecimal = {
      _amount += x
      x
    }

    def withdraw(x: BigDecimal): BigDecimal = {
      _amount -= x
      x
    }
} 
```

> here I remove all checks and error handling in order not to increase complexity, but in prodution code you should use Either, or smth else

Not we wanna to make this code more functional and pure, so

```scala
final case class Wallet(amount: BigDecimal = BigDecimal("0")){
    def deposit(x: BigDecimal): (Wallet, BigDecimal) = {      
      (this.copy(amount + x), x)
    }

    def withdraw(x: BigDecimal): (Wallet, BigDecimal) = {
      (this.copy(amount - x), x)
    }
}
```

Ok, but now your API client become suffer from pair deconstruction on every breath:
```scala
val w = Wallet()
  val (wallet, _) = w.deposit(BigDecimal("1000"))
  println(wallet.amount)
  val (wallet1, forIceCream) = wallet.withdraw(BigDecimal("5"))
  println(wallet1.amount)
  val (wallet2, forShampoo) = wallet1.withdraw(BigDecimal("10"))
  println(wallet2.amount)
  val (wallet3, forCar) = wallet2.withdraw(BigDecimal("900"))
  println(wallet3.amount)
```

Maybe there are some trick to avoid all this deconstructioning? At first we introduce some type alias for all statefull functions like `Wallet => (Wallet, Result)`
```scala
type State[S, A] = S => (S,A)
``` 
so we can define out deposit as
```scala
val deposit:State[Wallet, BigDecimal] = _.deposit(BigDecimal("1000"))
```
next step is to implement [Monad](monad.md) [typeclass](typeclass.md) instance for State
```scala
final object Monad{
  implicit def StateM[S]: Monad[State[S, ?]] = new Monad[State[S, ?]]{
    def bind[F, T](a: S => (S, F))(f: F => (S => (S, T))): S => (S, T) = state => {
        val (s,o) = a(state)
        f(o)(s)
      }
    }

  implicit class MonadOps[A[_], F](a: A[F]){
    def bind[T](f:F => A[T])(implicit m:Monad[A]):A[T] = m.bind(a)(f)
  }
}
```
so now we can chain our calculations with `bind`
```scala
val state1 = deposit.bind(x=>_.withdraw(BigDecimal("5")))
  val state2 = state1.bind(x=>_.withdraw(BigDecimal("10")))
  val state3 = state2.bind(x=>_.withdraw(BigDecimal("900")))
  val wallet3 = state3(w)
  println(wallet3._1.amount)
```
