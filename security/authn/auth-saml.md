# Enterprise Authentication. Part 3. SAML

## Why SAML
In previous part we discussed Kerberos protocol which solved problem of secure user authentication, so why we should be interested in something else? Unfortunately, Kerberos also has it's own Achilles' heel. An issue is that Kerberos requires a lot of settings (set up group policies for SPNEGO, attach workstation to domain and for service we should create a principal and release keytab) in addition to some infrasturcture requirements (synchronized clocks, canonical and trusted DNS). It's not a big deal, but only when you are in LAN, but what we should do if our service should work on WAN?
> Actualy, few recent Kerberos RFC add some support for some modern preauth techniques such as SPIKE and FAST-based, so it's configuration should be a lot simplier
## What is SAML
SAML(security assertion markup language) is raser simplified authentication protocol based on excahnge some XML-encoded documents named **Assertion**. An assertion is some kind of grant similar to kerberos ticket, but also provides a little bit more information about our principal. Same as kerberos ticket Assertion should be asked for some service (named **Service Provider** in SAML terms) by Client (here it names **Agent**) from Authentication Service (named here **Identity Provider** or simply **IdP**). Such assertion request named **SAML Request** and reply from IdP named **SAML Response**. So, basicly we have something like
```puml
actor User as u
participant Agent as c
participant i as "Identity Provider"
participant "Service Provider" as s

u -> c: open some mobile app
c -> s: call some API
s --> c: response with 

```
## That's it? We done our best?
## What about Keycloak?



https://docs.oasis-open.org/security/saml/v2.0/saml-core-2.0-os.pdf
http://docs.oasis-open.org/security/saml/Post2.0/sstc-saml-tech-overview-2.0-cd-02.pdf
#### основные сценарии
https://docs.oasis-open.org/security/saml/v2.0/saml-profiles-2.0-os.pdf
- vanila
- IDP initiated
- SAML Bearer
https://tools.ietf.org/id/draft-ietf-oauth-saml2-bearer-10.html