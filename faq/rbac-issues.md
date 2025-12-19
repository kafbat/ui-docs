# RBAC Issues

## RBAC is enabled, I see no clusters in UI

1. Make sure you see the clusters with rbac disabled first
2. check `/api/authorization` output to verify that you have required permissions. If not, proceed next.
3. Make sure that you get the needed roles assigned.

Add the following into the yaml config:

```
logging:
  level:
    io.kafbat.ui.service.rbac.extractor: TRACE
```

Restart the app, upon authenticating your user, grep the logs by `extractor`. The logs are self-descriptive.



```
```
