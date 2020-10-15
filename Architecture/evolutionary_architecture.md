# Evolutionary Architecture

Recently I read awesome book by Neal Ford called ["Evolutionary Architecture"](http://nealford.com/books/buildingevolutionaryarchitectures.html). I was so inspired that I write a lot of my keynotes of this book. First of all: I highly recomend to all read it and I believe that everyone can adapt some techniques from this book.

- Main assumption: software changes is cheap, so we can adapt to new changes and evolve. To meet this point we first of all should adopt all modern practices(devops, agile, BI, etc)
- Seems like this book is a source of definition: software architecture is the “parts hard to change later.” 
- Manual architecture governance should be replaced with automatic based on fitness functions. **Fitness functions**(FF) -- some measurable (not necessary automaticaly) executive process which define how your current architecture meet nonfunctional requirements. But how correctly define this functions? And how to deal with survival paradox here(we have no failures last year, so our resilience fitness function `ff _ = True` is great)?
- If it's possible structure teams to look like your target architecture, and it will be easier to achieve it. (Inverse Conway Law)
- FF should be defined at the concern collecting stage(before development) but should be updated if needed(see review process below)
- Fitness functions can be classified into three simple categories:
  1. **Key** -- These dimensions are critical in making technology or design choices
  2. **Relevant** -- These dimensions need to be considered at a feature level, but are unlikely to guide architecture choices
  3. **Not Relevant**
 - Many times Neal equals (-ilities) and dimesions(viewpoints). On the other hand [*"classic"*](https://www.viewpoints-and-perspectives.info/) approaches separate viewpoints and perspectives(-ilities)
 - There are should be a continous process of FF review with technical stakeholders
 - There are no concrete reference in this book to the target architecture, but many mentions of dimensions and "architecture design" suggests that the upfront architecture design process should preliminate of FF-based governance 
 - All views should be consistent at the design phase and than FF will help to identify missing tradeoffs at the development phase
 - Evolutionary Architecture helps architect deal with *unknown unknowns* domains because of reactive approach (if we found new concern we simply define new FF and evolve our architecture)
> All architectures become iterative because of unknown unknowns; agile just recognizes this and does it sooner.  
> — Mark Richards
- At the Neal's approach architects role should be dedicated to team(also team should be crossfunctional)
- Seems like that Evolutionary Architecture cornerstone and [Fowler's 9 microservices rules](../microservices.md/#9-microservices-rules) are quite similar
- Strive for a low number of connections between development teams. Seems like Less or other scaled framework should be adopted?
- This book ideas looks much more consistent in addition to new Open Group [Agile Architecture standart](https://publications.opengroup.org/c208?_ga=2.2181682.306539801.1602100093-1756631493.1602100093). In particular it's become more clear that the main focus of architecture shifts from intentional architecture to control emergent architecture. So FF helps with emergent, while classic approaches still valid for intentional design