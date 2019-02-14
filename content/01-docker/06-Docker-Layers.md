---
category: Docker Layers
---
# Docker Layers

## What is docker layers?
* Layer is basically a change on an image, or an intermediate image. 
* Every instructions you specify `(FROM, RUN, COPY, etc.)` in your `Dockerfile` causes the previous image to change, thus creating a new layer. 
* That image layer then becomes the parent for the layer created by the next instruction.
* Final image  and/or image that we use consists of a series of layers which are stacked, one on top of the another.
* You can think of it as staging changes when you're using **git**. You add a file's change, then another one, then another one.

---
* When you build your image before, you also created lots of images.
* `docker build . -t docker-kubernetes:dockerfile-basics`                

```bash
                            +---------------------------------+
             +------------> |          6e384ad670e7           |
             |              | CMD ["java", "-jar", "app.jar"] |
             |              +---------------------------------+
             +------------> |          adfedcd08e78           |
             |              |COPY ["target/*.jar", "./app.jar"|
             |              +---------------------------------+
             +------------> |          aae885b8525e           |
Layer Id+----+              |   RUN apk add --no-cache curl   |
             |              +---------------------------------+
             +------------> |          14446213dae8           |
             |              |           EXPOSE 8080           |
             |              +---------------------------------+
             +------------> |          a40643187675           |
             |              |          WORKDIR /myApp         |
             |              +---------------------------------+
             +------------> |          1b46cc2ba839           |
                            |    FROM openjdk:8-jre-alpine    |
                            +---------------------------------+

```

## Docker Union Filesystem
* By using the union filesystem, Docker combines all these layers into a single image entity.
* Filesystem structure of the top layer will merge with the structure of the layer beneath. 
* Files and directories which have the same path as in the previous layer will override those beneath.

```bash         
        +-----------------+
        |    FILE_4.txt   |----+
        +-----------------+    |
                               |       +---------------+
                               |       |               |
        +-----------------+    |       |  FILE_1.txt   |
        |    FILE_2.txt   |--------->  |  FILE_2.txt   |
        |    FILE_3.txt   |    |       |  FILE_3.txt   |
        +-----------------+    |       |  FILE_4.txt   |
                               |       |               |
                               |       +---------------+
        +-----------------+    |
        |    FILE_1.txt   |----+
        |    FILE_2.txt   |
        +-----------------+
```
* To maintain the order of layers, Docker utilizes the concept of layer IDs and pointers.
* Each layer contains the ID and a pointer to its parent layer. 
* A layer without a pointer referencing the parent is the first layer in the stack, a base.
* Layers are reusable and cacheable. If same kind of layers are found, docker will reuse it.
* Reusable layers is also a reason why Docker is so lightweight in comparison to full virtual machines, which don't share anything. It is thanks to layers that when you pull an image, you eventually don't have to download all of its filesystem
 
```bash
            +---------------------------+
            |         Layer Id          |
            |                           |
            |         Parent Id         |
            +------------|--------------+
                         |
            +------------v--------------+
            |         Layer id          |
            |                           |
            |         Parent Id         |
            +------------|--------------+
                         |
            +------------v--------------+
            |         Layer id          |
            |                           |
            |         Parent Id         |
            +------------+--------------+

```


