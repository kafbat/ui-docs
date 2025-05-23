---
description: Serialization, deserialization and custom plugins
---

# Serialization / SerDe

Kafka-ui supports multiple ways to serialize/deserialize data.

#### Int32, Int64, UInt32, UInt64

Big-endian 4/8 bytes representation of signed/unsigned integers.

#### Base64

Base64 (RFC4648) binary data representation. Can be useful in case if the actual data is not important, but exactly the same (byte-wise) key/value should be send.

#### Hex

[Hexadecimal](https://en.wikipedia.org/wiki/Hexadecimal) binary data representation. Bytes delimiter and case can be configured.

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

Sample configuration (if you want to overwrite default configuration):

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
