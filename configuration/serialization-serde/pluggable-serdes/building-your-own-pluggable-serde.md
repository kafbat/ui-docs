# Building your own pluggable SerDe

### Custom pluggable serde registration

You can implement your serde and register it in the UI. To do so:

1. Add `serde-api` dependency, [link to maven central](https://central.sonatype.com/artifact/io.kafbat.ui/serde-api)
2. Implement `io.kafbat.ui.serde.api.Serde` interface. See javadoc for implementation requirements.
3. Pack your serde into an uber jar, or provide a directory with no-dependency jar and its dependencies jars

Example pluggable serdes : [kafka-smile-serde](https://github.com/kafbat/ui-serde-smile), [kafka-glue-sr-serde](https://github.com/kafbat/ui-serde-glue)

Sample configuration:

```yaml
kafka:
  clusters:
    - name: Cluster1
      serde:
        - name: MyCustomSerde
          className: my.lovely.org.KafkaUiSerde
          filePath: /var/lib/kui-serde/my-kui-serde.jar
          
        - name: MyCustomSerde2
          className: my.lovely.org.KafkaUiSerde2
          filePath: /var/lib/kui-serde2
          properties:
            prop1: v1
```
