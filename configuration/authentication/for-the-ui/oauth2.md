---
description: Examples of setups for different OAuth providers
---

# OAuth2

## Generic configuration

In general, the structure of the Oauth2 config looks as follows:

```yaml
auth:
  type: OAUTH2
  oauth2:
    client:
      <unique_name>:
        clientId: xxx
        clientSecret: yyy
        scope: openid
        client-name: cognito # will be displayed on the login page
        provider: <provider>
        redirect-uri: http://localhost:8080/login/oauth2/code/<provider>
        authorization-grant-type: authorization_code
        issuer-uri: https://xxx
        jwk-set-uri: https://yyy/.well-known/jwks.json
        user-name-attribute: <zzz>
        custom-params:
          type: <provider_type> # fill this if you're gonna use RBAC. Supported values: cognito, google, github, oauth (for other generic providers)
          roles-field: groups # required for RBAC, a field name in OAuth token which will contain user's roles/groups
```

## Service Discovery

For specific providers like Github (non-enterprise) and Google ([see the current list](https://github.com/spring-projects/spring-security/blob/main/config/src/main/java/org/springframework/security/config/oauth2/client/CommonOAuth2Provider.java#L35)), you don't have to specify URIs as they're well known.

Furthermore, other providers that support [OIDC Service Discovery](https://openid.net/specs/openid-connect-discovery-1\_0.html#IssuerDiscovery) allow fetching URIs configuration from a `/.well-known/openid-configuration` endpoint. Depending on your setup, you may only have to set the `issuer-uri` of your provider to enable OIDC Service Discovery.

## Self-signed or private CA certificates

If your OAuth provider (e.g. a self-hosted Keycloak) uses a certificate that isn't trusted by the default JVM truststore, requests to it (issuer discovery, token, user info, JWKS) fail with `PKIX path building failed`. Provide a custom truststore for the OAuth provider connections:

```yaml
auth:
  type: OAUTH2
  oauth2:
    ssl:
      truststore-location: file:/etc/certs/truststore.jks # or AUTH_OAUTH2_SSL_TRUSTSTORELOCATION
      truststore-password: changeit # or AUTH_OAUTH2_SSL_TRUSTSTOREPASSWORD
    client:
      # ...
```

The truststore replaces the default one for OAuth provider connections only, so it has to contain the certificate (or CA) of every OAuth provider you configure.

Note that `server.ssl.trust-store` doesn't apply here: it only configures incoming TLS connections to kafbat-ui itself.

## Provider config examples

### Cognito

```yaml
kafka:
  clusters:
    - name: local
      bootstrapServers: localhost:9092
    # ...

auth:
  type: OAUTH2
  oauth2:
    client:
      cognito:
        clientId: xxx
        clientSecret: yyy
        scope: openid
        client-name: cognito
        provider: cognito
        redirect-uri: http://localhost:8080/login/oauth2/code/cognito
        authorization-grant-type: authorization_code
        issuer-uri: https://cognito-idp.eu-central-1.amazonaws.com/eu-central-1_xxx
        jwk-set-uri: https://cognito-idp.eu-central-1.amazonaws.com/eu-central-1_xxx/.well-known/jwks.json
        user-name-attribute: cognito:username
        custom-params:
          type: cognito
          logoutUrl: https://<XXX>>.eu-central-1.amazoncognito.com/logout #required just for cognito
```

### Google

```yaml
kafka:
  clusters:
    - name: local
      bootstrapServers: localhost:9092
    # ...

auth:
  type: OAUTH2
  oauth2:
    client:
      google:
        provider: google
        clientId: xxx.apps.googleusercontent.com
        clientSecret: GOCSPX-xxx
        user-name-attribute: email
        custom-params:
          type: google
          allowedDomain: kafbat.io # for RBAC
```

### Azure

```yaml
kafka:
  clusters:
    - name: local
      bootstrapServers: localhost:9092
    # ...

auth:
  type: OAUTH2
  oauth2:
    client:
      azure:
        clientId: "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        clientSecret: "somesecret"
        scope: openid
        client-name: azure
        provider: azure
        issuer-uri: "https://login.microsoftonline.com/{tenant_id}/v2.0"
        jwk-set-uri: "https://login.microsoftonline.com/{tenant_id}/discovery/v2.0/keys"
        user-name-attribute: email # Needed for RBAC roles matching
        custom-params: # Needed for RBAC configuration
          type: oauth
          roles-field: roles # AzureAD Role Claims 
```

### GitHub

Example of callback URL for github OAuth app settings:

`https://kafbat.io/login/oauth2/code/github`

For the self-hosted installation find the properties a little bit below.

```yaml
kafka:
  clusters:
    - name: local
      bootstrapServers: localhost:9092
    # ...

auth:
  type: OAUTH2
  oauth2:
    client:
      github:
        provider: github
        clientId: xxx
        clientSecret: yyy
        scope: read:org
        user-name-attribute: login
        custom-params:
          type: github
```

#### Self-hosted/Cloud (GitHub Enterprise Server)

Replace `HOSTNAME` by your self-hosted platform FQDN.

```yaml
kafka:
  clusters:
    - name: local
      bootstrapServers: localhost:9092
    # ...

auth:
  type: OAUTH2
  oauth2:
    client:
      github:
        provider: github
        clientId: xxx
        clientSecret: yyy
        scope: read:org
        user-name-attribute: login
        authorization-uri: http(s)://HOSTNAME/login/oauth/authorize
        token-uri: http(s)://HOSTNAME/login/oauth/access_token
        user-info-uri: http(s)://HOSTNAME/api/v3/user
        custom-params:
          type: github      
```

### Okta

```yaml
auth:
  type: OAUTH2
  oauth2:
    client:
      okta:
        clientId: xxx
        clientSecret: yyy
        scope: [ 'openid', 'profile', 'email', 'groups' ] # default for okta + groups for rbac
        client-name: Okta
        provider: okta
        redirect-uri: http://localhost:8080/login/oauth2/code/okta
        authorization-grant-type: authorization_code
        issuer-uri: https://<okta_domain>.okta.com
        jwk-set-uri: https://yyy/.well-known/jwks.json
        user-name-attribute: sub # default for okta, "email" also available
        custom-params:
          type: oauth
          roles-field: groups # required for RBAC
```

### Keycloak

```yaml
auth:
  type: OAUTH2
  oauth2:
    client:
      keycloak:
        clientId: xxx
        clientSecret: yyy
        scope: openid
        issuer-uri: https://<keycloak_instance>/auth/realms/<realm>
        user-name-attribute: preferred_username
        client-name: keycloak
        provider: keycloak
        custom-params:
          type: oauth
```

### GoAuthentic

```yaml
auth:
  type: OAUTH2
  oauth2:
    client:
      goauthentic:
        provider: goauthentic
        clientId: xxx
        clientSecret: yyy
        scope: [ 'openid', 'profile', 'email' ]
        client-name: goauthentic
        issuer-uri: https://<goauthentic_instance>/application/o/<slug>/
        user-name-attribute: nickname # OR "name", "given_name", "email", "preferred_username"
        redirect-uri: http://localhost:8080/login/oauth2/code/goauthentic
        authorization-grant-type: authorization_code
        custom-params:
          type: oauth
          roles-field: groups
          logoutUrl: https://<goauthentic_instance>/application/o/<slug>/end-session/
```
