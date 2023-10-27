# Confluent Control Center (c3) with Windows Server Active Directory
Simple Project to Confirm connectivity between a Confluent Platform Cluster (with C3) and Active Directory

This will provide all the necessary steps for setting up Confluent Platform's Control Center (C3) to use simple Active Directory authorization:

<https://docs.confluent.io/platform/current/control-center/security/c3-auth-ldap.html>


debug

docker exec -ti control-center bash

## Troubleshooting

Make sure the volumes have been mounted on the `control-center` instance and ensure that you can access the `jaas.conf` file:

```bash
docker-compose exec control-center bash
```

```bash
cd /tmp/
cat jaas.conf
```

### Debug level logging

You can add `-Djava.security.debug=all -Djava.security.auth.debug=all -Dorg.eclipse.jetty.util.log.IGNORED=true` to your `CONTROL_CENTER_OPTS` in your `docker-compose.yaml` file

```properties
CONTROL_CENTER_OPTS: -Djava.security.auth.login.config=/tmp/jaas.conf -Djava.security.debug=all -Djava.security.auth.debug=all -Dorg.eclipse.jetty.util.log.IGNORED=true
```

### Testing with LDAPSearch

```bash
ldapsearch -x -b "CN=Users,DC=ad-confluent,dc=io" -H ldap://192.168.1.236 -D "cn=Alex,CN=Users,DC=ad-confluent,DC=io" -W
```
