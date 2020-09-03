# .Net Memory Model

 CLR has each own memory model different from common hardware architectures. It doesn't provide common layered architecture(CPU registries -> CPU caches -> RAM) instead it provides abstractions of thread stacks and heaps

 ```plantuml
 @startuml

rectangle Memory{
    
    together {
        rectangle t1 as "Thread Stack"
        rectangle t2 as "Thread Stack"
        }
    
    together {
        rectangle h as "Heap"
        rectangle loh as "Large Objects Heap"
        }

    t1 -[hidden]->loh
}
 ```

 each thread has it's own stack where it stores return pointer, current method arguments references, local variables instances of value types and references of reference types. Heap, on the other side, stores all reference type objects, object-types and boxed value types.

 LOH is the same heap but for objects larger than 85k bytes. Also there are some difference in [garbage collection](dotnet-garbage-collection.md)