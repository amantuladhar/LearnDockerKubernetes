---
category: Dockerfile Basics
---
# `Dockerfile` And Basic Instructions

## What is `Dockerfile`
* `Dockerfile` is just a plain text file.
* The plain text file contains series of instruction.
* Docker will read `Dockerfile` when you start the process of building an image.
* It executes the instruction one by one in orderly fashion.
* At the end final read-only image is created.
* **It's important to know that Docker uses images to run your code, not the `Dockerfile`.**
* `Dockerfile` is just a convienent way to build your docker images.
* Default file name for `Dockerfile` is `Dockerfile`.


## `Dockerfile` Basics

### `FROM`
* `FROM @image:@tag`
* `FROM` is the first instruction in `Dockerfile`.
* Sets the base image for every subsequent instruction.
* If you skip tag, docker will use latest tag.
* Latest tag may not be latest image.
* If we provide invalid tag, docker will complain.

```dockerfile
FROM openjdk:8-jre-alpine

```

### `docker build`
* Even if you have single instruction `FROM` you can build your image.
* It is as easy as executing `docker build @contextPath -t @name:@tag`.
* `@contextPath` is the place where your `Dockerfile` exists.
* `-t` represents `tag`. This is so that you can name the image you are building.
* If you omit the `tag` part, docker will add by default **latest** tag.
* If you `Dockerfile` is not named Dockerfile you can use `-f` option to specify the filename.
* Notice when it builds an image, if you don't have the image that you used locally it will download it first.

```bash
➜ docker build . -t amantuladhar/docker-kubernetes:dockerfile-basics
Sending build context to Docker daemon  17.02MB
Step 1/1 : FROM openjdk:8-jre-alpine
8-jre-alpine: Pulling from library/openjdk
6c40cc604d8e: Pull complete 
e78b80385239: Pull complete 
f41fe1b6eee3: Pull complete 
Digest: sha256:712abc7541d52998f14b43998227eeb17fd895cf10957ad5cf195217ab62153e
Status: Downloaded newer image for openjdk:8-jre-alpine
 ---> 1b46cc2ba839
Successfully built 1b46cc2ba839
Successfully tagged amantuladhar/docker-kubernetes:dockerfile-basics
```

* Run `docker image list` and you will see your image on the list
* (Psst!! You can go inside your image an play around with it)

### WORKDIR
* `WORKDIR /path`
* Sets starting point from where you want to execute subsequent instructions.
* We can set both absolute / relative path
* We can have multiple `WORKDIR` in images
* If relative path is used, it will be relative to previous `WORKDIR`
* Think of this as changing the directory

```dockerfile
FROM openjdk:8-jre-alpine
WORKDIR /myApp
```

### COPY
* `COPY @source @destination`
* Copies files from source to container file system
* Source can be path or URL
* Path is relative to where build process was started.
* Both can contain wildcards like * and ?

---
* Let's add our app executable inside docker image.
* Before that you need a app. You can create one yourself or I have a simple spring app created for you on https://github.com/amantuladhar/DockerKubernetesFiles
* For this excercise let's switch to brach  `dockerfile-initial`. 
* `dockerfile-initial-complete` has final `Dockerfile` if you want to cheat.
* Let's build `jar` for our app. `./mvnw clean install`
* Maven creates `jar` inside **/target** folder.

```dockerfile
FROM openjdk:8-jre-alpine
WORKDIR /myApp
COPY ["target/*.jar", "./app.jar"]
```

* That's it. Build your image. Go inside an run the app

```bash
Sending build context to Docker daemon  17.03MB
Step 1/3 : FROM openjdk:8-jre-alpine
 ---> 1b46cc2ba839
Step 2/3 : WORKDIR /myApp
 ---> Running in 529b938e7bde
Removing intermediate container 529b938e7bde
 ---> a40643187675
Step 3/3 : COPY ["target/*.jar", "./app.jar"]
 ---> 63722ad415fe
Successfully built 63722ad415fe
Successfully tagged amantuladhar/docker-kubernetes:dockerfile-basics

```
* Running you app inside container

```bash
➜ docker run -it amantuladhar/docker-kubernetes:dockerfile-basics   
/myApp # ls
app.jar

/myApp # java -jar app.jar 

  .   ____          _            __ _ _
 /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
 \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
  '  |____| .__|_| |_|_| |_\__, | / / / /
 =========|_|==============|___/=/_/_/_/
 :: Spring Boot ::        (v2.1.2.RELEASE)
...
...
2019-02-13 15:02:09.789  INFO 9 --- [           main] o.s.b.w.embedded.tomcat.TomcatWebServer  : Tomcat started on port(s): 8080 (http) with context path ''

```

* You still can't access the app from outside as you haven't asked docker to export the port the app is listening to.

### ADD
* `ADD @source @destination`
* Exactly as `COPY`
* Has few features like, archive extractions. (Like extracting arhieve automatically)
* Best practice is to use `COPY` if you don’t need those additional features.


### EXPOSE
* `EXPOSE @port`
* `EXPOSE` tells Docker the running container listens on specific network ports. 
* This acts as a kind of port mapping documentation that can then be used when **publishing the ports**.
* `EXPOSE` will **not allow** communication via the defined ports to containers outside of the same network or to the host machine. 
* To allow this to happen you need to publish the ports using `-p` options when running container

```dockerfile
FROM openjdk:8-jre-alpine
WORKDIR /myApp
EXPOSE 8080
COPY ["target/*.jar", "./app.jar"]

```

### Port Publishing
* If you want to access your app from the host (outside the container), you need to publish the port container listens to.
* To do that we have `-p @hostPort:@containerPort` option.
* `docker run -it `**`-p 9090:8080`**`amantuladhar/docker-kubernetes:dockerfile-basics` 
* Now you can access you app on **localhost:9090**

```bash
➜ docker run -it -p 9090:8080 docker-kubernetes:dockerfile-basics

# Running your app inside the container
/myApp # java -jar app.jar 
....
....
2019-02-14 04:47:05.036  INFO 10 --- [           main] o.s.b.w.embedded.tomcat.TomcatWebServer  : Tomcat started on port(s): 8080 (http) with context path ''
2019-02-14 04:47:05.046  INFO 10 --- [           main] c.e.cloudnative.CloudNativeApplication   : Started CloudNativeApplication in 4.73 seconds (JVM running for 5.685)

```
* Here's a response that we get when we call `localhost:9090/test`

```bash
➜ http localhost:9090/test  
HTTP/1.1 200 
Content-Type: application/json;charset=UTF-8
Date: Thu, 14 Feb 2019 04:49:07 GMT
Transfer-Encoding: chunked

{
    "app.version": "v1-web",
    "host.address": "172.17.0.2",
    "message": "Hello Fellas!!!"
}

```

### RUN
* `RUN @command`
* `RUN` is central executing instruction for `Dockerfile`.
* `RUN` command will execute a command or list of command in a new layer on top of current image
* Resulting image will be new base image for subsequent instruction
* To make your `Dockerfile` more readable and easier to maintain, you can split long or complex `RUN` statements on **multiple lines separating them with a backslash ( `\` )**

---
* Let's install `curl` in our image. (We will need to use `curl` later)
* `RUN apk add --no-cache curl`

```dockerfile
FROM openjdk:8-jre-alpine
WORKDIR /myApp
EXPOSE 8080
RUN apk add --no-cache curl
COPY ["target/*.jar", "./app.jar"]
```

* If you build your app now, you will have `curl` command when you run the container.
* You can test if `curl` exist if you want.

```bash
➜ docker build . -t docker-kubernetes:dockerfile-basics          
...
Step 4/5 : RUN apk add --no-cache curl
 ---> Running in 883ac8f78866
fetch http://dl-cdn.alpinelinux.org/alpine/v3.9/main/x86_64/APKINDEX.tar.gz
fetch http://dl-cdn.alpinelinux.org/alpine/v3.9/community/x86_64/APKINDEX.tar.gz
(1/4) Installing nghttp2-libs (1.35.1-r0)
(2/4) Installing libssh2 (1.8.0-r4)
(3/4) Installing libcurl (7.63.0-r0)
(4/4) Installing curl (7.63.0-r0)
...
```










