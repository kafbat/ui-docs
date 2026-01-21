---
description: SerDes included in a default distribution
---

# Built-In SerDes

#### Int32, Int64, UInt32, UInt64

Big-endian 4/8 bytes representation of signed/unsigned integers.

#### Base64

Base64 (RFC4648) binary data representation. It can be useful in cases where the actual data is not important, but the same (byte-wise) key/value should be sent.

#### Hex

[Hexadecimal](https://en.wikipedia.org/wiki/Hexadecimal) binary data representation. The byte delimiter and case can be configured.

Class name: `io.kafbat.ui.serdes.builtin.HexSerde`

```yaml
kafka:
  clusters:
    - name: Cluster1
      # Other Cluster configuration omitted ... 
      serde:
        - name: HexWithEditedDelimiter
          className: io.kafbat.ui.serdes.builtin.HexSerde
          properties:
            uppercase: "false"
            delimiter: ":"
```

#### String

Treats binary data as a string in the specified encoding. Default encoding is UTF-8.

Class name: `io.kafbat.ui.serdes.builtin.StringSerde`

Sample configuration (if you want to overwrite the default configuration):

```yaml
kafka:
  clusters:
    - name: Cluster1
      # Other Cluster configuration omitted ... 
      serde:
          # registering String serde with custom config
        - name: AsciiString
          className: io.kafbat.ui.serdes.builtin.StringSerde
          properties:
            encoding: "ASCII"
        
          # overriding build-it String serde config   
        - name: String 
          properties:
            encoding: "UTF-16"
```

#### ProtobufFile

Class name: `io.kafbat.ui.serdes.builtin.ProtobufFileSerde`

Sample configuration:

```yaml
kafka:
  clusters:
    - name: Cluster1
      # Other Cluster configuration omitted ... 
      serde:
        - name: ProtobufFile
          properties:
            # protobufFilesDir specifies root location for proto files (will be scanned recursively)
            # NOTE: if 'protobufFilesDir' specified, then 'protobufFile' and 'protobufFiles' settings will be ignored
            protobufFilesDir: "/path/to/my-protobufs"
            # (DEPRECATED) protobufFile is the path to the protobuf schema. (deprecated: please use "protobufFiles")
            protobufFile: path/to/my.proto
            # (DEPRECATED) protobufFiles is the location of one or more protobuf schemas
            protobufFiles:
              - /path/to/my-protobufs/my.proto
              - /path/to/my-protobufs/another.proto
            # protobufMessageName is the default protobuf type that is used to deserialize
            # the message's VALUE if the topic is not found in protobufMessageNameByTopic.    
            # optional, if not set, the first type in the file will be used as default
            protobufMessageName: my.DefaultValType
            # default protobuf type that is used for KEY serialization/deserialization
            # optional
            protobufMessageNameForKey: my.Type1
            # mapping of topic names to protobuf types, that will be used for KEYS  serialization/deserialization
            # optional
            protobufMessageNameForKeyByTopic:
              topic1: my.KeyType1
              topic2: my.KeyType2
            # mapping of topic names to protobuf types, that will be used for VALUES  serialization/deserialization
            # optional
            protobufMessageNameByTopic:
              topic1: my.Type1
              "topic.2": my.Type2
```

#### ProtobufRawDecoder

Deserialize-only serde. Decodes protobuf payload without a predefined schema (like `protoc --decode_raw` command).

#### SchemaRegistry

SchemaRegistry serde is automatically configured if schema registry properties are set on the cluster level. But you can add new SchemaRegistry-typed serdes that will connect to another schema-registry instance.

Class name: `io.kafbat.ui.serdes.builtin.sr.SchemaRegistrySerde`

Sample configuration:

```yaml
kafka:
  clusters:
    - name: Cluster1
      # this url will be used by "SchemaRegistry" by default
      schemaRegistry: http://main-schema-registry:8081
      serde:
        - name: AnotherSchemaRegistry
          className: io.kafbat.ui.serdes.builtin.sr.SchemaRegistrySerde
          properties:
            url:  http://another-schema-registry:8081
            # auth properties, optional
            username: nameForAuth
            password: P@ssW0RdForAuth

          # and also add another SchemaRegistry serde
        - name: ThirdSchemaRegistry
          className: io.kafbat.ui.serdes.builtin.sr.SchemaRegistrySerde
          properties:
            url:  http://another-yet-schema-registry:8081
```

##### Avro Display Options

The SchemaRegistry serde supports additional options for controlling how Avro messages are displayed in the UI:

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| `showNullValues` | boolean | `false` | When `true`, fields with null values are displayed as `"field": null`. When `false` (default), null fields are omitted from the output. |
| `useFullyQualifiedNames` | boolean | `false` | When `true`, union types always use fully qualified names (e.g., `{"io.kafbat.test.NestedRecord": {...}}`). When `false` (default), short names are used unless there's a name collision within the union. |

These options can be configured in two ways:

**Option 1: Cluster-level configuration (auto-config)**

When using the auto-configured SchemaRegistry serde, set these properties at the cluster level:

```yaml
kafka:
  clusters:
    - name: Cluster1
      schemaRegistry: http://schema-registry:8081
      schemaRegistryShowNullValues: true
      schemaRegistryUseFullyQualifiedNames: true
```

Or as environment variables:
```
KAFKA_CLUSTERS_0_SCHEMAREGISTRYSHOWNULLVALUES=true
KAFKA_CLUSTERS_0_SCHEMAREGISTRYUSEFULLYQUALIFIEDNAMES=true
```

**Option 2: Explicit serde configuration**

When configuring the SchemaRegistry serde explicitly, set these in the serde properties:

```yaml
kafka:
  clusters:
    - name: Cluster1
      schemaRegistry: http://schema-registry:8081
      serde:
        - name: SchemaRegistry
          properties:
            showNullValues: true
            useFullyQualifiedNames: true
```

Or as environment variables:
```
KAFKA_CLUSTERS_0_SERDE_0_NAME=SchemaRegistry
KAFKA_CLUSTERS_0_SERDE_0_PROPERTIES_SHOWNULLVALUES=true
KAFKA_CLUSTERS_0_SERDE_0_PROPERTIES_USEFULLYQUALIFIEDNAMES=true
```

**Example output**

Given an Avro schema with nullable fields:
```json
{
  "type": "record",
  "name": "TestRecord",
  "namespace": "io.kafbat.test",
  "fields": [
    {"name": "id", "type": "string"},
    {"name": "optionalField", "type": ["null", "string"]},
    {"name": "nested", "type": ["null", {"type": "record", "name": "NestedRecord", "fields": [...]}]}
  ]
}
```

With default settings (`showNullValues: false`, `useFullyQualifiedNames: false`):
```json
{
  "id": "123",
  "nested": {"NestedRecord": {"value": "test"}}
}
```

With `showNullValues: true` and `useFullyQualifiedNames: true`:
```json
{
  "id": "123",
  "optionalField": null,
  "nested": {"io.kafbat.test.NestedRecord": {"value": "test"}}
}
```
