---
description: How to configure Azure Entra Authentication
---

# Azure Entra

## Overview
Kafbat UI comes with an OAuth Callback Handler for 
[Azure Event Hubs for Kafka](https://learn.microsoft.com/en-us/azure/developer/java/spring-framework/migrate-kafka-to-passwordless-connection?tabs=azure-portal%2Csign-in-azure-cli%2Cjava-kafka%2Capp-service%2Cassign-role-service-connector).

The Callback Handler uses the 
[Default Azure Credential](https://learn.microsoft.com/en-us/azure/developer/java/sdk/identity-azure-hosted-auth#default-azure-credential) for authentication, 
providing support for all commonly used Entra credentials.

Pre-requisites:
1. An Event Hub Namespace in the standard, premium or dedicated tiers.
2. An Event Hub.
3. Entra Credentials for an Entra Identity with a role assignment allowing 
   sending to and receiving from Event Hub, such as Azure Event Hubs Data Owner.

## Examples

Please replace `<EVENT HUB NAMESPACE NAME>` with the name of your Event Hub namespace.

Prior to starting Kafbat UI, authenticate with Azure Entra using any of the supported 
Azure Credential types (Environment Variable, Managed Identity, Azure CLI, etc.). 
See [Default Azure Credential](https://learn.microsoft.com/en-us/azure/developer/java/sdk/identity-azure-hosted-auth#default-azure-credential) 
for additional configuration required based on the credential type.

As an example, after authenticating with the Azure CLI using `az login` Kafbat UI will 
automatically connect to Event Hubs using CLI credentials.

### Docker

```bash
docker run -p 8080:8080 \
    -e KAFKA_CLUSTERS_0_NAME=local \
    -e KAFKA_CLUSTERS_0_BOOTSTRAPSERVERS=<EVENT HUB NAMESPACE NAME>.servicebus.windows.net:9093 \
    -e KAFKA_CLUSTERS_0_PROPERTIES_SECURITY_PROTOCOL=SASL_SSL \
    -e KAFKA_CLUSTERS_0_PROPERTIES_SASL_MECHANISM=OAUTHBEARER \
    -e KAFKA_CLUSTERS_0_PROPERTIES_SASL_CLIENT_CALLBACK_HANDLER_CLASS=io.kafbat.ui.config.auth.azure.AzureEntraLoginCallbackHandler \
    -e KAFKA_CLUSTERS_0_PROPERTIES_SASL_JAAS_CONFIG="org.apache.kafka.common.security.oauthbearer.OAuthBearerLoginModule required;" \
    -d ghcr.io/kafbat/kafka-ui
```

### application.yaml
```yaml
kafka:
  clusters:
    - bootstrapServers:<EVENT HUB NAMESPACE NAME>.servicebus.windows.net:9093
      name: local
      properties:
        security.protocol: SASL_SSL
        sasl.mechanism: OAUTHBEARER
        sasl.jaas.config:
          org.apache.kafka.common.security.oauthbearer.OAuthBearerLoginModule required;
        sasl.client.callback.handler.class: io.kafbat.ui.config.auth.azure.AzureEntraLoginCallbackHandler
      readOnly: false
```