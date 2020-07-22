# DI

## Agenda

DI is one of possible ways to follow DIP from SOLID. DIP tells that our objects dependencies should be abstract, so we can either make all dependencies abstract(DI) or make abstract source of all dependecies(SL).

DI by injection point:
- constructor-based(default)
- setter-based(for optional dependencies)
- argument-based(for local dependencies)

DI by worktime:
- runtime(like in AspNet)
- Compiletime DI ([Cake pattern](../cake-pattern.md) or [Reader monad](../reader.md))




