---
description: How to configure SASL OAUTHBEARER Authentication
---

# SASL\_OAUTHBEARER

You could pass sasl configs in properties section for each cluster.

### Examples:

Please replace

* \<KAFKA\_NAME> with cluster name
* \<KAFKA\_URL> with broker list
* \<CLIENT\_ID> with client id
* \<CLIENT\_SECRET> with client secret
* \<OAUTH2\_SERVER\_TOKEN\_URL> with the token url from your oauth2 server

#### Running From Docker Image

```bash
docker run -p 8080:8080 \
    -e KAFKA_CLUSTERS_0_NAME=<KAFKA_NAME> \
    -e KAFKA_CLUSTERS_0_BOOTSTRAPSERVERS=<KAFKA_URL> \
    -e KAFKA_CLUSTERS_0_PROPERTIES_SECURITY_PROTOCOL=SASL_SSL \
    -e KAFKA_CLUSTERS_0_PROPERTIES_SASL_MECHANISM=OAUTHBEARER \
    -e KAFKA_CLUSTERS_0_PROPERTIES_SASL_JAAS_CONFIG=org.apache.kafka.common.security.oauthbearer.OAuthBearerLoginModule required clientId=<CLIENT_ID> scope='openid' clientSecret=<CLIENT_SECRET>; \
    -e KAFKA_CLUSTERS_0_PROPERTIES_SASL_LOGIN_CALLBACK_HANDLER_CLASS=org.apache.kafka.common.security.oauthbearer.secured.OAuthBearerLoginCallbackHandler
    -e KAFKA_CLUSTERS_0_PROPERTIES_SASL_OAUTHBEARER_TOKEN_ENDPOINT_URL=<OAUTH2_SERVER_TOKEN_URL>
    -e ssl.endpoint.identification.algorithm: ""
    -d ghcr.io/kafbat/kafka-ui
```

#### Running From Docker-compose file

```yaml

version: '3.4'
services:

  kafbat-ui:
    image: ghcr.io/kafbat/kafka-ui
    container_name: kafbat-ui
    ports:
      - "888:8080"
    restart: always
    environment:
      - KAFKA_CLUSTERS_0_NAME=<KAFKA_NAME>
      - KAFKA_CLUSTERS_0_BOOTSTRAPSERVERS=<KAFKA_URL>
      - KAFKA_CLUSTERS_0_PROPERTIES_SECURITY_PROTOCOL=SASL_SSL
      - KAFKA_CLUSTERS_0_PROPERTIES_SASL_MECHANISM=OAUTHBEARER
      - KAFKA_CLUSTERS_0_PROPERTIES_SASL_JAAS_CONFIG=org.apache.kafka.common.security.oauthbearer.OAuthBearerLoginModule required clientId=<CLIENT_ID> scope='openid' clientSecret=<CLIENT_SECRET>;
      - KAFKA_CLUSTERS_0_PROPERTIES_SASL_OAUTHBEARER_TOKEN_ENDPOINT_URL=<OAUTH2_SERVER_TOKEN_URL>
      - KAFKA_CLUSTERS_0_PROPERTIES_SASL_LOGIN_CALLBACK_HANDLER_CLASS=org.apache.kafka.common.security.oauthbearer.secured.OAuthBearerLoginCallbackHandler
      - KAFKA_CLUSTERS_0_PROPERTIES_PROTOCOL=SASL
      - ssl.endpoint.identification.algorithm: ""
```

#### Configuring by application.yaml

```yaml
kafka:
  clusters:
    - name: local
      bootstrapServers: <KAFKA_URL>
      properties:
        security.protocol: SASL_SSL
        sasl.mechanism: OAUTHBEARER
        sasl.jaas.config: org.apache.kafka.common.security.oauthbearer.OAuthBearerLoginModule required clientId=<CLIENT_ID> scope='openid' clientSecret=<CLIENT_SECRET>;
        sasl.oauthbearer.token.endpoint.url: <OAUTH2_SERVER_TOKEN_URL>
        sasl.login.callback.handler.class: org.apache.kafka.common.security.oauthbearer.secured.OAuthBearerLoginCallbackHandler
        ssl.endpoint.identification.algorithm: ""

```
