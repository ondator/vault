# 12 factor applications methodology

`12 factor`  is a widely used methodolgy of developing SaaS software. In fact it's a checklist of some aspects covering all application viewpoints from configuration to IO.

Main goal of `12 factor` is to achieve operation and delivery simplicity by following some common best practices:
+ declarative infrastructure
+ portability
+ continous deployment
+ scalability
+ etc.

to achieve such goals production-ready service must be compliant to 12 factors

## 12 factors
### 1. Code base
+ Code base should be managed through VCS
+ Few apps must NOT share same code. If this violates than you should segregate common code to the library(see [Dependencies](#2-dependencies)) 

### 2. Dependencies
+ All dependencies should be declared explicitly(by manifest file such as `package.json` for `npm`)
+ There are should NOT be implicit dependencies

### 3. Configuration
+ configure your apps through *env vars*
+ don't use config files because you can occasionaly(or not) save them to VCS

### 4. Backing services
+ all backing services(as databases, 3rdparty api or infrastructure servces) should be threated as attachable resource.
+ Just don't hardcode connection strings

### 5. Build, release, run
+ there are one-way flow:
  ```mermaid
  graph LR
    Build["Build <br/>(download artefacts <br/>and build codebase)"] --> Release["Release <br/>(add configuration <br/>and deploy)"]--> Run
  ```

### 6. Processes
+ stateless share-nothing apps

### 7. Port binding
+ app should be self-contained
+ app should be exported by binding it to some port (like docker EXPOSE)

### 8. Concurrency
+ All that CAN be running as separate process SHOULD be running as separate process

### 9. Disposability
+ Gracefull shutdown SHOULD be designed

### 10. Dev/prod parity
+ Dev, Test and Prod environments should be same(or as close as possible)

### 11. Logging
+ Just log to `stdout`

### 12. Admin processes
+ Run admin processes as they are deployed code processes(same security/isolation/environment constraints)

## Another 7 factors

Recently IBM engeneers extends 12 factors with more 7:

### 13. Observability
+ add `liveness` and `readiness` probes
+ add custom metrics(`USE`/`RED`/`4 Golden Signals`)
+ use metrics driven design

### 14. Schedulability
+ Your app SHOULD not suffer from *noisy neighbour*. You can achieve this by using `requests` and `limits` in k8s or manualy set Linux namespases quota

### 15. Upgradability
+ There are SHOULD be an ability to run in parallel old and new version of service in order to zero-downtime update

### 16. Least privilege
+ run your application with least privilege in order to reduce attack zone

### 17. Auditability
+ you should be able to audit all critical operations

### 18. Security
+ use e2e security

### 19. Measurability
+ you should be able to measure costs and benefits from your service(this is about business metrics)