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

### Fallback

If selected serde couldn't be applied (exception was thrown), then fallback (String serde with UTF-8 encoding) serde will be applied. Such messages will be specially highlighted in UI.
