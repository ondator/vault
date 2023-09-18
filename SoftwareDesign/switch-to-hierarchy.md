# Hierarchy vs Pattern matching

Long time ago inspired by awesome M.Fowler book [Refactoring](https://martinfowler.com/books/refactoring.html) I was frequently asking on interview to refactor a code block like this:

```csharp
public class Animal
{
    public void MakeSound()
    {
        if(this is Dog) Bark();
        if(this is Cat) Mew();
        if(this is Pikachu) PikaPika();
        if(this is Cthulhu) EnslaveAllALiving();
    }
}
```
Ok, lets try to do something with this

## Switch to hierarchy
Of course I expect that interviewee will transform this awful switch to class hierarchy as Fowler recomends. Thanks to his book this is known as [Replace Conditional with Polymorphism](https://refactoring.com/catalog/replaceConditionalWithPolymorphism.html)
And finaly the code which I expects should looks like this:

```csharp
public abstract class Animal
{
    public abstract void MakeSound()
}

public class Dog : Animal
{
    public void MakeSound()
    {
        Bark();
    }
}

public class Cat : Animal
{
    public void MakeSound()
    {
        Mew();
    }
}
```

Looks much better, right? Seems like now we can simplier add new animals and `MakeSound` logic is now fully independent and more SOLID because Cthulhu doesn't know how to Bark ;)  
But what if our application specific is not to extend animals hierarchy but to add new abstract methods to `Animal`(and concrete implementation to descendants)? Seems like in this case we are inifinitly change all our concrete classes

## Pattern Matching

What if instead of OOP approach we will use functional? Functional programming follows principles of pure open data structures and pure functions separated from data. Ok lets try
```csharp
public interface IAnimal{}

public class AnimalService
{
    public string MakeSound(IAnimal animal)
    {
        return animal switch
        {
            Dog x => Bark(x),
            Cat x => Mew(x),
            _ => throw new NotSupportedException()
        };
    }

    public string Bark(Dog d)
    {
        ...
    }

    public string Mew(Cat c)
    {
        ...
    }
}

public class Dog : IAnimal
{
    ...
}

public class Cat : IAnimal
{
    ...
}
``` 

> Here we change return type of `MakeSound` to `string` because in functional world we prefer pure functions and adding of effects on program boundaries. So we can either model IO effect or simply return our "sound" and do IO somewhere up the stack

Now we have a design which is flexible enought to add new logic, but it's not so good in the case when we need to add a lot of new `IAnimal` implementations. This is a very interesting trade-off. It become much more confusing when you grasp that it is possible to combine two approaches above for one class hierarchy! Seems like that such mixed approach can solve this case, but practically team became confused and try use one consolidated approach

So here is the summary:
+ hierarchy is only the one of possible solutions
+ you should prefer class hierarchy if you will extends this hierarchy quite often
+ you should prefer pattern matching if you will add new methods and your class hierarchy will be stable
