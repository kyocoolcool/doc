# Nexus

## Nexus 啟動Https

```Bash
#!/bin/bash
NEXUS_DOMAIN=nexus.mitac.com
NEXUS_IP_ADDRESS=192.168.0.115
PASSWD=P@ssw0rd
keytool -genkeypair -keystore keystore.jks -storepass ${PASSWD} -keypass ${PASSWD} -alias nexus -keyalg RSA -keysize 2048 -validity 5000 -dname "CN=${NEXUS_DOMAIN}, OU=IT, O=Mitac, L=Beijing, ST=Taipei, C=TW" -ext "SAN=IP:${NEXUS_IP_ADDRESS}" -ext "BC=ca:true"
```

```Bash
keytool -export -alias nexus -keystore keystore.jks -file keystore.cer -storepass P@ssw0rd
```

```Bash
docker run -d -p 8081:8081 -p 8082:8082 -p 8443:8443 --name nexus -v nexus-data:/nexus-data -v nexus:/opt/sonatype/nexus sonatype/nexus3
```

Edit `$data-dir/etc/nexus.properties`

```Bash
# Add a new line
application-port-ssl=8443
# Add new content
nexus-args=${jetty.etc}/jetty.xml,${jetty.etc}/jetty-http.xml,${jetty.etc}/jetty-https.xml,${jetty.etc}/jetty-requestlog.xml
# Add new line
ssl.etc=${karaf.data}/etc/ssl
```

Edit `$install-dir/etc/jetty/jetty-https.xml`

```Bash
 <Set name="KeyStorePath"><Property name="ssl.etc"/>/keystore.jks</Set>
 <Set name="KeyStorePassword">P@ssw0rd</Set>
 <Set name="KeyManagerPassword">P@ssw0rd</Set>
 <Set name="TrustStorePath"><Property name="ssl.etc"/>/keystore.jks</Set>
 <Set name="TrustStorePassword">P@ssw0rd</Set>
```

第一次登入取得admin登入密碼
```Bash
# docker exec -it nexus bash -c "cat /nexus-data/admin.password"
```

```Bash
# 这里的ip换为nexus运行机器的ip
echo subjectAltName = IP:192.168.0.115 > extfile.cnf
# 生成ca
openssl genrsa -out ca.key 2048
openssl req -x509 -new -nodes -key ca.key -days 5000 -out ca.crt
# 生成server证书
openssl genrsa -out nexus.mitac.com.key 2048
openssl req -new -key nexus.mitac.com.key -subj "/CN=nexus.mitac.com" -out nexus.mitac.com.csr
openssl x509 -req -in nexus.mitac.com.csr -CA ca.crt -CAkey ca.key -CAcreateserial -extfile extfile.cnf -out nexus.mitac.com.crt -days 5000
# 将证书导出成pkcs格式
# 这里需要输入密码  用password，如果不用这个，需要修改镜像里的${jetty.etc}/jetty-https.xml，具体操作参考百度。
openssl pkcs12 -export -out keystore.pkcs12 -inkey nexus.mitac.com.key -in nexus.mitac.com.crt
```

Dockerfile
```Bash
FROM sonatype/nexus3
USER root
COPY keystore.pkcs12 /keystore.pkcs12
RUN keytool -v -importkeystore -srckeystore keystore.pkcs12 -srcstoretype PKCS12 -destkeystore keystore.jks -deststoretype JKS -storepass password -srcstorepass password  &&\
    cp keystore.jks /opt/sonatype/nexus/etc/ssl/
USER nexus
```


```Bash
docker build ./ -t nexus-https
```

```Bash
# 8443是nexus ui https访问端口
# 8081是nexus ui http访问端口
# 8082将要用来作为docker代理docker hub的端口
# 8083将要用来作为docker本地仓库的端口
docker run -d --restart=always -p 8443:8443 -p 8081:8081 -p 8082:8082 -p 8083:8083 --name nexus3 -v nexus-data:/nexus-data -v nexus:/opt/sonatype/nexus nexus-https:latest
# 修改nexus的目录权限
docker volume inspect nexus #取得volume路徑
chmod -R 777 /var/lib/docker/volumes/nexus-data/_data
chmod -R 777 /var/lib/docker/volumes/nexus/_data

# 等容器启动，可以正常访问页面之后修改配置文件，开启ssl
vim /var/lib/docker/volumes/nexus-data/_data/etc/nexus.properties 

# 内容如下：
# Jetty section
application-port-ssl=8443
application-port=8081
application-host=0.0.0.0
nexus-args=${jetty.etc}/jetty.xml,${jetty.etc}/jetty-http.xml,${jetty.etc}/jetty-requestlog.xml,${jetty.etc}/jetty-https.xml
nexus-context-path=/${NEXUS_CONTEXT}

#Nexus section
nexus-edition=nexus-pro-edition
nexus-features=\
 nexus-pro-feature
nexus.clustered=false

# 重启容器
docker restart nexus3
```
在client端的服务器上创建 /etc/docker/certs.d/<nexus ip>:8082/文件夹。
将生成ca证书步骤生成的 ca.crt复制到 这个文件夹中。
docker login 试一下。
https://www.jianshu.com/p/2117423c9811