# Data masking

## Topics data masking

You can configure kafbat-ui to mask sensitive data shown in Messages page.

Several masking policies supported:

#### REMOVE

For json objects - remove target fields, otherwise - return "null" string.

```yaml
- type: REMOVE
  fields: [ "id", "name" ]
  ...
```

Apply examples:

```
{ "id": 1234, "name": { "first": "James" }, "age": 30 } 
 ->
{ "age": 30 } 
```

```
non-json string -> null
```

#### REPLACE

For json objects - replace target field's values with specified replacement string (by default with `***DATA_MASKED***`). Note: if target field's value is object, then replacement applied to all its fields recursively (see example).

```yaml
- type: REPLACE
  fields: [ "id", "name" ]
  replacement: "***"  #optional, "***DATA_MASKED***" by default
  ...
```

Apply examples:

```
{ "id": 1234, "name": { "first": "James", "last": "Bond" }, "age": 30 } 
 ->
{ "id": "***", "name": { "first": "***", "last": "***" }, "age": 30 } 
```

```
non-json string -> ***
```

#### MASK

Mask target field's values with specified masking characters, recursively (spaces and line separators will be kept as-is). `maskingCharsReplacement` array specifies what symbols will be used to replace upper-case chars (index 0), lower-case chars (index 1), digits (index 2) and other symbols (index 3) correspondingly.

```yaml
- type: MASK
  fields: [ "id", "name" ]
  maskingCharsReplacement: ["A", "a", "N", "_"]   # optional, default is ["X", "x", "n", "-"]
  ...
```

Apply examples:

```
{ "id": 1234, "name": { "first": "James", "last": "Bond!" }, "age": 30 } 
 ->
{ "id": "NNNN", "name": { "first": "Aaaaa", "last": "Aaaa_" }, "age": 30 } 
```

```
Some string! -> Aaaa aaaaaa_
```

***

For each policy, if `fields` not specified, then policy will be applied to all object's fields or whole string if it is not a json-object.

You can specify which masks will be applied to topic's keys/values. Multiple policies will be applied if topic matches both policy's patterns.

Yaml configuration example:

```yaml
kafka:
  clusters:
    - name: ClusterName
      # Other Cluster configuration omitted ... 
      masking:
        - type: REMOVE
          fields: [ "id" ]
          topicKeysPattern: "events-with-ids-.*"
          topicValuesPattern: "events-with-ids-.*"
          
        - type: REPLACE
          fields: [ "companyName", "organizationName" ]
          replacement: "***MASKED_ORG_NAME***"   #optional
          topicValuesPattern: "org-events-.*"
        
        - type: MASK
          fields: [ "name", "surname" ]
          maskingCharsReplacement: ["A", "a", "N", "_"]  #optional
          topicValuesPattern: "user-states"

        - type: MASK
          topicValuesPattern: "very-secured-topic"
```

Same configuration in env-vars fashion:

```properties
...
KAFKA_CLUSTERS_0_MASKING_0_TYPE: REMOVE
KAFKA_CLUSTERS_0_MASKING_0_FIELDS_0: "id"
KAFKA_CLUSTERS_0_MASKING_0_TOPICKEYSPATTERN: "events-with-ids-.*"
KAFKA_CLUSTERS_0_MASKING_0_TOPICVALUESPATTERN: "events-with-ids-.*"

KAFKA_CLUSTERS_0_MASKING_1_TYPE: REPLACE
KAFKA_CLUSTERS_0_MASKING_1_FIELDS_0: "companyName"
KAFKA_CLUSTERS_0_MASKING_1_FIELDS_1: "organizationName"
KAFKA_CLUSTERS_0_MASKING_1_REPLACEMENT: "***MASKED_ORG_NAME***"
KAFKA_CLUSTERS_0_MASKING_1_TOPICVALUESPATTERN: "org-events-.*"

KAFKA_CLUSTERS_0_MASKING_2_TYPE: MASK
KAFKA_CLUSTERS_0_MASKING_2_FIELDS_0: "name"
KAFKA_CLUSTERS_0_MASKING_2_FIELDS_1: "surname"
KAFKA_CLUSTERS_0_MASKING_2_MASKING_CHARS_REPLACEMENT_0: 'A'
KAFKA_CLUSTERS_0_MASKING_2_MASKING_CHARS_REPLACEMENT_1: 'a'
KAFKA_CLUSTERS_0_MASKING_2_MASKING_CHARS_REPLACEMENT_2: 'N'
KAFKA_CLUSTERS_0_MASKING_2_MASKING_CHARS_REPLACEMENT_3: '_'
KAFKA_CLUSTERS_0_MASKING_2_TOPICVALUESPATTERN: "user-states"

KAFKA_CLUSTERS_0_MASKING_3_TYPE: MASK
KAFKA_CLUSTERS_0_MASKING_3_TOPICVALUESPATTERN: "very-secured-topic"
```
