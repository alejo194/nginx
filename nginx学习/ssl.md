```bash
openssl genrsa -out ca-key.pem 2048
openssl req -new -key ca-key.pem -out ca-req.csr -subj "/C=CN/ST=GZ/L=DG/O=fish/OU=fish/CN=CA"
openssl x509 -req -in ca-req.csr -out ca-cert.pem -signkey ca-key.pem -days 3650
服务器端私钥
openssl genrsa -out server-key.pem 2048
openssl req -new -out server-req.csr -key server-key.pem -subj "/C=CN/ST=GZ/L=DG/O=fish/OU=fish/CN=*.maxwinfish.com"
openssl x509 -req -in server-req.csr -out server-cert.pem -signkey server-key.pem -CA ca-cert.pem -CAkey ca-key.pem -CAcreateserial -days 3650
```
