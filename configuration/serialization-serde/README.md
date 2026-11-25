---
description: Serialization, deserialization and custom plugins
---

# Serialization / SerDe

Kafka-ui supports multiple ways to serialize/deserialize data.

[built-in-serdes.md](built-in-serdes.md "mention")

[pluggable-serdes](pluggable-serdes/ "mention")

### Setting serdes for specific topics

You can specify a preferred serde for the topics key/value. This serde will be chosen by default in UI on topic's view/produce pages. To do so, set `topicValuesPattern/topicValuesPattern` properties for the selected serde. Kafka-ui will choose the first serde that matches the specified pattern.

Sample configuration:

```yaml
kafka:
  clusters:
    - name: Cluster1
      serde:
        - name: String
          topicKeysPattern: click-events|imp-events
        
        - name: Int64
          topicKeysPattern: ".*-events"
        
        - name: SchemaRegistry
          topicValuesPattern: click-events|imp-events
```

### Default serdes

You can specify which serde will be chosen in UI by default if no other serdes are selected via `topicKeysPattern/topicValuesPattern` settings.

Sample configuration:

```yaml
kafka:
  clusters:
    - name: Cluster1
      defaultKeySerde: Int32
      defaultValueSerde: String
      serde:
        - name: Int32
          topicKeysPattern: click-events|imp-events
```

### Restricting serdes for producing

By default, any serde can produce messages to any topic. On a topic that holds Avro values, a message produced with the String serde reaches consumers as raw bytes, and Schema Registry consumers fail on it with `Schema not found`. To stop that, list in `produceSerdes` the serdes allowed to produce to a topic. Consuming is unaffected.

`produceSerdes` is an ordered list of rules:

* `topicPattern`: a regex that must match the whole topic name. The first matching rule applies; topics matching no rule are unrestricted.
* `keySerdes` / `valueSerdes`: names of the serdes allowed for the message key / value. If a list is empty or not set, then any serde can produce that side.

The produce form leaves out disallowed serdes, and the API rejects a message produced with one with HTTP 400. A null key or a null value (tombstone) is never serialized, so it can be produced whatever serde is selected.

If a rule names an unknown serde or holds an invalid regex, then kafka-ui fails at startup.

Sample configuration, allowing only Schema Registry to produce to `domain.*` topics, and only String for keys on `logs.*` topics:

```yaml
kafka:
  clusters:
    - name: Cluster1
      produceSerdes:
        - topicPattern: "domain\\..*"
          keySerdes: [ SchemaRegistry ]
          valueSerdes: [ SchemaRegistry ]

        - topicPattern: "logs\\..*"
          keySerdes: [ String ]
```

Same configuration in env-vars fashion:

```properties
KAFKA_CLUSTERS_0_PRODUCESERDES_0_TOPICPATTERN: "domain\\..*"
KAFKA_CLUSTERS_0_PRODUCESERDES_0_KEYSERDES_0: SchemaRegistry
KAFKA_CLUSTERS_0_PRODUCESERDES_0_VALUESERDES_0: SchemaRegistry

KAFKA_CLUSTERS_0_PRODUCESERDES_1_TOPICPATTERN: "logs\\..*"
KAFKA_CLUSTERS_0_PRODUCESERDES_1_KEYSERDES_0: String
```

### Fallback

If selected serde couldn't be applied (exception was thrown), then fallback (String serde with UTF-8 encoding) serde will be applied. Such messages will be specially highlighted in UI.
