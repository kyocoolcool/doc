# Self-sign

## Generate a Certificate Authority Certificate

ca.key
```Bash
# openssl genrsa -out ca.key 4096
```

ca.crt
```Bash
# openssl req -x509 -new -nodes -sha512 -days 3650 \
 -subj "/C=TW/ST=Taipei/L=Taipei/O=Mitac/OU=IT/CN=ca.mitac.com" \
 -key ca.key \
 -out ca.crt
```

## Generate a Server Certificate

```Bash
# openssl genrsa -out harbor.mitac.com.key 4096
```

```Bash
# openssl req -sha512 -new \
    -subj "/C=TW/ST=Taipei/L=Taipei/O=Mitac/OU=IT/CN=harbor.mitac.com" \
    -key harbor.mitac.com.key \
    -out harbor.mitac.com.csr
```

```Bash
# cat > v3.ext <<-EOF
authorityKeyIdentifier=keyid,issuer
basicConstraints=CA:FALSE
keyUsage = digitalSignature, nonRepudiation, keyEncipherment, dataEncipherment
extendedKeyUsage = serverAuth
subjectAltName = @alt_names

[alt_names]
DNS.1=harbor.mitac.com
DNS.2=harbor.mitac
DNS.3=harbor
EOF
```

```Bash
openssl x509 -req -sha512 -days 3650 \
    -extfile v3.ext \
    -CA ca.crt -CAkey ca.key -CAcreateserial \
    -in harbor.mitac.com.csr \
    -out harbor.mitac.com.crt
```

```Bash
cp harbor.mitac.com.crt /data/cert/
cp harbor.mitac.com.key /data/cert/
```

```Bash
openssl x509 -inform PEM -in harbor.mitac.com.crt -out harbor.mitac.com.cert
```

```Bash
cp harbor.mitac.com.cert /etc/docker/certs.d/harbor.mitac.com/
cp harbor.mitac.com.key /etc/docker/certs.d/harbor.mitac.com/
cp ca.crt /etc/docker/certs.d/harbor.mitac.com/
```