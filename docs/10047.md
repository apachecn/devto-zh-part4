# 转换密钥库文件(。jks)转换成 PEM 证书

> 原文：<https://dev.to/groguelon/convert-a-keystore-file-jks-into-a-pem-certificate-3lpi>

```
# Export the certificate
keytool -export -alias mydomain -file mydomain.der -keystore mycert.jks
# Convert the certificate into PEM
openssl x509 -inform der -in mydomain.der -out mydomain.pem
# Export the key
keytool -importkeystore -srckeystore mycert.jks -destkeystore keystore.p12 -deststoretype PKCS12
# concert PKCS12 key to unencrypted PEM
openssl pkcs12 -in keystore.p12  -nodes -nocerts -out mydomain.key 
```

[来源](https://serverfault.com/a/715841)