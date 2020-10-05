# Libraries vs Frameworks

During my communications with software developers I frequently faced with dissent on my preference libraries over frameworks. In this article I will collect opinions of famous engineers and architects at this point

### Neal Ford at "Evolutionary Architecture" book
Architects make a common distinction between libraries and frameworks, with the
colloquial definition of “a developer’s code calls library whereas the framework calls a
developer’s code.” Generally, developers subclass from frameworks (which in turn
calls those derived classes), thus the distinction that the framework calls code. Con‐
versely, library code generally comes as a collection of related classes and/or functions
developers call as needed. Because the framework calls the developer’s code, it creates
a high degree of coupling to the framework. Contrast that with library code, which is
generally more utilitarian code (like XML parsers, network libraries, etc.) and has a
lower degree of coupling.
We prefer libraries because they introduce less coupling to your application, making
them easier to swap out when the technical architecture needs to evolve.
One reason to treat libraries and frameworks differently comes down to engineering
practices. Frameworks include capabilities such as UI, object-relational mapper, scaf‐
folding like model-view-controller, and so on. Because the framework forms the scaf‐
folding for the remainder of the application, all the code in the application is subject
to impact by changes to the framework. Many of us have felt this pain viscerally—any
time a team allows a fundamental framework to become outdated by more than two
major versions, the effort (and pain) to finally update it is excruciating.
Because frameworks are a fundamental part of applications, teams must be aggressive
about pursuing updates. Libraries generally form less brittle coupling points than
frameworks do, allowing teams to be more casual about upgrades. One informal gov‐
ernance model treats framework updates as push updates and library updates as pull
updates. When a fundamental framework (one whose afferent/efferent coupling
numbers are above a certain threshold) updates, teams should apply the update as
soon as the new version is stable and the team can allocate time for the change. Even
though it will take time and effort, the time spent early is a fraction of the cost if the
team perpetually procrastinates on the update. Because most libraries provide utilitarian functionality, teams can afford to update
them only when new desired functionality appears, using more of an “update when
needed” model.