# State monad

Imagine you have some reach domain object with some state and logic:

```scala
public class Wallet
{
    def amount:BigDecimal

    def withdraw(BigDecimal amount): BigDecimal
    {
        this.amount -= amount;
        amount;
    }
} 
```

> here I reduce all checks and error handling in order not to increase complexity, but in prodution code you should use Either, or smth else

Not we wanna to make this code more functional and pure, so

```scala
public class Wallet
{
    def amount: BigDecimal

    public Withdraw(BigDecimal amount) : (BigDecimal, Wallet)
    {
        var result = copy;
        result.Amount -= amount
        return (amount, result);
    }
} 
```

Ok, but now your API client become suffer from pair deconstruction on every breath:
```cs
var wallet = new Wallet(1000m);
var (forIceCream, wallet1) = wallet.Withdraw(5m);
var (forShampoo, wallet2) = wallet1.Withdraw(10m);
var (forCar, wallet3) = wallet2.Withdraw(900m);
```

Maybe there are some trick to avoid all this deconstructioning?
