# Configuration

Most of the Helm charts parameters are common, follow table describes unique parameters related to application configuration.

#### Kafbat-UI parameters

Please find specific paramater description in helm chart *[README.md](https://github.com/kafbat/helm-charts/blob/main/charts/kafka-ui/README.md)*


### Example

To install Kafbat-UI need to execute follow:

```bash
helm repo add kafka-ui https://kafbat.github.io/helm-charts
helm install kafka-ui kafka-ui/kafka-ui --set envs.config.KAFKA_CLUSTERS_0_NAME=local --set envs.config.KAFKA_CLUSTERS_0_BOOTSTRAPSERVERS=kafka:9092
```

To connect to Kafbat-UI web application need to execute:

```bash
kubectl port-forward svc/kafbat-ui 8080:80
```

Open the `http://127.0.0.1:8080` on the browser to access Kafbat-UI.
