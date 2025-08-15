---
description: The list of supported auth providers for RBAC
---

# Supported Identity Providers

### Generic OAuth

Any OAuth provider not on the list of all providers below this one.

Set up the auth itself first, docs [here](../authentication/for-the-ui/oauth2.md) and [here](../authentication/for-the-ui/sso-guide.md). **Don't forget "custom-params.type: oauth".**

```yaml
      subjects:
        - provider: oauth
          type: role
          value: "role-name"
        - provider: oauth
          type: user
          value: "zoidberg"
```

### Google

Set up google auth [first](../authentication/for-the-ui/oauth2.md#google)

```yaml
        - provider: oauth_google
          type: domain
          value: "memelord.lol"
        - provider: oauth_google
          type: user
          value: "kek@memelord.lol"
```

### Github

Set up github auth [first](../authentication/for-the-ui/oauth2.md#github)

```yaml
        - provider: oauth_github
          type: organization
          value: "kafbat"
        - provider: oauth_github
          type: user
          value: "memelord"
        - provider: oauth_github
          type: team
          value: "kafbat/backend"
```

### Cognito

Set up cognito auth [first](../authentication/for-the-ui/oauth2.md#cognito)

```yaml
        - provider: oauth_cognito
          type: user
          value: "zoidberg"
        - provider: oauth_cognito
          type: group
          value: "memelords"
```

### Azure

Set up AzureAD OAUTH2 auth [first](../authentication/for-the-ui/oauth2.md#azure)

```yaml
        - provider: oauth
          type: user
          value: "zoidberg@memelords.lol"
        - provider: oauth
          type: role
          value: "admin" # From AzureAD Role Claims
```

### LDAP

Set up LDAP auth [first](../authentication/for-the-ui/ldap-active-directory.md)

```yaml
        - provider: ldap
          type: group
          value: "admin_staff"
        - provider: ldap
          type: user
          value: "pepega"
```

### Active Directory

```yaml
       - provider: ldap_ad
         type: group
         value: "admin_staff"
       - provider: ldap_ad
         type: user
         value: "zoidberg"
```

### Okta

You can map Okta Groups to roles. First, confirm that your okta administrator has included the `group` claim or the groups will not be passed in the auth token.

Ensure `roles-field` in the auth config is set to `groups` and that `groups` is included in the `scope`, see [here](../authentication/for-the-ui/oauth2.md#okta) for more details.

Configure the role mapping to the okta group via generic provider mentioned above:

```yaml
      subjects:
        - provider: oauth
          type: role
          value: "<okta-group-name>"
```

### GoAuthentic

You can map GoAuthentic Groups to roles. First, confirm that your GoAuthentic administrator has included the `profile` claim or the groups will not be passed in the auth token.

Ensure `roles-field` in the auth config is set to `groups` and that `profile` is included in the `scope`, as groups are passed by default in the profile scope. See [here](../authentication/for-the-ui/oauth2.md#goauthentic) for more details.

Configure the role mapping to the GoAuthentic group via generic provider mentioned above:

```yaml
      subjects:
        - provider: oauth
          type: role
          value: "<goauthentic-group-name>"
```
