### Existing secret usage
For example, in auth section you want to pass sensitive values like `clientId` and `clientSecret` as a secret to be sure that such data will not be exposed through code. `existingSecret` field will help you with it. You need to create appropriate Kubernetes Secret with same name and pass there values that you need.
Example:
```yaml
existingSecret: "kafka-ui"
yamlApplicationConfig:
  auth:
    type: OAUTH2
    oauth2:
      client:
        google:
          provider: google
          user-name-attribute: <zzz>
          custom-params:
            type: google
            allowedDomain: <your_domain>
```
Where `existingSecret` will contain such fields as `AUTH_OAUTH2_CLIENT_GOOGLE_CLIENTID` and `AUTH_OAUTH2_CLIENT_GOOGLE_CLIENTSECRET`.