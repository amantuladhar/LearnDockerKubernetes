---
category: Docker Networks
---
# Docker Networks

## Why do we need network?
* To see why we need inter network communication, let's run two container (in background).

```bash
➜ docker run \
         -p 9090:8080
         -d amantuladhar/docker-kubernetes:dockerfile-basics
```

* Let's try to access our spring app from different container

```bash
➜ docker run \
         -it amantuladhar/network-tools sh 
```
* `network-tools` image have few tools like `curl`, `http`, `ping`, `nc`.
* From this container, let's call `http localhost:8080/test`.
* It won't work because there is no connection between those two containers.
* Consider scenario where our app needs to talk with container that is running **mysql**.

---

## Networks Basics
* We have `docker `**`network`** command to issue instruction related to networks.
* If you do `docker network list`, you will see three networks is already created for you.
  * bridge
  * host
  * none
* These are networks created by different network drivers docker supports. (We have few more drivers).
* Default driver for network is `bridge`.

---

## `docker network create @name`
* To create a network you just issue `docker network create @name`.

```bash
➜ docker network create myNet
```
* This will create network with name `myNet` with driver `bridge`.


---
## `--net=@networkName` option
* To use the network we created we can use `--net` option.

```bash
➜ docker run \
         --name=myApp
         --net=myNet
         -p 9090:8080
         -d amantuladhar/docker-kubernetes:dockerfile-basics
```
* Notice I am naming my container myself using `--name` option. You will know in a bit why I am doing this.
* This will run our container in our network `myNet`.
* Now let's run `network-tools` image on same network.

```bash
➜ docker run \
         --name=myTester \
         --net=myNet
         -it amantuladhar/network-tools sh 
```
* Try to execute `curl` on `localhost:8080/test`. or `localhost:9090/test`
* It won't work. Why? We explicitly ran both of your container on same network.

---
* So do access the app our spring app, we have to use it's name i.e `myApp`.
* If you do `curl` on `myApp:8080/test` it will work.
* Docker has builtin DNS servier that converts container name to it's IP.

## `--net=`**`container:name`**
* `--net` has one more syntax i.e `--net=container:name`.
* If you use this syntax, you will be able to use `localhost` instead of `containerName` to connect to it.
* Run the image that has our app like before.

```bash
➜ docker run \
         --name=myApp
         --net=myNet
         -p 9090:8080
         -d amantuladhar/docker-kubernetes:dockerfile-basics
```

* Run the tester again with `container` syntax.
```bash
docker run \
       --name=myTester \
       --net=container:myApp \
       -it \
       amantuladhar/network-tools \
       sh   
```

* Test using localhost, and it just works.

```bash
/ # curl localhost:8080/test | jq .
{
  "message": "Hello Fellas!!!",
  "host.address": "172.23.0.3",
  "app.version": "v1-web"
}

```

---

> * As a exercise you can try to create a application that stores some values in database.
> * You can use `amantuladhar/docker-kubernetes:v1-db` image. 
> * App expects that you will set `DB_URL`, which is a full connection URL for mysql. 

