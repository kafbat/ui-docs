# Getting started

To run Kafbat UI, you can use either a pre-built Docker image or build it (or a jar file) yourself.

### Quick start (Demo run)

```
docker run -it -p 8080:8080 -e DYNAMIC_CONFIG_ENABLED=true ghcr.io/kafbat/kafka-ui
```

Then access the web UI at [http://localhost:8080](http://localhost:8080)

The command is sufficient to try things out. When you're done trying things out, you can proceed with a [persistent installation](getting-started.md#persistent-installation)

### Persistent installation

```
services:
  kafbat-ui:
    container_name: kafbat-ui
    image: ghcr.io/kafbat/kafka-ui
    ports:
      - 8080:8080
    environment:
      DYNAMIC_CONFIG_ENABLED: true
    volumes:
      - ~/kui/config.yml:/etc/kafkaui/dynamic_config.yaml
```

Please refer to our [configuration](broken-reference) page to proceed with further app configuration.

### Some useful configuration-related links

[Web UI Cluster Configuration Wizard](https://ui.docs.kafbat.io/configuration/configuration-wizard)

[Configuration file explanation](https://ui.docs.kafbat.io/configuration/configuration-file)

[Docker Compose examples](https://ui.docs.kafbat.io/configuration/compose-examples)

[Misc configuration properties](https://ui.docs.kafbat.io/configuration/misc-configuration-properties)

### Helm charts

[Quick start](https://ui.docs.kafbat.io/configuration/helm-charts/quick-start)

### Building from sources

[Quick start](https://ui.docs.kafbat.io/development/building/prerequisites) with building

### Liveliness and readiness probes

Liveliness and readiness endpoint is at `/actuator/health`.\
Info endpoint (build info) is located at `/actuator/info`.

## Configuration options

All of the environment variables/config properties could be found [here](https://ui.docs.kafbat.io/configuration/misc-configuration-properties).

## Contributing

Please refer to [contributing guide](https://ui.docs.kafbat.io/development/contributing), we'll guide you from there.
