---
description: Basic username+password authentication
---

# Basic Authentication

In order to enable basic username+passworda authentication add these properties:

```
      AUTH_TYPE: "LOGIN_FORM"
      SPRING_SECURITY_USER_NAME: admin
      SPRING_SECURITY_USER_PASSWORD: pass
```

YAML config counterpart:

```
auth.
  type: LOGIN_FORM

spring:
  security:
    user:
      name: admin
      password: pass
```

Please note that basic auth is not compatible with neither any other auth method nor RBAC.
