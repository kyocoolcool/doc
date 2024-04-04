mvn clean package -Pprod

## 取得base image arch amd64, 與 Mac arch arm64 不同
podman pull --arch amd64 openjdk:21-slim

## build image tag for openshift imageStream and version
podman build -t default-route-openshift-image-registry.apps.ocp-cloud.pnr.com/mongo/hyweb-services:1.1 .


oc login --token=sha256~UzgCkt8ZiUzxOi6PEQPBzbATYAPu5KJlQoC7UwEhwA8 --server=https://api.ocp-cloud.pnr.com:6443 --insecure-skip-tls-verify
oc image registry: default-route-openshift-image-registry.apps.ocp-cloud.pnr.com

oc login to image registry
podman login -u appis -p sha256~UzgCkt8ZiUzxOi6PEQPBzbATYAPu5KJlQoC7UwEhwA8 default-route-openshift-image-registry.apps.ocp-cloud.pnr.com
-> podman login -u appis -p $(oc whoami -t) default-route-openshift-image-registry.apps.ocp-cloud.pnr.com

upload image to image registry
podman push default-route-openshift-image-registry.apps.ocp-cloud.pnr.com/mongo/hyweb-services:1.1 --tls-verify=false

oc create deployment hyweb-services --image=default-route-openshift-image-registry.apps.ocp-cloud.pnr.com/mongo/hyweb-services:1.2
oc expose deployment hyweb-services --port=8080 --name=hyweb-services
oc expose svc/hyweb-services

## OpenShift允許 root run container
oc adm policy add-scc-to-user anyuid -z default


## image registry
### 外部url
podman login https://registry.ocp-cloud.pnr.com:8443 --tls-verify=false
### 內部url
podman login image-registry.openshift-image-registry.svc:5000


## install operator
ref: https://medium.com/@abhichandra1998/installing-operators-in-a-disconnected-openshift-cluster-129af21cd904
podman login -u='15175903|pnr' -p=eyJhbGciOiJSUzUxMiJ9.eyJzdWIiOiJiM2Q3ZmFiYzY1OTk0MGNiOTZiYzc5YjNiZThmYTVkMCJ9.w9zD87YTpawLB5q6CmYUM1Hky0z9KI5znMXKATIPM3p8kTUOPrwZAEX0BGOHIXvfqMxoobDuNIgQFN8nYIOpJsJVM0d9vsXoFRodVRh3VIL65WRj6RhfY7W6bBEjguweCx7eDwhJC8I73LOV2OGt_3sehe_oNIOpY4rMNyKBE8EWh0_KptaMGkhVjfKyL0JFjHqd0OXVIAXQoMmUST_psND7aTiWC2usV4_a0LK2-wSgb-62-63qNV20n79aBrFI2Xkv7xyU-liM_vEzqJgAkRvTKxDnRPTGBvu9LoRw9W1Awozbhm6zHJrKRpGJ3gezq_Wd9gTGPQYTmSVfOGw9mgou06ax7zOIjIa7T_7OXVeqkrQ-pxejcNOL_K5Bjn87ckAChFx8ER44WhZTuYS1FgCmjBaXlgQri8InK7Z0sNNf4AUVDjvEGRKEz9S8H6BqxPfYRVBVtkKOkvCZiG0qD3_FygjSOr8V623-gl4MjA6zhfm0oB9oj__ejQ97TvVmWd4d7PQIce-eX0ydlH0t-168AIOB5fTz1vUldjI-tKGrEOvcqritA7Cv_H81ZeQLGkhpMfN1PrmQEbSptHCXKI8mp8JCQQg9TYo8onR8_81jEKrxFf3yanyOvf4V0iDlWWI2AC1HRoZdMTppRxHnHM9vHR-RXHXxByAWOKFKjIc registry.redhat.io
oc-mirror list operators --catalog registry.redhat.io/redhat/redhat-operator-index:v4.12 --package serverless-operator
oc mirror --config=imageSetConfig.yaml file://download
oc-mirror --from=download/ docker://registry.ocp-cloud.pnr.com:8443/pipelines/serverless


### upload operator
podman tag registry.redhat.io/openshift-pipelines/pipelines-rhel8-operator:v1.11.3-12  registry.ocp-cloud.pnr.com:8443/openshift-pipelines/pipelines-rhel8-operator:v1.11.3-12
podman push registrypodman push registry.ocp-cloud.pnr.com:8443/openshift-pipelines/pipelines-rhel8-operator:v1.11.3-12 --tls-verify=false --remove-signatures


