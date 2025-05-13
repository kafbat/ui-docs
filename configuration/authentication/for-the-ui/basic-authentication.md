---
description: Basic username+password authentication
---

# Basic Authentication

Basic authentication supports only one local user. If you need multiple, running a Keycloak instance is the easiest way.

To enable basic username+password authentication, add these properties:

```properties
      AUTH_TYPE: "LOGIN_FORM"
      SPRING_SECURITY_USER_NAME: admin
      SPRING_SECURITY_USER_PASSWORD: pass
```

YAML config counterpart:

```yaml
auth:
  type: LOGIN_FORM

spring:
  security:
    user:
      name: admin
      password: pass
```

Please note that basic auth is incompatible with any other auth method or RBAC.
