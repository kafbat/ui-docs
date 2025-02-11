# Configuration

#### Kafbat UI parameters

Please find specific parameters description in helm chart [CONFIGURATION.md](https://github.com/kafbat/helm-charts/blob/main/charts/kafka-ui/CONFIGURATION.md)

### Setup

To install Kafbat-UI:

```bash
helm repo add kafka-ui https://kafbat.github.io/helm-charts
helm install kafka-ui kafka-ui/kafka-ui --set envs.config.KAFKA_CLUSTERS_0_NAME=local --set envs.config.KAFKA_CLUSTERS_0_BOOTSTRAPSERVERS=kafka:9092
```

To connect to Kafbat UI:

```bash
kubectl port-forward svc/kafbat-ui 8080:80
```

Open the `http://127.0.0.1:8080` in the browser to access Kafbat UI
