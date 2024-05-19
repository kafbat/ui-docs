# Authentication

## LDAP authentication doesn't work

Add the following env/config property and check your logs upon authentication:

```
LOGGING_LEVEL_ORG_SPRINGFRAMEWORK_SECURITY_LDAP_USERDETAILS: "TRACE"
```
