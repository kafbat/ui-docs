---
description: Quick Start with Helm Chart
---

# Quick start

## General

1. Clone/Copy Chart to your working directory
2.  Execute command

```bash
    helm repo add kafbat-ui https://kafbat.github.io/helm-charts
    helm install kafbat-ui kafbat-ui/kafka-ui
```

#### Passing Kafbat-UI configuration as Dict

Create values.yml file

```yaml
yamlApplicationConfig:
  kafka:
    clusters:
      - name: yaml
        bootstrapServers:  kafka-cluster-broker-endpoints:9092
  auth:
    type: disabled
  management:
    health:
      ldap:
        enabled: false
```

Install by executing command

```bash
helm install kafbat-ui kafbat-ui/kafka-ui -f values.yml
```

#### Passing configuration file as ConfigMap

Create config map

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kafbat-ui-configmap
data:
  config.yml: |-
    kafka:
      clusters:
        - name: yaml
          bootstrapServers: kafka-cluster-broker-endpoints:9092
    auth:
      type: disabled
    management:
      health:
        ldap:
          enabled: false
```

This ConfigMap will be mounted to the Pod

Install by executing the command

```bash
helm install kafbat-ui kafbat-ui/kafka-ui --set yamlApplicationConfigConfigMap.name="kafbat-ui-configmap",yamlApplicationConfigConfigMap.keyName="config.yml"
```

#### Passing environment variables as ConfigMap

Create config map

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kafbat-ui-helm-values
data:
  KAFKA_CLUSTERS_0_NAME: "kafka-cluster-name"
  KAFKA_CLUSTERS_0_BOOTSTRAPSERVERS: "kafka-cluster-broker-endpoints:9092"
  AUTH_TYPE: "DISABLED"
  MANAGEMENT_HEALTH_LDAP_ENABLED: "FALSE" 
```
Install by executing the command

```bash
helm install kafbat-ui kafbat-ui/kafka-ui --set existingConfigMap="kafbat-ui-helm-values"
```
