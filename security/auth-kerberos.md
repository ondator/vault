# Аутентификация. Kerberos
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