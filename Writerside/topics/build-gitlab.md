docker run --detach \
--hostname gitlab.pnr.com \
--add-host=youtrack.pnr.com:10.11.121.17 \
--name gitlab \
--restart always \
--publish 443:443 --publish 80:80 \
--volume /opt/gitlab/config:/etc/gitlab \
--volume /opt/gitlab/logs:/var/log/gitlab \
--volume /opt/gitlab/data:/var/opt/gitlab \
gitlab/gitlab-ce:latest