# Enterprise Authentication. Part 3. SAML

## Why SAML
In previous part we discussed Kerberos protocol which solved problem of secure user authentication, so why we should be interested in something else? Unfortunately, Kerberos also has it's own Achilles' heel. An issue is that Kerberos requires a lot of settings (set up group policies for SPNEGO, attach workstation to domain and for service we should create a principal and release keytab) in addition to some infrasturcture requirements (synchronized clocks, canonical and trusted DNS). It's not a big deal, but only when you are in LAN, but what we should do if our service should work on WAN?
> Actualy, few recent Kerberos RFC add some support for some modern preauth techniques such as SPIKE and FAST-based, so it's configuration should be a lot simplier
## What is SAML
SAML(security assertion markup language) is raser simplified authentication protocol based on excahnge some XML-encoded documents named **Assertion**. An assertion is some kind of grant similar to kerberos ticket, but also provides a little bit more information about our principal. Same as kerberos ticket Assertion should be asked for some service (named **Service Provider** in SAML terms) by Client (here it names **Agent**) from Authentication Service (named here **Identity Provider** or simply **IdP**). Such assertion request named **SAML Request** and reply from IdP (with Assertion) named **SAML Response**. So, basicly we have something like
```puml
actor User as u
participant Agent as c
participant i as "Identity Provider"
participant "Service Provider" as s

u -> c: open some app
c -> s: call some API
s --> c: response with 302 (SAML Request in Location)
c -> i: redirects to IdP with SAML Request
i --> c: response with 302 (SAML Response in Location)
c -> s: redirects to SP with SAML Response
c -> s: call some API
s --> c: response with some data
c --> u: provide some awesome features
```

Actualy SAML is transport-independent, so it could be implemented over HTTP, SOAP, etc. Here is diagram of SAML using HTTP 302 Redirects, but there are also commonly using HTTP POST way. In SAML universe such transport approaches called **Bindings**.
Also there are a few options of starting point of SAML flow. In the diagram an initiator of authentication process is SP, but it can also be an IdP (in such case user at first comes to the IdP and than will be redirected to an SP after authentication). Such approaches in SAML called **Profiles**. Here we will see only WebBrowser SP-initiated SSO profile, but there are a lot of other.

Actualy, all this magic doesn't required any configuration except building of trust relationships between SP and IdP. Usualy it's also quite simple, parties should just exchange some metadata including some ids and crypto keys wrapped in XML document. Moreover, if SP and IdP have network interop they could just exchange metadata links as far as they support metadata publishing.
## What about Keycloak?
### Keycloak as SP
### Keycloak as IdP
### Few words about ADFS


https://docs.oasis-open.org/security/saml/v2.0/saml-core-2.0-os.pdf
http://docs.oasis-open.org/security/saml/Post2.0/sstc-saml-tech-overview-2.0-cd-02.pdf
### основные сценарии
https://docs.oasis-open.org/security/saml/v2.0/saml-profiles-2.0-os.pdf
- vanila
- IDP initiated
- SAML Bearer
https://tools.ietf.org/id/draft-ietf-oauth-saml2-bearer-10.html

## Debug
- SAML Debug is quite simple as far as you can sniff HTTP traffic on User Agent. For example, if you use browser, you can just open DevTools and search for SAML Requests/Responses. Than you can apply base64 decode and review SAML XML Messages
- You can also see Keycloak SAML proccessing logs if set up some envs
`KEYCLOAK_LOG_LEVEL: WARN,...,org.keycloak.saml.SAMLRequestParser:debug`