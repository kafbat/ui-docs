# Persisting config

Please ensure the target volume (`~/kui/config.yml`) of your config file does exist. If it doesn't, docker will create it as a directory instead of a file, and everything will explode.

Create a YAML file with the following contents:

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

Run the compose via:

```bash
docker-compose -f <your-file>.yml up -d
```
