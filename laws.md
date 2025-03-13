# Software engeneering laws
Here is my collection of software engineering laws. In my opinion it's too much easier to substantiate your point if it is based on "Some Wellknown Guy's Law"

## Conway's law
Any organization that designs a system (defined broadly) will produce a design whose structure is a copy of the organization's communication structure.
> There are also "Inverse Conway Maneuver": to deliberately structure an organization so that a particular, desired architecture emerges
## Little's law
 Long-term average number L of customers in a stationary system is equal to the long-term average effective arrival rate λ multiplied by the average time W that a customer spends in the system
## Amdahl's law
If you can split your task on multiple parallelizable instructions then total time of task execution will be time of execution slowest instruction  
For example, if a program needs 20 hours to complete using a single thread, but a one hour portion of the program cannot be parallelized, therefore only the remaining 19 hours (p = 0.95) of execution time can be parallelized, then regardless of how many threads are devoted to a parallelized execution of this program, the minimum execution time cannot be less than one hour

## Hyrum's law 
An observation on Software Engineering  
With a sufficient number of users of an API, it does not matter what you promise in the contract: all observable behaviors of your system will be depended on by somebody.

## Brooks's law
Adding manpower to a late software project makes it later

## Miller's law
the number of objects an average human can hold in short-term memory is 7 ± 2

## Lehman's laws

+ "Continuing Change" — a software system must be continually adapted or it becomes progressively less satisfactory.
+ "Increasing Complexity" — a software system evolves, its complexity increases unless work is done to maintain or reduce it.
+ "Self Regulation" — a software system evolution processes are self-regulating with the distribution of product and process measures close to normal.
+ "Conservation of Organisational Stability (invariant work rate)" — the average effective global activity rate in an evolving a software system is invariant over the product's lifetime.
+ "Conservation of Familiarity" — as a software system evolves, all associated with it, developers, sales personnel and users, for example, must maintain mastery of its content and behaviour to achieve satisfactory evolution. Excessive growth diminishes that mastery. Hence the average incremental growth remains invariant as the system evolves.
+ "Continuing Growth" — the functional content of a software system must be continually increased to maintain user satisfaction over its lifetime.
+ "Declining Quality" — the quality of a software system will appear to be declining unless it is rigorously maintained and adapted to operational environment changes.
+ "Feedback System" (first stated 1974, formalised as law 1996) — software system evolution processes constitute multi-level, multi-loop, multi-agent feedback systems and must be treated as such to achieve significant improvement over any reasonable base.

