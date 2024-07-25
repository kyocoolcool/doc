#Keycloak setting

Reference document: https://www.keycloak.org/getting-started/getting-started-openshift
```Bash
oc process -f keycloak.yaml \
-p KEYCLOAK_ADMIN=admin \
-p KEYCLOAK_ADMIN_PASSWORD=admin \
-p NAMESPACE=mongo \
| oc create -f -
```

### Admin console
https://keycloak-mongo.apps.ocp-cloud.pnr.com/admin/master/console/

### Account console
https://keycloak-mongo.apps.ocp-cloud.pnr.com/realms/myrealm/account/

### Secure Vue.js apps with Keycloak | DevNation Tech Talk
https://developers.redhat.com/devnation/tech-talks/secure-vuejs-keycloak

### Vue3+Keycloak
https://blog.devgenius.io/security-in-vuejs-3-0-with-authentication-and-authorization-by-keycloak-part-1-ae884889fa0d?gi=70c8c04627a4