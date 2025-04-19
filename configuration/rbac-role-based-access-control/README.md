# RBAC (Role based access control)

## Role-based access control

In this article, we'll guide how to set up Kafbat-UI with role-based access control.

### Authentication methods

First of all, you'd need to set up authentication method(s). Refer to [this](../authentication/) article for setup.

### Config placement

First of all, you have to decide if either:

1. You wish to store all roles in a separate config file
2. Or within a main config file

This is how you include one more file to start with a docker-compose example:

```yaml
services:
  kafbat-ui:
    container_name: kafbat-ui
    image: ghcr.io/kafbat/kafka-ui
    environment:
      KAFKA_CLUSTERS_0_NAME: local
      # other properties, omitted
      SPRING_CONFIG_ADDITIONAL-LOCATION: /roles.yml
    volumes:
      - /tmp/roles.yml:/roles.yml
```

Alternatively, you can append the roles file contents to your main config file.

### Roles file structure

#### Clusters

In the roles file we define roles, duh. Every role has access to defined clusters:

```yaml
rbac:
  roles:
    - name: "memelords"
      clusters:
        - local
        - dev
        - staging
        - prod
```

#### Subjects

A role also has a list of _subjects_ which are the entities we will use to assign roles to. They are provider-dependant, in general, they can be users, groups, or some other entities (github orgs, google domains, LDAP queries, etc.) In this example we define a role `memelords` that will contain all the users within the Google domain `memelord.lol` and, additionally, a GitHub user `Haarolean`. You can combine as many subjects as you want within a role.

```yaml
    - name: "memelords"
      subjects:
        - provider: oauth_google
          type: domain
          value: "memelord.lol"
        - provider: oauth_github
          type: user
          value: "Haarolean"
```

The subject value is either a fixed string or a regular expression identifying a subject. To use regular expression, you must set `isRegex: true`.
Regular expression works for any combination of `provider` and `type`.
In this example, we define a role `admins` that will contain all the users with an oauth role ending with `-ADMIN`.

```yaml
    - name: "admins"
      subjects:
        - provider: oauth
          type: role
          value: ".*-ADMIN"
          isRegex: true
```

#### Providers

A list of supported providers and corresponding subject fetch mechanism:

* oauth: `user`, `role`
* oauth\_google: `user`, `domain`
* oauth\_github: `user`, `organization`, `team`
* oauth\_cognito: `user`, `group`
* ldap: `user`, `group`
* ldap\_ad: `user`, `group`

More on identity providers in: [supported-identity-providers.md](supported-identity-providers.md "mention")

Find the more detailed examples in a full example file lower.

#### Permissions

The next thing which is present in your roles file is, surprisingly, permissions. They consist of:

1. Resource Can be one of the: `CLUSTERCONFIG`, `TOPIC`, `CONSUMER`, `SCHEMA`, `CONNECT`, `KSQL`, `ACL`.
2. The resource value is either a fixed string or a regular expression identifying a resource. Value is not applicable to `clusterconfig` and `ksql` resources. Please do not fill it out.
3. Actions It's a list of actions (the possible values depend on the resource, see the lists below) that will be applied to the certain permission. Also, note, there's a special action for any of the resources called "all", it will virtually grant all the actions within the corresponding resource. An example for enabling viewing and creating topics whose name start with "derp":

```yaml
      permissions:
        - resource: topic
          value: "derp.*"
          actions: [ VIEW, CREATE ]
```

**Actions**

A list of all the actions for the corresponding resources (please note neither resource nor action names are case-sensitive):

* `applicationconfig`: `view`, `edit`
* `clusterconfig`: `view`, `edit`
* `topic`: `view`, `create`, `edit`, `delete`, `messages_read`, `messages_produce`, `messages_delete`, `analysis_run`, `analysis_view`
* `consumer`: `view`, `delete`, `reset_offsets`
* `schema`: `view`, `create`, `delete`, `edit`, `modify_global_compatibility`
* `connect`: `view`, `edit`, `create`, `delete`,`restart`, `reset_offsets`
* `ksql`: `execute`
* `acl`: `view`, `edit`
* `audit`: `view`
* ~~`client_quotas`: `view`, `edit`~~ — not yet implemented

## Example file

**A complete file example:**

```yaml
rbac:
  roles:
    - name: "memelords"
      clusters:
        - local
        - dev
        - staging
        - prod
      subjects:
        - provider: oauth_google
          type: domain
          value: "memelord.lol"
        - provider: oauth_google
          type: user
          value: "kek@memelord.lol"

        - provider: oauth_github
          type: organization
          value: "memelords_team"
        - provider: oauth_github
          type: user
          value: "memelord"

        - provider: oauth_cognito
          type: user
          value: "username"
        - provider: oauth_cognito
          type: group
          value: "memelords"

        - provider: ldap
          type: user
          value: "username"
        - provider: ldap
          type: group
          value: "admin_staff"

        - provider: ldap_ad
          type: user
          value: "username"
        - provider: ldap_ad
          type: group
          value: "admin_staff"

      permissions:
        - resource: applicationconfig
          # value not applicable for applicationconfig
          actions: [ "view", "edit" ] # can be with or without quotes

        - resource: clusterconfig
          # value not applicable for clusterconfig
          actions: [ "view", "edit" ]

        - resource: topic
          value: "ololo.*"
          actions: # can be a multiline list
            - VIEW # can be upper or lowercase
            - CREATE
            - EDIT
            - DELETE
            - MESSAGES_READ
            - MESSAGES_PRODUCE
            - MESSAGES_DELETE
            - ANALYSIS_RUN
            - ANALYSIS_VIEW

        - resource: consumer
          value: "\_confluent-ksql.*"
          actions: [ VIEW, DELETE, RESET_OFFSETS ]

        - resource: schema
          value: "blah.*"
          actions: [ VIEW, CREATE, DELETE, EDIT, MODIFY_GLOBAL_COMPATIBILITY ]

        - resource: connect
          value: "local"
          actions: [ view, edit, create, delete, restart, reset_offsets ]
        # connectors selector not implemented yet, use connects
        #      selector:
        #        connector:
        #          name: ".*"
        #          class: 'io.kafbat.connectorName'

        - resource: ksql
          # value not applicable for ksql
          actions: [ execute ]

        - resource: acl
          # value not applicable for acl
          actions: [ view, edit ]

```

**A read-only setup:**

```yaml
rbac:
  roles:
    - name: "readonly"
      clusters:
        # FILL THIS
      subjects:
        # FILL THIS
      permissions:
        - resource: clusterconfig
          actions: [ "view" ]

        - resource: topic
          value: ".*"
          actions:
            - VIEW
            - MESSAGES_READ
            - ANALYSIS_VIEW

        - resource: consumer
          value: ".*"
          actions: [ view ]

        - resource: schema
          value: ".*"
          actions: [ view ]

        - resource: connect
          value: ".*"
          actions: [ view ]

        - resource: acl
          actions: [ view ]

```

**An admin-group setup example:**

```yaml
rbac:
  roles:
    - name: "admins"
      clusters:
        # FILL THIS
      subjects:
        # FILL THIS
      permissions:
        - resource: applicationconfig
          actions: all

        - resource: clusterconfig
          actions: all

        - resource: topic
          value: ".*"
          actions: all

        - resource: consumer
          value: ".*"
          actions: all

        - resource: schema
          value: ".*"
          actions: all

        - resource: connect
          value: ".*"
          actions: all

        - resource: ksql
          actions: all

        - resource: acl
          actions: [ view ]

```
