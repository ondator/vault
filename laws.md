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

