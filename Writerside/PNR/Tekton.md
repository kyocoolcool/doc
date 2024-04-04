Ref: https://redhat-scholars.github.io/tekton-tutorial/tekton-tutorial

default-route-openshift-image-registry.apps.ocp-cloud.pnr.com

securityContext:
capabilities:
add:
- SETFCAP

tkn task start -n tektontutorial build-app \
--param contextDir='springboot' \
--param destinationImage='image-registry.openshift-image-registry.svc:5000/tektontutorial/tekton-tutorial-greeter' \
--param storageDriver='vfs' \
--showlog