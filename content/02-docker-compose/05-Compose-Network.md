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

## Named Network
* While relying on default network works, you may want to name your network yourself. You may even want to create multiple networks on a single compose file.
* To achieve that we have **networks** properties.
* If we use it as a root property, we can define any number custom networks we want.
* If used inside **services** property, it will make that particular **service** reside in the network.

```yaml
version: '3'
services:
  myApp:
    image: amantuladhar/docker-kubernetes:v1-web
    ports:
      - 9090:8080
    networks:                              # 3     
      - myNet                              # 4    
  myTester:
    image: amantuladhar/network-tools
    command: sleep 50000
    networks:                              # 3    
      - myNet                              # 4    
  myDb:
    image: mysql:5.7
...
...
networks:                                  # 1
  myNet:                                   # 2
...
...
``` 
* `#1` this is a root property. This includes list of custom network you want to create.
* `#2` name of network you want to create.
* `#3` this is used inside **services**. This defines which network particular **serivice** communicates with.
* `#4` Name of networks **service** belongs to. You can add multiple networks
* You can check if two containers can talk with each other like before.

## `network_mode`
* Which help of network mode we can define which network mode to operate on.
* Remember `--net=container:@name` syntax. `network_mode` can achieve something like this.
* `network_mode` supports
  * "bridge"
  * "host"
  * "none"
  * "service:@name"
  * "container:@id/@name" 
* I think `serive:name` is most useful in our case as we are dealing with services.

```yaml
version: '3'
services:
  myApp:
    image: amantuladhar/docker-kubernetes:v1-web
    ports:
      - 9090:8080
    networks:                                   
      - myNet                                 
  myTester:
    image: amantuladhar/network-tools
    command: sleep 50000
    network_mode: service:myApp           # 1
  myDb:
    image: mysql:5.7
...
...
...
``` 
* `#1` This defines that we want to use same network that is being used by **service** `myApp`.
* Notice we didn't even have to define `networks` properties for that particular service.
* For a single service you cannot define both `ports` and `network_mode` at a same time.
* Testing using `localhost` in `myTester`
```bash
/ # curl localhost:8080/test
{
  "message": "Hello Fellas!!!",
  "host.address": "192.168.112.2",
  "app.version": "v1-web"
} 
```

