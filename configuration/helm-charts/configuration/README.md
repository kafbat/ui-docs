# Configuration

Most of the Helm charts parameters are common, follow table describes unique parameters related to application configuration.

#### Kafbat-UI parameters

| Parameter                                   | Description                                                                                                                                     | Default     |
| ------------------------------------------- |-------------------------------------------------------------------------------------------------------------------------------------------------| ----------- |
| `existingConfigMap`                         | Name of the existing ConfigMap with kafbat-ui environment variables                                                                             | `nil`       |
| `existingSecret`                            | Name of the existing Secret with Kafbat-UI environment variables                                                                                | `nil`       |
| `envs.secret`                               | Set of the sensitive environment variables to pass to Kafbat-UI                                                                                 | `{}`        |
| `envs.config`                               | Set of the environment variables to pass to Kafbat-UI                                                                                           | `{}`        |
| `yamlApplicationConfigConfigMap`            | Map with name and keyName keys, name refers to the existing ConfigMap, keyName refers to the ConfigMap key with Kafbat-UI config in Yaml format | `{}`        |
| `yamlApplicationConfig`                     | Kafbat-UI config in Yaml format                                                                                                                 | `{}`        |
| `networkPolicy.enabled`                     | Enable network policies                                                                                                                        	| `false`     |
| `networkPolicy.egressRules.customRules`     | Custom network egress policy rules                                                                                                             	| `[]`        |
| `networkPolicy.ingressRules.customRules`    | Custom network ingress policy rules                                                                                                            	| `[]`        |
| `podLabels`                             	  | Extra labels for Kafbat-UI pod                                                                                                                  | `{}`        |
| `ingress.enabled`                           | Enable Ingress to expose the Kafbat-UI service                                                                                          		| `false`     |
| `ingress.annotations`                       | Add annotations to the Ingress                                                                                                         			| `{}`        |
| `ingress.tls.enabled`                       | Enable Ingress route as a secured endpoint                                                                                                   	| `false`     |
| `ingress.tls.termination`                   | Set Ingress TLS termination                                                                                                            			| `edge`      |
| `ingress.tls.insecureEdgeTerminationPolicy` | Set Ingress Insecure Edge Termination Policy                                                                                           			| `Redirect`  |

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
