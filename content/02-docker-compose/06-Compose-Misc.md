---
category: Healthcheck and Restart Policies
---
# Healthcheck and Restart Policies

## `restart`
* `restart` property accepts the same values we learned before.
  * on-failure
  * unless-stopped
  * always
  * none
  
```yaml
version: '3'
services:
  myApp:
...
    restart: on-failure
...
```

## healthcheck
* We also have healthcheck property if we want to override the healthcheck of Image.

```yaml
version: '3'
services:
  myApp:
...
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:8080/test"]
      interval: 10s
      timeout: 5s
      retries: 3
      start_period: 20s
...
```

