加入yum-config-manager

sudo yum install yum-utils

加入yum docker repository

sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo

安裝docker

sudo yum install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

啟動Docker

sudo systemctl enable docker --now