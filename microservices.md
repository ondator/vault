# Microservices

`Microservices` is an distributed systems architectural style which is successor of SOA and fix common SOA pitfalls such as
+ Tight coupling
+ ESB\XML Driven Development
+ Management hell
+ etc.

## Microservices size and bounds

`MSA` was populized by M.Fowler, S.Newman and M.Richards. Unfortunately in [contrast](http://www.soa-manifesto.org/default.html) to `SOA` microservices doesn't have it's own well-defined manifesto, so there are few common taxonomies(they are quite simmilar but there are differences)

S. Newman in his fabulous book define microservice using a DDD `bounded context` term. He thought that microservice should be bounded by bounded context

M.Richards adds a term of `infrastructure microservice`, which is wery simmilar to SOA infrastructure service.

There are also popular opinion that microservice border should be restricted not by bounded context but an DDD `aggregate`. Because in V.Vernon opinion transactional integrity is bind by aggregate (but realy there are [many polar opinions](https://emacsway.github.io/ru/domain-events-in-ddd/#eventual-consistency-vs-strong-transactional-consistency))

## 9 microservices rules

In his original [blog post](https://martinfowler.com/articles/microservices.html) M. Fowler define 9 rules of microservice architecture.

### 1. Componentization via Services
+ services are implemented via [processes](12factor.md/#8-concurrency)
+ services communicate via RPC or messaging(no in-process communications)

### 2. Organized around Business Capabilities
+ microservices should be organised around Business Capability in order to avoid cross-team communication. 
+ Seems like it also assume microservice dedicated teams

### 3. Products not Projects
+ team should own a product over its full lifetime(You build it, you run it!)

### 4. Smart endpoints and dumb pipes
+ All logic should be encapsulated in microservice. There are should NOT be any logic on integration environment
+ Seems like it also assumes Choreography over Orchestration

### 5. Decentralized Governance
+ Only team is responsible for select a tool\tecnology.
+ How to deal with enterprise standarts compliance?

### 6. Decentralized Data Management
+ Every microservice should have it's own storage(`polyglot persistance`)

### 7. Infrastructure Automation
+ CI and (!)CD should be adopted
+ As far as CD Automated Tests should be adopted too

### 8. Design for failure
+ every service should be ready to hande it's dependency failure
+ chaos engeneering should be adopted
+ no single point of failure

### 9. Evolutionary Design
+ follow monolith-first approach