```Bash
# mkdir -p -m 750 /opt/youtrack/data \
/opt/youtrack/logs \
/opt/youtrack/conf \
/opt/youtrack/backups
```

```Bash
# chown -R 13001:13001 /opt/youtrack/data \
/opt/youtrack/logs \
/opt/youtrack/conf \
/opt/youtrack/backups
```

```Bash
# docker run -it --name youtrack-server \
-v /opt/youtrack/data:/opt/youtrack/data \
-v /opt/youtrack/conf:/opt/youtrack/conf \
-v /opt/youtrack/logs:/opt/youtrack/logs \
-v /opt/youtrack/backups:/opt/youtrack/backups \
-p 8080:8080 \
-p 8443:8443 \
jetbrains/youtrack:2023.1.22653
```

http://youtrack.pnr.com:8080//?wizard_token=cSD4xLh1nq7LMYdyy0in

Token
cSD4xLh1nq7LMYdyy0in

License
YouTrack Default
42bd2ce4dbaf8554bf082946a1c039d4384e81a99ed53fb75215c1b6ace8d34cf66889fdeee418cbe93d54f38f6e94a8a05a0108234b1955582d566283e3d084b23a0b8b52b016b0e610abcbbe7573fa3ff8fb3cdc9346dc94ef5f596f5b12719abcb02e6fae23d98f53f22a01788066aac7298b7fe79cc3dcd6f8df2fb41ab4

docker 
/opt/youtrack/bin/youtrack.sh configure --tls-server-cert-key-file=/home/jetbrains/YouTrack_Server_TLS.pem --tls-server-cert-file=/home/jetbrains/YouTrack_Server_TLS_cert.pem

docker start youtrack-server;docker exec -it youtrack-server /bin/bash -c "/opt/youtrack/bin/youtrack.sh configure --tls-server-cert-key-file=/home/jetbrains/YouTrack_Server_TLS.pem --tls-server-cert-file=/home/jetbrains/YouTrack_Server_TLS_cert.pem"

docker start youtrack-server;docker exec -it youtrack-server /bin/bash -c \
"/opt/youtrack/bin/youtrack.sh configure \
--listen-port=8443 \
--base-url=youtrack.pnr.com \
--secure-mode=tls \
--tls-server-cert-key-file=/home/jetbrains/YouTrack_Server_TLS.pem \
--tls-server-cert-file=/home/jetbrains/YouTrack_Server_TLS_cert.pem \
--tls-redirect-from-http=false"

docker run -it --rm \
-v /opt/youtrack/conf:/opt/youtrack/conf
jetbrains/youtrack:2023.1.22653\
configure --base-url youtrack.pnr.com --listen port 8443


docker run -it --name youtrack-server \
-v /opt/youtrack/data:/opt/youtrack/data \
-v /opt/youtrack/conf:/opt/youtrack/conf \
-v /opt/youtrack/logs:/opt/youtrack/logs \
-v /opt/youtrack/backups:/opt/youtrack/backups \
-p 443:443 \
-p 8080:8080 \
-p 8443:8443 \
--add-host=youtrack.pnr.com:10.11.121.17 \
jetbrains/youtrack:2023.1.22653
