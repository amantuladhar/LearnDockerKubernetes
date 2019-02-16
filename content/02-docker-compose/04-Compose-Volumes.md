---
category: Volumes
---
# Docker Compose Volumes

## Volumes
* If you want to save the data so that it is available for next run you need to use volume.
* Compose supports volumes as well.
* Let's try easy way first

```yaml
version: '3'
services:
  myApp:
    image: amantuladhar/docker-kubernetes:v1-web
    ports:
      - 9090:8080
  myDb:
    image: mysql:5.7
    environment:
      - MYSQL_ROOT_PASSWORD=password
      - MYSQL_DATABASE=docker_kubernetes
    volumes:                                       # 1
      - "./volume/mysql-compose/:/var/lib/mysql/"  # 2
```
* `#1` we use **volumes** properties to map the volumes
* `#2` In this case we are mapping host volume to `/var/lib/mysql`.
* Play around with the service.
* Add a table, insert a data then, dispose all services and start up again.
* Check if all data are still there.

---
* Different ways we can map volumes in compose file

```yaml
volumes:
  # Just specify a path and let the Engine create a volume
  - /var/lib/mysql

  # Specify an absolute path mapping
  - /opt/data:/var/lib/mysql

  # Path on the host, relative to the Compose file
  - ./cache:/tmp/cache

  # Named volume
  - datavolume:/var/lib/mysql
```

## Named volumes
* If you want to use named volume in docker-compose, you don't need to create one yourself.
* You have `volumes` root property where you can define your volumes.

```yaml
version: '3'
services:
  myApp:
    image: amantuladhar/docker-kubernetes:v1-web
    ports:
      - 9090:8080
  myDb:
    image: mysql:5.7
    environment:
      - MYSQL_ROOT_PASSWORD=password
      - MYSQL_DATABASE=docker_kubernetes
    volumes:
      - "./volume/mysql-compose/:/var/lib/mysql/"
      - "namedVolume:/test/path"                   # 3                        
volumes:                                           # 1
  namedVolume:                                     # 2     
```
* `#1` root `volumes` property, where we can define *n* number for volumes.
* `#2` name of the volume. You can add some properties inside namedVolume too.
* `#3` using **namedVolume**.

---
