# Cake Pattern

Cake Pattern -- архитектура приложения, которая позволяет организовать модульное приложение с явным внедрением зависимостей. Требует наличия в языке возможности `self type` механизма.

Идея в том, что бы вытавить наружу контракты компонента:

```scala
trait MyDependencyComponent{
    val myDependency: MyDependency // <- контракт

    trait MyDependency{
        def bar(): Unit
    }
}
```

тогда в другом компоненте можно "попросить" зависимость:

```scala
trait MyDependentComponent{
    this: MyDependencyComponent => // <- вот тут объявляем, что нам нужна зависимость

    trait MyDependent{
        def foo = myDependency.bar()
    }
}
```