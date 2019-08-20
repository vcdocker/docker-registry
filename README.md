# Deploy A Docker Registry With Letsencrypt Certificates

## Generate SSL certificate for domain

Get your certificate from Letsencrypt. You will have some file

```
cert.pem
chain.pem
fullchain.pem
privkey.pem
```

## Rename SSL certificates

```
cp privkey.pem domain.key
cat cert.pem chain.pem > domain.crt
```

## Generate Auth key

```
mkdir auth
docker run --entrypoint htpasswd registry:2 -Bbn {myuser} {mypassword} > auth/htpasswd
```

## Run registry container

```
docker run -d --restart=always --name registry -v "$(pwd)"/certs:/certs -v "$(pwd)"/auth:/auth -e "REGISTRY_AUTH=htpasswd"  -e "REGISTRY_AUTH_HTPASSWD_REALM=Registry Realm"  -e "REGISTRY_AUTH_HTPASSWD_PATH=/auth/htpasswd" -e REGISTRY_HTTP_ADDR=0.0.0.0:443 -e REGISTRY_HTTP_TLS_CERTIFICATE=/certs/domain.crt -e REGISTRY_HTTP_TLS_KEY=/certs/domain.key -p 443:443 registry:2
```
