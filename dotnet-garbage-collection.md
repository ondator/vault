# Garbage Collection

.Net GC is generation-based mark-and-sweep stop the world. CLR begin garbage collection when
+ OS reports about low memory
+ manual call `GC.Collect()`
+ Heap generation overflow

When GC starts to collect it
+ suspend all other threads
+ find all roots(static fields, local variables, CPU registers). Recurcivelly find all referenced objects and marks them
+ Build finalization queue
+ Run compaction on heap
+ Sweep all garbage

Garbage collected by generations. There are 3 generations + LOH. If object survives it moved to next generation. On generation 2 also collected LOH.

> Actually now GC is not *stop the world*. There are background collection support for large generation(2 and LOH). 0 and 1 generation are unparallel because they are quite small