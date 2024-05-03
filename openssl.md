# Few openssl tricks

1. fetch cert based on root crt
```shell
</dev/null openssl s_client -connect dc120.npo-saturn.int:636 -CAfile /tmp/RSA01_2029.pem -servername dc120.npo-saturn.int | openssl x509 > /tmp/npo-saturn.cert
```

2. fetch cert
```shell
</dev/null openssl s_client -connect dc120.npo-saturn.int:636 -servername dc120.npo-saturn.int | openssl x509 > /tmp/npo-saturn.cert
```

3. view cert
```shell
 openssl x509 -in /tmp/nembus.cert -text
```

4. view java keystore
- misc:
```shell
keytool -list -keystore /opt/bitnami/java/lib/security/cacerts -storepass changeit -v
```

- cacerts
 keytool -list -cacerts -storepass changeit -v
