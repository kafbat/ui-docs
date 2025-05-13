# Getting started

To run Kafbat UI, you can use either a pre-built Docker image or build the app yourself.

### Quick start (Demo run)

```bash
docker run -it -p 8080:8080 -e DYNAMIC_CONFIG_ENABLED=true ghcr.io/kafbat/kafka-ui
```

Then, access the web UI at [http://localhost:8080](http://localhost:8080)

The command is sufficient to try things out. When you're done trying these things out, you can proceed with a [persistent installation](getting-started.md#persistent-installation)

### Image distribution & Available Container Registries

#### Image types

There are two types of images we publish:

* **Release** images, tagged with `latest` and simultaneously semver tags like `v1.0.0`&#x20;
* **Snapshot** images for every commit pushed to the main branch, named as the respective commit's short SHA, like `b241abe`. The latest build of the main branch is always tagged as `main` as well

Sometimes, for people who require recent changes that are not yet released, we suggest pulling snapshot images. These are usually safe to use, breaking changes happen rarely. To prevent pulling new versions, you can pin the version using the sha-named tag rather than `main` .

#### Image distribution channels

We publish our container images to these registries:

* GHCR (Github Container Registry) — `ghcr.io/kafbat/kafka-ui:latest`
* AWS ECR (Elastic Container Registry) — `public.ecr.aws/kafbat/kafka-ui:latest`
* Docker Hub — `kafbat/kafka-ui:latest`

GHCR is considered the main registry for various reasons, the rest are treated as mirrors. You can use these registries to avoid being ratioed by these services (GHCR requires authentication to pull images, Docker Hub is known to have pull limits).

GHCR contains all our images (release and snapshots), while the others store only release versions.

In the future, we will consider distributing snapshots to all the registries as a separate package.

### Persistent installation

```yaml
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

Please refer to our [configuration](https://ui.docs.kafbat.io/configuration/configuration-file) page to proceed with further app configuration.

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
