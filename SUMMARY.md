# Table of contents

## 🎓 Overview

* [About](README.md)
* [Features](overview/features.md)
* [Getting started](overview/getting-started.md)

## 🛣️ Project

* [Code of Conduct](project/code-of-conduct.md)
* [Roadmap](project/roadmap.md)

## 🧱 Development

* [🤝🏻 Contributing](development/contributing.md)
* [Setting up git](development/setting-up-git.md)
* [Building](development/building/README.md)
  * [Prerequisites](development/building/prerequisites.md)
  * [With Docker](development/building/with-docker.md)
  * [Without Docker](development/building/without-docker.md)
* [WIP: Testing](development/wip-testing.md)

## ⚡ Quick Start

* [🔍 Prerequisites](quick-start/prerequisites/README.md)
  * [Kafka Permissions](quick-start/prerequisites/permissions/README.md)
    * [Standalone Kafka ACLs](quick-start/prerequisites/permissions/required-acls.md)
    * [MSK (+Serverless) Setup](quick-start/prerequisites/permissions/msk-+serverless-setup.md)
* [Demo run](quick-start/demo-run.md)
* [AWS Marketplace](quick-start/via-aws-marketplace.md)
* [Persisting config](quick-start/persistent-start.md)
* [K8s / Helm](quick-start/k8s-helm.md)

## 🛠️ Configuration

* [Configuration wizard](configuration/configuration-wizard.md)
* [Configuration file](configuration/configuration-file.md)
* [Setup example configs](configuration/compose-examples.md)
* [Helm charts](configuration/helm-charts/README.md)
  * [Quick start](configuration/helm-charts/quick-start.md)
  * [Configuration](configuration/helm-charts/configuration/README.md)
    * [SSL example](configuration/helm-charts/configuration/ssl-example.md)
  * [Resource limits](configuration/helm-charts/resource-limits.md)
  * [Sticky sessions](configuration/helm-charts/sticky-sessions.md)
* [Misc configuration properties](configuration/misc-configuration-properties.md)
* [Complex configuration examples](configuration/configuration/complex-configuration-examples/README.md)
  * [Kraft mode + multiple brokers](configuration/configuration/complex-configuration-examples/kraft-mode-+-multiple-brokers.md)
* [Kafka secured with SSL](configuration/ssl.md)
* [Authentication](configuration/authentication/README.md)
  * [For the UI](configuration/authentication/for-the-ui/README.md)
    * [Basic Authentication](configuration/authentication/for-the-ui/basic-authentication.md)
    * [OAuth2](configuration/authentication/for-the-ui/oauth2.md)
    * [LDAP / Active Directory](configuration/authentication/for-the-ui/ldap-active-directory.md)
    * [SSO Guide (Deprecated)](configuration/authentication/for-the-ui/sso-guide.md)
  * [For Kafka](configuration/authentication/for-kafka/README.md)
    * [AWS IAM](configuration/authentication/for-kafka/aws-iam.md)
    * [SASL\_SCRAM](configuration/authentication/for-kafka/sasl_scram.md)
* [RBAC (Role based access control)](configuration/rbac-role-based-access-control/README.md)
  * [Supported Identity Providers](configuration/rbac-role-based-access-control/supported-identity-providers.md)
* [Data masking](configuration/data-masking.md)
* [Audit log](configuration/audit-log.md)
* [Serialization / SerDe](configuration/serialization-serde/README.md)
  * [Built-In SerDes](configuration/serialization-serde/built-in-serdes.md)
  * [Pluggable SerDes](configuration/serialization-serde/pluggable-serdes/README.md)
    * [Building your own pluggable SerDe](configuration/serialization-serde/pluggable-serdes/building-your-own-pluggable-serde.md)
* [OpenDataDiscovery Integration](configuration/opendatadiscovery-integration.md)

## ❓ FAQ

* [Common problems](faq/common-problems.md)
* [MCP Server](faq/mcp.md)
* [FAQ](faq/faq.md)
* [RBAC Issues](faq/rbac-issues.md)
* [Authentication Issues](faq/authentication.md)
