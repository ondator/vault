# OWASP Design Principles

## OWASP Security Design Principles

These principles help to develop secure application:
### 1. Asset clarification
You should know what data you will secure

### 2. Understanding attackers
Motivated cybercriminals and script kiddies require different level of security

### 3. Core pillars of information security
OWASP recommends that all security controls should be designed with the core pillars of information security in mind:
+ Confidentiality – only allow access to data for which the user is permitted
+ Integrity – ensure data is not tampered or altered by unauthorised users
+ Availability – ensure systems and data are available to authorised users when they need it

### 4. Security architecture
Security viewpoint should be present and maintained. For example through STRIDE and DREAD approaches

## OWASP Development Guide
There are some points helps to comply OWASP Security Design Principles:

### 1. Minimise attack surface area
If you can not to provide some functionality to user you should not to provide this functionality. This principle also aboult software presented on environment

### 2. Establish secure defaults
Application should be secure by default. Security should not be attached when development ends

### 3. The principle of Least privilege(POLP)
User should have least privilege which possible to maintain his activities

### 4. The principle of Defence in depth
There are should be multiple levels of security(on frontend, backend, etc.)

### 5. Fail securely
When fail pls not show stacktrace to enduser

### 6. Don’t trust services
You shouldn't give high privilege to 3rdparty services

### 7. Separation of duties
There should not be a god-mode user. Admin and end customer should have different abilities

### 8. Avoid security by obscurity
If you think that no one knows your admin panel URL you're wrong

### 9. Keep security simple
All complexity provides more issues

### 10. Fix security issues correctly
It's possible to multiple occurence of same issue because of copy-paste or smth else

## STRIDE and DREAD

There is approaches to cathegorize and estimate possible impact of vulnerability named STRIDE and DREAD respectively

### STRIDE
Is just a list of 6 vulnerability types helps to cathegorize your issue. STRIDE stands for
|Threat|Property Violated|DefiniLon|Example|
|------|-----------------|---------|-------|
|Spoofing|Authentication|Impersonating something or	someone else|Pretending	to be any of Bill Gates, Paypal.com or ntdll.dll|
|Tampering|Integrity|Modifying data or code|Modifying a DLL on disk	or DVD,	or a packet	as it traverses	the	network| 
|Repudiation|Non-repudiation|Claiming to have not performed an action|“I	didn’t send	that email” “I didn’t modify that file”|
|Information Disclosure|Confidentiality|Exposing information to	someone	not authorized to see it|Allowing someone to read the Windows source code; publishing a list of customers to a web site.|
|Denial	of Service|Availability|Deny or degrade	service	to users||
|Elevation of Privilege|Authorization|Gain capabilities without	proper authorization|

### DREAD
DREAD on the other side is an approach to estimate threat factor. 
```
DREAD Risk = (Damage + Reproduciblity + Exploitability + Affected Users + Discoverability) / 5
```

Following is a customized mathematical approach to implement DREAD methodology:

#### Damage Potential
If a threat exploit occurs, how much damage will be caused?

0 = Nothing
5 = Information disclosure that could be used in combination with other vulnerabilities
8 = Individual/employer non sensitive user data is compromised.
9 = Administrative non sensitive data is compromised.
10 = Complete system or data destruction.
10 = Application unavailability.

#### Reproducible
How easy is it to reproduce the threat exploit?

0 = Very hard or impossible, even for administrators of the application.
5 = Complex steps are required for authorized user.
7.5 = Easy steps for Authenticated user
10 = Just a web browser and the address bar is sufficient, without authentication.

#### Exploit-ability
What is needed to exploit this threat?

2.5 = Advanced programming and networking knowledge, with custom or advanced attack tools.
5 = Exploit exits in public, using available attack tools.
9 = A Web Application Proxy tool
10 = Just a web browser

#### Affected Users
How many users will be affected?

0 = None
2.5 individual/employer that is already compromised.
6 = some users of individual or employer privileges, but not all.
8 = Administrative users
10 = All users

#### Discover-ability
How easy is it to discover this threat?

0 = Very hard requires source code or administrative access.
5 = Can figure it out by monitoring and manipulating HTTP requests
8 = Details of faults like this are already in the public domain and can be easily discovered using a search engine.
10 = the information is visible in the web browser address bar or in a form.