---
category: Networking
---
# Docker Compose Networking

* Networks are also easy concept, you won't learn anything new here.
* But at the end you will be creating a app that talks with database to store its values.
* And most of all database values will be persisted for multiple runs.

## Setting Up Network
* If you were paying attention to logs when we run our services, you saw that `docker-compose` creates a `default` network for us.
* So all **services** defined on same file are already in same (default ) network.

```yaml
version: '3'
services:
  myApp:
    image: amantuladhar/docker-kubernetes:v1-web
    ports:
      - 9090:8080
  myTester:                               # 1
    image: amantuladhar/network-tools     # 2
    command: sleep 50000                  # 3
  myDb:
    image: mysql:5.7
...
...
``` 

* `#1` Define one more service `myTester`
* `#2` Use the image `amantuladhar/network-tools`. Remember when we learned networks, we used this to test if two containers were in fact were able to talk to each other.
* `#3` Overriding default `command` for an image, as this image doesn't have long running process. This is so that it runs for a long period of time and we can go inside the **service** and test our network.
* I believe this is nothing new, we already did this before.
* Only difference is that, now we are using compose and it's convenience.

### Testing Network
* Attach pseudo-tty of `myTester`.

```bash
➜ docker-compose exec myTester sh  
```
* Test the network like we did before. Notice, we are using **`service name`** here to test if two **services** can talk.
* `localhost` will not work. We will learn how we can achieve this later. 

```bash
/ # curl myApp:8080/test | jq .
{
  "message": "Hello Fellas!!!",
  "host.address": "192.168.48.2",
  "app.version": "v1-web"
}
```
