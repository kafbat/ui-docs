---
description: How to configure Google Cloud IAM Authentication
---

# 📘 Google Cloud IAM

---
### Introduction
Google Cloud Platform offers for a [Managed Service for Apache Kafka](https://cloud.google.com/managed-service-for-apache-kafka/docs/overview).

Kafbat UI comes with a built-in [GCP Auth library](https://github.com/googleapis/managedkafka) library for authentication.

It implements auth using an OAuth Callback Handler that takes advantage of the [Application Default Credentials (ADC)](https://cloud.google.com/docs/authentication/application-default-credentials) to authenticate to the Kafka cluster.

---
### ⚙️ Pre-requisites
1. A Managed Service for Apache Kafka instance
2. A Google IAM service account with the following permissions:
    - `roles/kafka.client`
    - `roles/kafka.producer`
    - `roles/kafka.consumer`
    - `roles/kafka.admin`
    - `roles/kafka.viewer`
3. The respective service account JSON file

---
### 🛠️ Configuration Examples
This section will cover some KafkaUI methods.

> 📝 **NOTE**
>>**This only works within the same VPC subnet as the kafka cluster as a limitation of the service.**

#### 🐳 Docker
You will need to mount the Service Account JSON file into the deployment.

You will then use the following variables in your Docker deployment:
```bash
   GOOGLE_APPLICATION_CREDENTIALS=/path/to/service-account-json/application_default_credentials.json
   KAFKA_CLUSTERS_0_NAME=gmsk-local
   KAFKA_CLUSTERS_0_BOOTSTRAPSERVERS=<KAFKA_URL_FROM_YOUR_MANAGED_CLUSTER>
   KAFKA_CLUSTERS_0_PROPERTIES_SECURITY_PROTOCOL=SASL_SSL
   KAFKA_CLUSTERS_0_PROPERTIES_SASL_MECHANISM=OAUTHBEARER
   KAFKA_CLUSTERS_0_PROPERTIES_SASL_CLIENT_CALLBACK_HANDLER_CLASS='com.google.cloud.hosted.kafka.auth.GcpLoginCallbackHandler'
   KAFKA_CLUSTERS_0_PROPERTIES_SASL_JAAS_CONFIG='org.apache.kafka.common.security.oauthbearer.OAuthBearerLoginModule required;'
```

- Your Kafka URL would look something like this: `bootstrap.CLUSTER_ID.us-central1.managedkafka.PROJECT_ID.cloud.goog:9092`
- Replace `/path/to/service-account-json/application_default_credentials.json` with the location of your mounted Service Account JSON file.


#### 🌱 Configuring by application.yaml
Set up the following configurations to connect to the cluster.

```yaml
kafka:
  clusters:
    - name: gmsk-local
      bootstrapServers: <KAFKA_URL_FROM_YOUR_MANAGED_CLUSTER>
      properties:
        security.protocol: SASL_SSL
        sasl.mechanism: OAUTHBEARER
        sasl.client.callback.handler.class: com.google.cloud.hosted.kafka.auth.GcpLoginCallbackHandler
        sasl.jaas.config: org.apache.kafka.common.security.oauthbearer.OAuthBearerLoginModule required;
```
