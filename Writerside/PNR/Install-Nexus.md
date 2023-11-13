docker run with persistence data and https port

```Bash
$ docker volume create --name nexus-data
$ docker run -d -p 8081:8081 -p 8082:8082 --name nexus -v nexus-data:/nexus-data sonatype/nexus3
```
防火牆開通port
```Bash
$ firewall-cmd --zone=public --add-port=8081/tcp --permanent
$ firewall-cmd --zone=public --add-port=8082/tcp --permanent
firewall-cmd --reload
```

```Bash
$ docker volume inspect nexus-data #查詢掛載路徑
```

mount point
```Bash
$ ls -l /var/lib/docker/volumes/nexus-data
```

default account
admin `password > admin.password #密碼文件`