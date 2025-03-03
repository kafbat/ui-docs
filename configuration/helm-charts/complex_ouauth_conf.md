
# Goals

This configuration provides a complex setup for enabling SASL_SSL with OAUTHBEARER authentication between a UI Kafka client and a Kafka cluster, including JWT and scope validation.
It uses the RedHat Kafka distribution called strimzi, that work like any kafka cluster excpet it use a specific callback handler class for authentication, which can be replaced with the default org.apache or Confluent library, or even a custom implementation.
Additionally, the Schema Registry used in this setup is the Red Hat distribution, Apicurio Registry.

The prerequesit at this are:
 - A kubernet secret with your kafka user/password (given by your authent server for example) :

```yaml
kind: Secret
apiVersion: v1
metadata:
  name: kafkauiclientsecretadfs
data:
  clientid: kafka-ui-kafka-clientId
  clientsecret: my_user_password
```

- A user provisionned inside ADFS, that should look like:

```yaml
  applicationGroup: ADFS--infra-kafka
  id: kafka-ui-kafka-clientId
  issuer: https://my_adfs.com/adfs
  permissions:
    - audience: api://kafka.jwt
      scopes:
        - openid
        - allatclaims
    - audience: kafka-ui-kafka-clientId
      scopes:
        - openid
        - allatclaims
  redirectURI:
    - https://my-kafka-ui.com/login/oauth2/code/auth0
```

 - A the user inside kafka that should have a name equal to: ```kafka-ui-kafka-clientId``` and ACL needed to make the Kafka-Ui work [see](https://ui.docs.kafbat.io/quick-start/prerequisites/permissions/required-acls#acls-for-standalone-kafka)


- A secret containing the truststore needed for Kafka Broker and Authentication server ssl communications

```yaml
kind: Secret
apiVersion: v1
metadata:
  name: truststore-secret
data:
  truststore.jks: your_trust.jks  
type: Opaque
```

# helm Configuration

```yaml
    spec:
      containers:
        - env:
            - name: KAFKA_CLUSTERS_0_BOOTSTRAPSERVERS
              value: broker-kafka-extoauth-bootstrap:9096
            - name: KAFKA_CLUSTERS_0_ZOOKEEPER
              value: broker-zookeeper-client:2181
            - name: JAVA_OPTS
              value: -Djavax.net.ssl.trustStore=/var/run/secrets/truststore.jks -Djavax.net.ssl.trustStorePassword=changeit
            - name: KAFKA_CLUSTERS_0_SCHEMAREGISTRY
              value: https://broker-apicurio-schema-registry-service:8443/apis/ccompat/v7
            - name: KAFKA_CLUSTERS_0_PROPERTIES_SECURITY_PROTOCOL
              value: SASL_SSL
            - name: KAFKA_CLUSTERS_0_PROPERTIES_SASL_MECHANISM
              value: OAUTHBEARER
            - name: KAFKA_CLUSTERS_0_PROPERTIES_SASL_LOGIN_CALLBACK_HANDLER_CLASS
              value: io.strimzi.kafka.oauth.client.JaasClientOauthLoginCallbackHandler
            - name: OAUTH_CLIENT_ID
              valueFrom:
                secretKeyRef:
                  key: clientid
                  name: kafkauiclientsecretadfs
            - name: OAUTH_CLIENT_SECRET
              valueFrom:
                secretKeyRef:
                  key: clientsecret
                  name: kafkauiclientsecretadfs
            - name: OAUTH_USERNAME_CLAIM
              value: appid
            - name: KAFKA_CLUSTERS_0_PROPERTIES_SASL_JAAS_CONFIG
              value: org.apache.kafka.common.security.oauthbearer.OAuthBearerLoginModule required oauth.token.endpoint.uri="https://my_adfs.com/adfs/oauth2/token" ;
            - name: OAUTH_SCOPE
              value: api://kafka.jwt/allatclaims
            - name: KAFKAUICLIENTSECRET
              valueFrom:
                secretKeyRef:
                  key: clientsecret
                  name: kafkauiclientsecretadfs
            - name: KAFKAUICLIENTID
              valueFrom:
                secretKeyRef:
                  key: clientid
                  name: kafkauiclientsecretadfs
            - name: KAFKA_CLUSTERS_0_NAME
              value: broker-kafka-lab
            - name: SPRING_CONFIG_LOCATION
              value: /kafka-ui/config.yml
          volumeMounts:
            - mountPath: /var/run/secrets/
              name: truststore
            - mountPath: /kafka-ui/
              name: kafka-ui-yaml-conf
      volumes:
        - name: truststore
          secret:
            defaultMode: 420
            secretName: truststore-secret
        - configMap:
            name: kafkaui-config-fromvalues
          name: kafka-ui-yaml-conf
```

# Re use the kafka User for web interface authentifaction

You can also use this same oauth configuration to enable RBAC in the UI with:


```yaml
kind: ConfigMap
apiVersion: v1
metadata:
  labels:
  name: kafkaui-config-fromvalues
data:
  config.yml: |-
    auth:
      oauth2:
        client:
          oauth0:
            authorization-grant-type: authorization_code
            authorization-uri: https:/my_adfs.com/adfs/oauth2/authorize/
            client-name: route
            clientId: ${KAFKAUICLIENTID}
            clientSecret: ${KAFKAUICLIENTSECRET}
            custom-params:
              logoutUrl: https://my-kafka-ui.com/logout
              roles-field: groups
              type: oauth
            jwk-set-uri: https://my_adfs.com/adfs/discovery/keys
            provider: oauth
            redirect-uri: https://my-kafka-ui.com/login/oauth2/code/auth0
            scope:
            - openid
            token-uri: https://my_adfs.com/adfs/oauth2/token/
            user-name-attribute: email
      type: OAUTH2
    rbac:
      roles:
      - clusters:
        - broker-kafka
        name: Plateform_ops_team
        permissions:
        - actions:
          - ALL
          resource: clusterconfig
        - actions:
          - ALL
          resource: applicationconfig
        - actions:
          - ALL
          resource: topic
          value: .*
        - actions:
          - ALL
          resource: consumer
          value: .*
        - actions:
          - ALL
          resource: schema
          value: .*
        - actions:
          - ALL
          resource: acl
          value: .*
        subjects:
        - provider: oauth
          type: role
          value: ADgroupPlatform
      - clusters:
        - broker-kafka
        name: Admin_team
        permissions:
        - actions:
          - ALL
          resource: clusterconfig
        - actions:
          - ALL
          resource: topic
          value: .*
        - actions:
          - ALL
          resource: consumer
          value: .*
        - actions:
          - ALL
          resource: schema
          value: .*
        - actions:
          - ALL
          resource: acl
          value: .*
        subjects:
        - provider: oauth
          type: role
          value: ADgroupProd
      - clusters:
        - broker-kafka
        name: Developer_team
        permissions:
        - actions:
          - ALL
          resource: clusterconfig
        - actions:
          - ALL
          resource: topic
          value: .*
        - actions:
          - ALL
          resource: consumer
          value: .*
        - actions:
          - VIEW
          - edit
          resource: schema
          value: .*
        - actions:
          - ALL
          resource: acl
          value: .*
        subjects:
        - provider: oauth
          type: role
          value: ADgroupDev
      - clusters:
        - broker-kafka
        name: Integration_team
        permissions:
        - actions:
          - ALL
          resource: clusterconfig
        - actions:
          - ALL
          resource: topic
          value: .*
        - actions:
          - ALL
          resource: consumer
          value: .*
        - actions:
          - ALL
          resource: schema
          value: .*
        - actions:
          - ALL
          resource: acl
          value: .*
        subjects:
        - provider: oauth
          type: role
          value: ADgroupIntegration
```
