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

