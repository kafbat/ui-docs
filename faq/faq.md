# FAQ

### Basic (username password) authentication

[basic-authentication.md](../configuration/authentication/basic-authentication.md "mention")

### Role-based access control

[rbac-role-based-access-control](../configuration/rbac-role-based-access-control/ "mention")

### OAuth 2

[oauth2.md](../configuration/authentication/oauth2.md "mention")

### LDAP

See [this](https://github.com/kafbat/kafka-ui/blob/main/documentation/compose/auth-ldap.yaml#L29) example.

### Active Directory (LDAP)

See [this](https://github.com/kafbat/kafka-ui/blob/main/documentation/compose/auth-ldap.yaml#L29) example.

### SAML

Planned, see [#478](https://github.com/kafbat/kafka-ui/issues/478)

### Smart filters syntax

We use CEL syntax for smart message filtersVariables bound to the context:

1. key (json if possible)
2. value (json if possible)
3. keyAsText
4. valueAsText
5. header
6. partition
7. timestampMs

JSON parsing logic:Key and Value (if parsing to JSON is available) are bound as JSON objects, otherwise as nulls.Filter examples:

1. `has(record.keyAsText) && record.keyAsText.matches(".*[Gg]roovy.*")` - regex for key as a string
2. `has(record.key.name.first) && record.key.name.first == 'user1'` - in case if the value is json
3. `record.headers.size() == 1 && !has(record.headers.k1) && record.headers['k2'] == 'v2'`

### Can I use the app as API?

Sure! Swagger declaration is located [here](https://github.com/kafbat/kafka-ui/blob/main/contract/src/main/resources/swagger/kafbat-ui-api.yaml).

### My OIDC / OAuth provider uses self-signed certificates, how do I add them to the truststore?

```
server:
  ssl:
    trust-store: classpath:keycloak-truststore.jks
    trust-store-password: changeit
```
