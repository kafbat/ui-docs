# Audit log

Kafbat-UI allows you to log all operations to your kafka clusters done within kafbat-ui itself.

Logging can be done to either kafka topic and/or console.

See all the available configuration properties:

```yaml
kafka:
  clusters:
    - name: local
      audit:
        topic-audit-enabled: true
        console-audit-enabled: true
        topic: '__kui-audit-log' # default name
        audit-topic-properties: # any kafka topic properties in format of a map
          retention.ms: 43200000
        audit-topics-partitions: 1 # how many partitions, default is 1
        level: all # either ALL or ALTER_ONLY (default). ALL will log all read operations.
```

### Note

If you create the audit topic manually, it **should not** be compacted, as the audit documents do not have a key, which is required for Kafka topic compaction.

Other than **compaction**, the Kafka UI does not expect specific requirements for additional topic properties. However, as always, consider configuring the following:

* **Retention period** How long the data should be stored in Kafka.
* **Number of partitions** This affects parallelism and how the data will be consumed.
