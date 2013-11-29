Apache SSL
==========

```
$domain=mydomain.com
openssl genrsa -des3 -out $domain.key 1024
openssl rsa -in $domain.key -out $domain.key.insecure
mv $domain.key $domain.key.secure
mv $domain.key.insecure $domain.key
openssl req -new -key $domain.key -out $domain.csr
openssl x509 -req -days 365 -in $domain.csr -signkey $domain.key -out $domain.crt
sudo cp $domain.crt /etc/ssl/certs
sudo cp $domain.key /etc/ssl/private
```

```
<VirtualHost *:443>
  SSLEngine on
  SSLCertificateFile    /etc/ssl/certs/castilhor.com.crt
  SSLCertificateKeyFile /etc/ssl/private/castilhor.com.key
</VirtualHost>
```

https://help.ubuntu.com/10.04/serverguide/certificates-and-security.html
