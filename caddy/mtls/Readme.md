# Certificates
The certificates for tests have been created according to: https://blog.devolutions.net/2020/07/tutorial-how-to-generate-secure-self-signed-server-and-client-certificates-with-openssl/

Execute the following commands in the `certs` - Folder
## Gen CA
```
openssl ecparam -name prime256v1 -genkey -noout -out ca.key
openssl req -new -x509 -sha256 -key ca.key -out ca.crt
```

## Server Cert and Key
```
openssl ecparam -name prime256v1 -genkey -noout -out server.key
openssl req -new -sha256 -key server.key -out server.csr
openssl x509 -req -in server.csr -CA ca.crt -CAkey ca.key -CAcreateserial -out server.crt -days 1000 -sha256
```

## Client Cert
```
openssl ecparam -name prime256v1 -genkey -noout -out client1.key
openssl req -new -sha256 -key client1.key -out client1.csr
openssl x509 -req -in client1.csr -CA ca.crt -CAkey ca.key -CAcreateserial -out client1.crt -days 1000 -sha256
```

## Verification
in folder certs
```
curl https://localhost:8443 -k -v --cert client1.crt --key client1.key
```

The configuration is according to:
* server: https://caddyserver.com/docs/caddyfile/directives/tls
* reverse-proxy: https://caddyserver.com/docs/caddyfile/directives/reverse_proxy


## Limit certificates
The server provides an option for leaf certificates. Actually you can add cleint crt files to the config.
With the following line:
```
trusted_leaf_cert_file /etc/caddy/keys/client1.crt
```
Only clients with exactly this client-certificate are autorized to request.
You can prove this by creating another client-certificate with the same CA.
The request won't work with the other client.crt, only with the configured one. To prove the client cert would work in general, remove the config line and start the caddy server again.


