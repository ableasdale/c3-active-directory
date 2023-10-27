# Confluent Control Center (C3) with Windows Server Active Directory

An example project to demonstrate the integration between Confluent Control Center (C3) and Windows Active Directory, using AD Groups to control access to the groups.

This will provide all the necessary steps for setting up Confluent Platform's Control Center (C3) to use simple Active Directory authorization.  The configuration is documented here:

<https://docs.confluent.io/platform/current/control-center/security/c3-auth-ldap.html>

## Getting Started

This walkthrough has been tested with a VirtualBox installation of a trial version of Windows Server 2019, with Active Directory Services installed and with the VM being promoted to an AD Domain Controller.

Two users have been set up in Active Directory (in Windows Server, run `dsa.msc` to access the `Active Directory Users and Computers` component).  

- **Alex** is a member of the **Administrators** group
- **Bob** is a member of the **Guests** group

Alex:
![Administrator](img/admin.png)

Bob:
![Administrator](img/guest.png)

![Apache Directory Studio](img/directorystudio.png)

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

For a successful login (with Debug logging configured), you will see:

```log
control-center   | 	[LoginContext]: org.eclipse.jetty.jaas.spi.LdapLoginModule loaded via reflection
control-center   | 	[LoginContext]: org.eclipse.jetty.jaas.spi.LdapLoginModule login success
control-center   | 	[LoginContext]: org.eclipse.jetty.jaas.spi.LdapLoginModule commit success
control-center   | [2023-10-27 14:38:15,066] INFO Attempting authentication: CN=Alex,CN=Users,DC=ad-confluent,DC=io (org.eclipse.jetty.jaas.spi.LdapLoginModule)
control-center   | [2023-10-27 14:38:15,070] INFO Attempting authentication: CN=Alex,CN=Users,DC=ad-confluent,DC=io (org.eclipse.jetty.jaas.spi.LdapLoginModule)
control-center   | 	[LoginContext]: org.eclipse.jetty.jaas.spi.LdapLoginModule login success
control-center   | 	[LoginContext]: org.eclipse.jetty.jaas.spi.LdapLoginModule commit success
control-center   | 	[LoginContext]: org.eclipse.jetty.jaas.spi.LdapLoginModule loaded via reflection
control-center   | 	[LoginContext]: org.eclipse.jetty.jaas.spi.LdapLoginModule login success
control-center   | 	[LoginContext]: org.eclipse.jetty.jaas.spi.LdapLoginModule commit success
```

### Testing with LDAPSearch

```bash
ldapsearch -x -b "CN=Users,DC=ad-confluent,dc=io" -H ldap://192.168.1.236 -D "cn=Alex,CN=Users,DC=ad-confluent,DC=io" -W
```
