# Аутентификация 3. Kerberos
## WTF is Kerberos
В предыдущей части, когда мы разбирали LDAP мы частично затронули вопросы безопасности этого протокола и выяснили, что используя LDAP bind мы с вами далеко не уедем. Но что же делать, если все-таки хочется централизованно управлять пользователями и их правами, с одной стороны, и иметь возможность надежной и безопасной аутентификации, с другой? Вот тут на помощь и приходит Kerberos.
> Disclamer: Я тут постараюсь описать все максимально упрощенно, не вдаваясь в подробности и прошу простить меня за некоторые неточности в описании реализации (например, я умышленно опустил всю криптографию). Цель этого intro -- понять Kerberos настолько, что бы иметь возможность настроить клиентское приложение для работы с ним. Настройку KDC и нюансы протокола мы тут рассматривать не будем. Тем не менее, для углубления понимания можно почитать Kerberos RFC [4120](https://datatracker.ietf.org/doc/html/rfc4120) и полистать [сайт консорциума](https://kerberos.org/)

Главным предположением в основе Kerberos протокола является работа в недоверенной сети, т.е. никакие конфиденциальные данные не могут быть переданы никому в открытом виде. Что бы как-то выжить в такой недружелюбной среде, нам необходимо выделить некоего хранителя аутентификационной информации и доверять (и защищать) только ему. Этому хранителю мы будем передавать нашу аутентификационную информацию(давать логин и пароль), в ответ получая некие ключи, с помощью которых мы и будем подтверждать себя в диалоге с другими системами. Т.к. наш хранитель выдает ключи, то и называть мы его будем Центром Выдачи Ключей (KDC). 
Что же находится внутри ключа? Внутри у нас лежит во-первых владелец ключа(Client Principal), во вторых приложение, которому будет ключ предъявлен (Service Principal), в третьих временную метку момента выпуска ключа и срок действия ключа и, наконец, секретный ключ сессии, который знает только приложение и (теперь) клиент. Теперь у нас есть некий тикет, который мы можем отдать приложению, что бы оно убедилось, что мы те, за кого себя выдаем
На самом деле, все немного сложнее и в KDC мы ходим не один раз, а 2:
```puml
participant Client as c
participant KDC as k
participant Application as a

c ->  k: AS_REQ
k --> c: AS_REP
c ->  k: TGS_REQ
k --> c: TGS_REP
c ->  a: AP_REQ
a --> c: AP_REP
```

Зачем же 2 раза ходить в KDC? Почему нельзя сразу попросить тикет для нужного приложения? На самом деле, можно и так (например используя в kinit флажок -s), но, в этом случае, тикет будет получен для конкретного приложения и SSO (обсудим его чуть дальше) работать не будет. В базовом же сценарии, мы сначала приходим в KDC и получаем TGT (ticket granting ticket), а уже TGT меняем на финальный тикет, с которым идем в приложение.

На всякий случай, приведу тут словарик Kerberos-терминов, которые наверняка вам встретятся при работе с этим чудом техники:

- Realm -- пространство 
- Principal -- 
- SPN -- 
- UPN -- 
- Keytab --
- KVNO -- версия секретного ключа принципала. Дело в том, что при смене пароля принципала, для него генерируется новый секретный ключ и что бы тикеты, которые были подписаны старым ключем протухли, используется KVNO. Фактически это просто номер версии, который инкрементально растет при каждой смене пароля
- KDC --  key distribution center. Фактически -- Kerberos-сервер
- AS -- подсистема KDC, обрабатывающая AS_ запросы
- TGS -- подсистема KDC, обрабатывающая TGS_ запросы
- TGT -- tickets granting ticket. Тикет тикетов. Именно его мы можем обменять у KDC на конечный тикет для конечного приложения.
  
## Магия SSO
Это все замечательно, но самый главный фокус который дает Kerberos, это даже не безопасная аутентификация, а то, что с Kerberos'ом мы можем войти в любую систему, не заставляя пользователя постоянно вводить логин и пароль (т.н. механика Single-Sign-On или SSO).
Все это получается благодаря тому, что у нас вместо обычного пароля есть тикет и мы можем абсолютно безопасно предъявлять его в KDC при входе в любую систему, тем самым, автоматически авторизовываясь в этой системе. Системе остается всего-лишь попросить у пользователя его тикет, для чего мы будем использовать механизм SPNEGO.
## настройка KDC
> этот шаг мы делаем самостоятельно только на нашем тестовом стенде. В бою, для вас все выполнит администратор контроллера домена
В этот момент нам понадобится контейнер kdc. Проваливаемся в него и делаем следующее:
1. Создаем принципала для нашего кейклока 
   ```sh
   $ kadmin.local
   $ addprinc -x dn=cn=keycloak2,ou=tuz,dc=example,dc=org HTTP/keycloak2
   ```
> я выбрал 2й, но вы можете аналогично создать и для 1го
2. Добавляем kerberos-атрибуты (делаем пользователя принципалом) для Alice
   ```sh
   $ kadmin.local
   $ addprinc -x dn=cn=Alice,ou=people,dc=example,dc=org alice
   ```
3. в контейнере _keycloak2_ генерим кейтаб-файл для нашего сервиса
   ```sh
   $ kadmin -p HTTP/keycloak2@EXAMPLE.ORG
   $ ktadd -k /tmp/kc2.keytab HTTP/keycloak2
   ```

в результате всех манипуляций, в контейнере keycloak2 должен появиться файл /tmp/kc2.keytab и при вызове `KRB5_TRACE=/dev/stdout kinit -k -t /tmp/kc2.keytab HTTP/keycloak2@EXAMPLE.COM`
не должно вывалиться ошибок, а если после еще и вызвать `klist -l`, то должно появиться что-то вроде
```sh
Principal name                 Cache name
--------------                 ----------
HTTP/keycloak2@EXAMPLE.ORG          FILE:/tmp/krb5cc_1001
```

## настройка
0. открываем 88 порт от нашей системы до KDC
1. получаем УЗ и кейтаб
2. создаем krb5.conf в /etc
```ini
[libdefaults]
 dns_lookup_realm = false
 ticket_lifetime = 24h
 renew_lifetime = 7d
 forwardable = true
 rdns = false
 pkinit_anchors = FILE:/etc/pki/tls/certs/ca-bundle.crt
 default_realm = SAMPLE.INT
 default_ccache_name = KEYRING:persistent:%{uid}
 allow_weak_crypto = true
# permitted_enctypes = rc4-hmac arcfour-hmac arcfour-hmac-md5
# default_tkt_enctypes = rc4-hmac arcfour-hmac arcfour-hmac-md5
# default_tgs_enctypes = rc4-hmac arcfour-hmac arcfour-hmac-md5
 default_tkt_enctypes = aes256-cts-hmac-sha1-96 aes128-cts-hmac-sha1-96 rc4-hmac
 default_tgs_enctypes = aes256-cts-hmac-sha1-96 aes128-cts-hmac-sha1-96 rc4-hmac

[realms]
 EXAMPLE.COM = {
  kdc = DC.EXAMPLE.COM
  default_domain = EXAMPLE.COM
 }

 SAMPLE.INT = {
  kdc = DC1.SAMPLE.INT
  default_domain = SAMPLE.INT
 }

[domain_realm]
 .example.com = EXAMPLE.COM
 example.com = EXAMPLE.COM
 EXAMPLE.COM = EXAMPLE.COM
 .EXAMPLE.COM = EXAMPLE.COM

 .sample.int = SAMPLE.INT
 sample.int = SAMPLE.INT
 SAMPLE.INT = SAMPLE.INT
 .SAMPLE.INT = SAMPLE.INT
```
Если мы не знаем адрес kdc, то можно посмотреть nslookup'ом:
`nslookup -type=any _kerberos._tcp.EXAMPLE.COM`

1. на всякий случай стоит сделать kinit -k -t /etc/prod.keytab HTTP/uchetka.example.com@EXAMPLE.COM
2. вы великолепны

## дебаг

не работать может потому что
1. вы криворукий дебил
2. админ АД криворукий дебил

- что бы убедиться, что мы не верблюды, стоит получить kerberos tiket и посмотреть на него:
1. делаем `KRB5_TRACE=/dev/stdout kinit -k -t /etc/prod.keytab HTTP/uchetka.example.com@EXAMPLE.COM`
2. делаем `klist -l`

- что бы убедиться, что админ АД не верблюд мы
1. проверим групповые политики в __используемом пользователем__ браузере
- в хроме жмем chrome://policy и ищем AuthNegotiateDelegateWhitelist и AuthServerWhitelist. Там долны быть домены нашего приложения
- в яндексе жмем browser://policy и ищем AuthNegotiateDelegateAllowlist и AuthServerAllowlist. Там долны быть домены нашего приложения
2. если политики не прописаны, то есть способ запустить браузер с флагами и убедиться, что мудак действительно одмен:
`"C:\Program Files\Google\Chrome\Application\chrome.exe" --auth-server-whitelist="*.example.com,example.com" --auth-negotiate-delegate-whitelist="*.example.com,example.com"`
3. в конце-концов можно поснифать spnego-токены(просто через F12/network их не видно): chrome://net-export/

- еще стоит
1. проверить kerberos principal. Внезапно он может оказаться отличным от UserPrincipalName. Симптомы -- ошибка вида 
```
>>>KRBError:
         sTime is Mon Oct 07 17:36:33 UTC 2024 1728322593000
         suSec is 644455
         error code is 68
         error Message is null
         sname is krbtgt/FSKEES.RU@FSKEES.RU
         msgType is 30
                [Krb5LoginModule] authentication failed
```

> N.B. логика создания kerberos принципала в кейклоке следующая: при импорте пользователя берется атрибут указанный в поле Kerberos principal attribute и записывается в readOnly атрибут пользователя KERBEROS_PRINCIPAL. При аутентификации значение достается из KERBEROS_PRINCIPAL, и если оно с доменным суффиксом, то отдается в KDC как есть, если без суффикса, то суффикс приделывается исходя из kerberos realm'а. Т.е. если UPN с некорректным суффиксом, а sAMAccountName корректный, то sAMAccountName в Kerberos principal attribute решит проблему. При этом, в версии ниже 22.0.5 зачем-то сделана странная проверка на доменный суффикс, и UPN, если суффикс отличается от реалма даже не будет отправлен в KDC(процесс сразу свалится с ошибкой). С 22.0.5 это поведение убрали, но почему-то sAMAccountName в Kerberos principal attribute ломает импорт пользователей. Чинится созданием маппера на атрибут KERBEROS_PRINCIPAL с значением ldap-атрибута sAMAccountName

2. Включить ползунок парольной аутентификации через керберос. Если проблема именно со spnego, то аутентификация по паролю будет работать, если проблема с керберосом, то посыпятся логи
3. Проверить криптоалгоритмы в krb5.conf. Вероятно надо включить allow_weak_crypto = true и что-то дописать в permitted_enctypes. Симптомы -- ошибка вида 
   ```
   2024-06-06 07:54:31,547 WARN  [org.keycloak.federation.kerberos.impl.SPNEGOAuthenticator] (executor-thread-185) SPNEGO login failed: java.security.PrivilegedActionException: GSSException: Failure unspecified at GSS-API level (Mechanism level: Invalid argument (400) - Cannot find key of appropriate type to decrypt AP-REQ - RC4 with HMAC)
   ```