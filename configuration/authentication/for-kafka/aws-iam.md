---
description: How to configure AWS IAM Authentication
---

# AWS IAM

Kafbat UI comes with a built-in [aws-msk-iam-auth](https://github.com/aws/aws-msk-iam-auth) library.

You could pass SASL configs in the properties section for each cluster.

More details could be found here: [aws-msk-iam-auth](https://github.com/aws/aws-msk-iam-auth)

More about permissions: [msk-+serverless-setup.md](../../../quick-start/prerequisites/permissions/msk-+serverless-setup.md "mention")

## Authentication Options

* AWS profile-based authentication (`awsProfileName`)
* IAM Role-based authentication (`awsRoleArn` with optional session config)

### Examples:

Please replace

* \<KAFKA\_URL> with broker list
* \<PROFILE\_NAME> with your AWS profile
* \<ROLE\_ARN> with the AWS IAM Role ARN
* \<SESSION\_NAME> with a custom role session name (optional)
* \<STS\_REGION> with the AWS region for STS (optional)

#### Running From Docker Image

Using `awsProfileName`:

```bash
docker run -p 8080:8080 \
    -e KAFKA_CLUSTERS_0_NAME=local \
    -e KAFKA_CLUSTERS_0_BOOTSTRAPSERVERS=<KAFKA_URL> \
    -e KAFKA_CLUSTERS_0_PROPERTIES_SECURITY_PROTOCOL=SASL_SSL \
    -e KAFKA_CLUSTERS_0_PROPERTIES_SASL_MECHANISM=AWS_MSK_IAM \
    -e KAFKA_CLUSTERS_0_PROPERTIES_SASL_CLIENT_CALLBACK_HANDLER_CLASS=software.amazon.msk.auth.iam.IAMClientCallbackHandler \
    -e KAFKA_CLUSTERS_0_PROPERTIES_SASL_JAAS_CONFIG='software.amazon.msk.auth.iam.IAMLoginModule required awsProfileName="<PROFILE_NAME>";' \
    -d ghcr.io/kafbat/kafka-ui 
```

Using `awsRoleArn` and optional fields:

```bash
docker run -p 8080:8080 \
    -e KAFKA_CLUSTERS_0_NAME=local \
    -e KAFKA_CLUSTERS_0_BOOTSTRAPSERVERS=<KAFKA_URL> \
    -e KAFKA_CLUSTERS_0_PROPERTIES_SECURITY_PROTOCOL=SASL_SSL \
    -e KAFKA_CLUSTERS_0_PROPERTIES_SASL_MECHANISM=AWS_MSK_IAM \
    -e KAFKA_CLUSTERS_0_PROPERTIES_SASL_CLIENT_CALLBACK_HANDLER_CLASS=software.amazon.msk.auth.iam.IAMClientCallbackHandler \
    -e KAFKA_CLUSTERS_0_PROPERTIES_SASL_JAAS_CONFIG='software.amazon.msk.auth.iam.IAMLoginModule required awsRoleArn="<ROLE_ARN>" awsRoleSessionName="<SESSION_NAME>" awsStsRegion="<STS_REGION>";' \
    -d ghcr.io/kafbat/kafka-ui 
```

#### Configuring by application.yaml

Using `awsProfileName`:

```yaml
kafka:
  clusters:
    - name: local
      bootstrapServers: <KAFKA_URL>
      properties:
        security.protocol: SASL_SSL
        sasl.mechanism: AWS_MSK_IAM
        sasl.client.callback.handler.class: software.amazon.msk.auth.iam.IAMClientCallbackHandler
        sasl.jaas.config: software.amazon.msk.auth.iam.IAMLoginModule required awsProfileName="<PROFILE_NAME>";
```

Using `awsRoleArn` and optional fields:

```yaml
kafka:
  clusters:
    - name: local
      bootstrapServers: <KAFKA_URL>
      properties:
        security.protocol: SASL_SSL
        sasl.mechanism: AWS_MSK_IAM
        sasl.client.callback.handler.class: software.amazon.msk.auth.iam.IAMClientCallbackHandler
        sasl.jaas.config: software.amazon.msk.auth.iam.IAMLoginModule required awsRoleArn="<ROLE_ARN>" awsRoleSessionName="<SESSION_NAME>" awsStsRegion="<STS_REGION>";
```
