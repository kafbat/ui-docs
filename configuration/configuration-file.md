---
description: This page explains configuration file structure
---

# Configuration file

Let's start with that there are two possible ways to configure the app, they can interchange each other or even supplement each other.

There are two ways: YAML config & env. variables config. We **strongly** recommend using YAML in favor of env variables for the most part of the config. You can use env vars to override the default config on some different environments.

[This tool](https://env.simplestep.ca/) can help you to translate your config back and forth from YAML to env vars.

We will mostly provide examples of configs in YAML format, but sometimes single properties might be written in form of env variables.

Rather than writing your config from a scratch, it would be more convenient to use one of the ready-to-go [compose examples](compose-examples.md) and adjust it to your needs.

#### Config file description ####

```yaml
# ======================================================
# Kafka Clusters Configuration
# Define all Kafka cluster-specific settings and related integrations.
# ======================================================
kafka:
  clusters:
    - name: local  # Unique name identifier for the Kafka cluster
      bootstrap-servers: kafka1:9092,kafka2:9092  # List of Kafka broker addresses

      # SSL configuration for secure connection to Kafka brokers
      ssl:
        truststore-location: path/to/truststore/file.jks  # Path to truststore JKS file
        truststore-password: password  # Password to access the truststore
        verify-ssl: true  # Enable SSL certificate verification

      # Schema Registry connection and authentication details
      schemaRegistry: http://schema-registry:8085
      schemaRegistryShowNullValues: true  # Show null fields in Avro messages (default: false)
      schemaRegistryUseFullyQualifiedNames: true  # Use full type names in unions (default: false)
      schema-registry-auth:
        username: schema registry username
        password: schema registry password
      schema-registry-ssl:
        keystore-location: path/to/keystore/file.jks
        keystore-password: password

      # ksqlDB connection and security settings
      ksqldb-server: http://ksqldb-host:8088
      ksqldb-server-auth:
        username: ksqldb-username
        password: ksqldb-password
      ksqldb-server-ssl:
        keystore-location: path/to/keystore/file.jks
        keystore-password: password

      # Kafka Connect REST endpoint and optional authentication
      kafka-connect:
        - name: first  # Identifier for the Connect cluster
          address: http://kafka-connect-host:8083
          username: Auth username
          password: Auth password
          keystore-location: path/to/keystore/file.jks
          keystore-password: keystore password

      # Custom SerDe (Serializer/Deserializer) for interpreting topic data
      serde:
        - name: CustomHexWithEditedDelimiter
          class-name: io.kafbat.ui.serdes.builtin.HexSerde
          file-path: /var/lib/kui-serde/my-kui-serde.jar
          topic-keys-pattern: ".*-events"  # Regex to match applicable topic keys
          topic-values-pattern: ".*-events"  # Regex to match applicable topic values
          properties:
            uppercase: "false"  # Output lowercase hex
            delimiter: ":"  # Byte delimiter

      default-key-serde: String  # Default SerDe for message keys
      default-value-serde: SchemaRegistry  # Default SerDe for message values

      # Monitoring and metrics collection from the cluster
      metrics:
        type: JMX  # Available options: JMX or PROMETHEUS
        port: 9997  # Port to collect JMX metrics
        username: Auth username
        password: Auth password
        ssl: false  # Whether to use SSL for metrics endpoint
        keystore-location: path/to/keystore/file.jks
        keystore-password: password

      # Global Kafka client properties
      properties:
        "security.protocol": SASL_SSL
        "sasl.mechanism": SCRAM-SHA-512
        "sasl.jaas.config": org.apache.kafka.common.security.scram.ScramLoginModule required username="your-username" password="your-password";

      # Kafka Consumer configuration overrides
      consumer-properties:
        "max.partition.fetch.bytes": 1048576

      # Kafka Producer configuration overrides
      producer-properties:
        "enable.idempotence": false

      read-only: true  # Prevent write operations from the UI

      polling-throttle-rate: 0  # Delay (seconds) between polling intervals in UI; 0 = no throttling

      # Data masking rules for messages shown in the UI
      masking:
        - type: MASK  # Action: MASK, REMOVE, or REPLACE
          fields: [ "id", "name" ]  # Specific fields to apply masking to
          fields-name-pattern: "id.*"  # Regex pattern to match field names
          masking-chars-replacement: ["A", "a", "N", "_"]  # Optional: override default mask characters
          replacement: "***MASKED_FIELD_NAME***"  # Replacement string for masked field names
          topic-values-pattern: events-with-ids-.*  # Apply on topic values matching this pattern
          topic-keys-pattern: events-with-ids-.*  # Apply on topic keys matching this pattern

      # Audit logging configuration
      audit:
        topic-audit-enabled: false  # Enable/disable Kafka topic audit logs
        console-audit-enabled: false  # Enable/disable console audit output
        topic: audit-topic-name  # Kafka topic for audit entries
        audit-topics-partitions: 3  # Number of partitions for the audit topic
        level: ALL  # ALL = log all actions, ALTER_ONLY = only mutating actions
        audit-topic-properties:
          "retention.ms": 43200000  # Audit log retention in ms (12 hours)

  # Prefix to identify internal Kafka topics used by system tools
  internalTopicPrefix: "__"

  # Timeout for admin Kafka operations (in milliseconds)
  admin-client-timeout: 30000

  # Polling behavior for consumer previews in the UI
  polling:
    poll-timeout-ms: 1000  # How long to wait for new records (ms)
    max-page-size: 500  # Maximum number of records per poll
    default-page-size: 100  # Default number of records per poll

# ======================================================
# Model Context Protocol config
# ======================================================
mcp:
  enabled: true # Enable MCP (ModelContextProtocol) endpoint

# ======================================================
# OAuth2 Authentication Configuration
# Used for authenticating users via external identity providers.
# ======================================================
auth:
  oauth2:
    client:
      client_name:
        client-id: xxx
        client-secret: yyy
        scope: openid
        client-name: cognito  # Display name on the login screen
        provider: <provider>  # Path alias used in redirect URI
        redirect-uri: http://localhost:8080/login/oauth2/code/<provider>
        authorization-grant-type: authorization_code
        issuer-uri: https://xxx  # Identity provider URL
        jwk-set-uri: https://yyy/.well-known/jwks.json  # Public key set URL
        user-name-attribute: <zzz>  # JWT field used as the user's name
        custom-params:
          type: <provider_type>  # Optional RBAC integration type (e.g. cognito, google, github, oauth)
          roles-field: groups  # Field containing user roles/groups

    resource-server:
      jwt:
        jwk-set-uri: http://jwk/uri
        jws-algorithms: ["RS256"]
        issuer-uri: http://issuer/uri
        public-key-location:  # Optional path to JWT public key
        audiences: []  # List of accepted audience values in JWT
        authority_prefix:  # Optional prefix added to authorities
        authorities-claim-delimiter:  # Regex delimiter for splitting roles
        authorities-claim-name:  # Claim name holding roles/permissions
        principal-claim-name:  # Claim used for determining the username

      opaque-token:
        client-id:  # Client ID used for introspection
        client-secret:  # Secret used for introspection
        introspection-uri:  # URL to the introspection endpoint

# ======================================================
# LDAP Configuration
# Used for user and group resolution in RBAC when using LDAP.
# ======================================================
spring:
  ldap:
    urls: ldap://localhost:10389
    base: "cn={0},ou=people,dc=planetexpress,dc=com"
    admin-user: "cn=admin,dc=planetexpress,dc=com"
    admin-password: "GoodNewsEveryone"
    user-filter-search-base: "dc=planetexpress,dc=com"
    user-filter-search-filter: "(&(uid={0})(objectClass=inetOrgPerson))"
    group-filter-search-base: "ou=people,dc=planetexpress,dc=com"

# ======================================================
# Role-Based Access Control (RBAC)
# Configure roles, user/group bindings, and permissions.
# ======================================================
rbac:
  roles:
    - name: "memelords"  # Role name
      clusters:
        - local  # Clusters this role applies to
      subjects:
        - provider: oauth_google  # e.g. oauth_{client_name}, ldap
          type: domain  # domain, organization, user, group
          value: "kafbat.dev"
          regex: false
      permissions:
        - resource: applicationconfig  # Resource types: applicationconfig, clusterconfig, topic, consumer, schema, connect, ksql, acl, audit
          value: ".*"
          actions: all  # Allowed actions: read, write, all

# ======================================================
# WebClient Settings
# Configuration for HTTP clients used by the app.
# ======================================================
webclient:
  response-timeout-ms: 20000  # Timeout for all outgoing HTTP requests
  max-in-memory-buffer-size: 20MB  # Maximum buffer size for handling responses

# ======================================================
# Spring Boot Actuator
# Enables health, info, and Prometheus endpoints for monitoring.
# ======================================================
management:
  endpoint:
    info:
      enabled: true
    health:
      enabled: true
  endpoints:
    web:
      exposure:
        include: "info,health,prometheus"

# ======================================================
# Application Logging Configuration
# Configure logging levels for specific packages.
# ======================================================
logging:
  level:
    root: INFO
    io.kafbat.ui: DEBUG
    reactor.netty.http.server.AccessLog: INFO
    org.hibernate.validator: WARN

```

#### Providing a config path for the app instance:

**Docker**: 

```bash
docker run -it \
  -p 8080:8080 \
  -e spring.config.additional-location=/tmp/config.yml \
  -v /tmp/kui/config.yml:/tmp/config.yml \
  ghcr.io/kafbat/kafka-ui
```

**Docker compose**:&#x20;

```yaml
services:
  kafbat-ui:
    container_name: kafbat-ui
    image: ghcr.io/kafbat/kafka-ui
    environment:
      KAFKA_CLUSTERS_0_NAME: local
      # other properties, omitted
      SPRING_CONFIG_ADDITIONAL-LOCATION: /config.yml
    volumes:
      - /tmp/config.yml:/config.yml
```

**Jar**: `java -Dspring.config.additional-location=<path-to-application-local.yml> -jar <path-to-jar>.jar`
