---
category: Dockerfile 1.0 
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



