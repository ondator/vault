# Keycloak

1. Get token
```sh
curl -d "username=admin&password=Pa55w0rd&grant_type=password&client_id=oidc&client_secret=BhvHHHkuigfbM2wqYO5az0nJ0SGAlW2g" -v -X POST 'http://keycloak2:8082/realms/master/protocol/openid-connect/token'
```
or in var

```sh
token=$(curl -d "username=admin&password=Pa55w0rd&grant_type=password&client_id=oidc&client_secret=BhvHHHkuigfbM2wqYO5az0nJ0SGAlW2g" -X POST "http://keycloak2:8082/realms/master/protocol/openid-connect/token" | jq .access_token)
```

2. search user
```sh
curl -H "Authorization: Bearer $token" -v -X GET 'http://keycloak2:8082/admin/realms/master/users?q=lastName:Einstein%20firstName:Albert%20Einstein' 
```

