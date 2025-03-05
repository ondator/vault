# Enterprise Authentication. Part 1. Basics
Since working with a lot of different software development and engeneering teams I noticed that a lot of my collegues are not clearly understood all shades of contemporary authentication protocols, moreover frequently confuse authentication with authorization and identification. In this articles I will try to explain what is authentication, how it works based on most popular protocols and how to work with this protocols using [Keycloak](https://www.keycloak.org/)

Seems like it will be a lot of info, so I will split it into a few parts. Here is the articles list:
- Enterprise Authentication. Part 1. Basics (here you are)
- [Enterprise Authentication. Part 2. LDAP](./auth-ldap.md)
- [Enterprise Authentication. Part 3. Kerberos](./auth-kerberos.md)
- [Enterprise Authentication. Part 4. SAML](./auth-saml.md)
- [Enterprise Authentication. Part 5. OIDC](./auth-oidc.md)

## What is authentication (and what it isn't)
First of all we should see the difference between 3 concepts: authentication(authN), identification and authorization(authZ):

- Identification is responsible for recognition a person in front of us (Who is it?). It should be anything from simple login form up to QR codes from already logined device or every face recognition
- Authentication is responsible for verification of identification procedure. Also a lot of options possible here: password, confirmation codes by SMS/email/etc, one-time-password apps like Google Authenticator, etc. The main idea is to take some proof that person in front of us is exactly the same who he want to appear
- Authorization is the final step of access management process. It's responsible for confirmation of person's permissions for some actions which persons want to do. Basically it's can be some kind of dialogue with some "arbiter" like "Can I? No/Yes". Here also can be a lot of realisation options, but I belive that authorization is a subject for a separate article cycle (or may be even a RFC like 6749, which we will briefly touch in article about [OIDC](./auth-oidc.md))

Keycloak which I mentioned before and which we will use for all our study cases is an open source identity and access management (IAM) software. It's used for manipulating user's identities(for identification), and as central authentication system
![access management](../../assets/img/access_management.png)
> Actually Keycloak also could be used for authorization purposes, but it's not point of our interests now (however we see a few options in SAML and OIDC chapters)

As I said here we will discuss the most popular authN protocols. However, there are a lot of them and all of them are quite different, there are a lot of similar traits. First of all, all of them try to grant(or not) access for some __User__ to some __Service__. Also most of them try to differ some ways User used to get access to Service(for example mobile app or web site or smth else), usualy it is called __Client__. Besided that, for obtaining access to Service User should authenticate in some __Authentication Service__ or simply __AS__(yep, it's our Keycloak). Than, most of them based on conception of trust. It means that both of Service and AS are trust to each other and both of them can proof itselt to each other(in some cryptographic way). Moreover, our User also trust to our AS and them also have some way(usualy also cryptographic-based) to proof it's identity to each other.


## Why so serious? 
Seems like we can simply use login + password? Unfortunatelly we can't. First of all, we don't want to. Actually average person use a lot of systems in their life, and every has it's own password (or, even worse, the same). Just imagine all  this passwords you should to remember...  Of course, there are some password management systems like Bitwarden, but all of them also have some exploites and all your passwords could be stolen one day. Moreover you could be accessible to some sensitive data (nuclear bomb codes). Are you really want to save password from nuclear button on the Bitwarden/paper on the top of your screen or even forgot it? If you see some Hollywood films, they are not use passwords, they use a physical keys/or retina scaner or something more complicated, and actualy, our large enterprises(Critical Informaion Infrastructure/КИИ) also use more complicated and secured authentication system. On the other hand large enterprise has a lot of systems, and if each of them will use it's own IAM it will be a real chaos every time when new employee gets a job, or someone get fired. Administrators should add or delete new user from every system. So the last our concern will be the one unified IAM, to rule all systems. So, what we should do?

## SSO

So, we have a few concerns:
- we want to have one password per employee, or don't have at all
- we want to be able to use physical keys instead of passwords
- we want to operate all identities and permissions in one system

To meet them all let me introduce an Single Sign On (SSO) technique. The main idea is that we have one authentication system which one all our enterprise system trust. If we have one access control system we can connect it to one single identities database and also can apply any security measures we need. In later chapters we will meet a different realisations of such authN system and different SSO protocols.

## Keycloak

For all experiments I will use [this](https://github.com/ondator/sandboxes) docker compose scripts (please don't use them in production). To run them up just clone repository, add record
```
127.0.0.1       keycloak
127.0.0.1       keycloak2
``` 
to your /etc/host(or C:/system32/drivers/etc/hosts) and run

```
docker compose -f keycloak/keycloak-postgres-dc.yml up
```

this script will set up a few docker containers and in a few minutes you can go to http://keycloak:8080 and see Keycloak admin panel. Default login and password are admin:Pa55w0rd

## Keycloak Basics

Let's talk about key concepts of Keycloak.
1. At the left side of the Keycloak admin panel we can see a panel with two tabs groups "manage" and "configure". In this part we will talk only about "manage" group, some configuration items we will touch in the further parts. First of all we will talk about users. Users are some persons that will use our applications and authenticate to them thru keycloak. On the "users" tab you can create user or drill down to existing one (admin). Every user has some attributes(username, email, etc), credentials(password) and may have some roles and sessions. About roles we will talk a little bit later, now lets take a look into a sessions. Session is a some kind of usage of some application(client) by some user. Session can be expired(user ends usage) or active(user use an app now).
2. Upper to the Users tab there are Clients tab. Client is a term of an application thru which user will authenticate. It could be anything: mobile app/browser SPA or desktop client and for all of them we can configure different permissions and opts. First of all you should select a protocol for your client, keycloak supports two: OpenID Connect(OIDC) and SAML. Both of this protocols we will discuss later in respective parts with all their configuration settings.
3. Last tab we will see here is "Realm roles". It represent some user roles which will apply to user without client affilation. Actualy I will talk here about roles in common because if you want to apply some role per-client you can do it by just drilling down to client and than jump to "Roles" tab there.
    First of all, there are two common approach for permission management ABAC(attribute-based access control) and RBAC(role-based access control) the diffrence is in granularity of access. When ABAC provides an access for every single resource unit(attribute). For example if you have some news website, you can add a separate attribute for every single news. On the other hand, RBAC provide access based on user role. So, if your news website have some administrators, you can assign them different role from plane users.
> If you want to read more about ABAC and RBAC you [can start here](https://www.okta.com/identity-101/role-based-access-control-vs-attribute-based-access-control/)

In keycloak perspective roles is just user an attribute which will someway passed to the final service and whos based it will grant access or not. So, in keycloak role have only a name and a description, because all permissions related to role will be defined on the final service side 
4. At the top of the admin pannel we can see a dropdown with selected "master" item. "Master" a current realm, which is somekind of Keycloak tennant. You can create your own one (or use existing "master"). Realm shares all users, roles, clients and other settings between it's users

Here we are. In the next part we will talk about LDAP protocol.