---
description: Integrating ksqlDB with Kafbat UI
---

# Integrating ksqlDB with Kafbat UI

## Summary

This document provides configuration details for integrating ksqlDB with Kafbat UI.

Using Kafbat, you can easily integrate [ksqlDB](https://github.com/confluentinc/ksql) with Kafbat UI. This provides a powerful tool for visualizing and managing real-time streaming data.

![ksqlDB architecture](../../.gitbook/assets/ksqldb-1.png)

[ksqlDB](https://github.com/confluentinc/ksql) is a SQL engine based on Kafka Streams. ksqlDB provides a query layer that allows you to build event streaming applications on Kafka topics. Unlike Kafka Streams, ksqlDB allows you to create new streams or materialized views using SQL.

## Configuration

ksqlDB configuration is specified in `yamlApplicationConfig` section of `values.yaml` file.

```yaml
# charts/kafka-ui/values.yaml
yamlApplicationConfig:
  kafka:
    clusters:
      - name: my-kafka-cluster
        # ... omitted for brevity ...
        ksqldbServer: http://my-ksql.data.svc.cluster.local:8088
        ksqldbServerSsl:
          keystoreLocation: <KEYSTORE_LOCATION>
          keystorePassword: <KEYSTORE_PASSWORD>
        ksqldbServerAuth:
          username: <USERNAME>
          password: <PASSWORD>
```

This table outlines the configuration keys needed to set up ksqlDB with Kafka UI, including their types, example values, and descriptions for proper environment configuration.

| Configuration Key | Type | Example Value | Description |
|-------------------|------|---------------|-------------|
| `kafka.clusters.ksqldbServer` | `string` | `http://my-ksql.data.svc.cluster.local:8088` | URL for the KSQL server, used to connect to the KSQL endpoint.|
| `kafka.clusters.ksqldbServerSsl.keystoreLocation` | `string` | `/etc/security/ksql/keystore.jks` | Path to the keystore file for SSL connection to the KSQL server.|
| `kafka.clusters.ksqldbServerSsl.keystorePassword` | `string` | `changeit` | Password for the keystore file, used for SSL authentication. |
| `kafka.clusters.ksqldbServerAuth.username` | `string` | `ksql_user` | Username for authenticating with the KSQL server. |
| `kafka.clusters.ksqldbServerAuth.password` | `string` | `ksql_password` | Password for authenticating with the KSQL server. |

For detailed schema structure of `yamlApplicationConfig` values in kafka-ui chart, refer to the [kafbat-ui-api.yaml](https://github.com/kafbat/kafka-ui/blob/main/contract/src/main/resources/swagger/kafbat-ui-api.yaml) file.
