# Reader monad

По-факту это просто стрелка из некоторого окружения в тип:
```haskell
-- r - окружение
data Reader r a = Reader { runReader :: (r -> a) }
```

## Reader-based DI

Reader можно использовать для compile-time DI:

```scala
def metodWithDep : Reader[Ret, Dep] = {
    Reader(
        (dep: Dep) => useDep(dep)
    )
}

def appRoot = {
    val depInstance = ???
    val methodRes = metodWithDep().run(depInstance)
}
```