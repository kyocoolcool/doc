---
title: Docker tips
author: Chris Chen
language: en-US,ch-TW
tags: [document, docker]
abstract: |
Docker Tips.
---
# Docker 操作

紀錄Docker操作.

## docker login
在mac,用https登入私有image registry,
修改daemon.json
`vim /System/Volumes/Data/Users/jinchangchen/.docker/daemon.json`

添加`"insecure-registry": ["192.168.0.131"]`  #192.168.0.131為image registry ip.

