---
category: Docker Volumes
---
# Mount host directory into container.

---
## Mount host path
* `-v` has yet another major use case, which can only be accomplished throught `-v` option.
* Mount the host directory into container.
* `docker run -v @host-path:@container-path -it busybox`
  * host path must be absolute.
  * container path is absolute as well.
* `docker run -v $(pwd)/volume/:/my-volume -it busybox`
  * We can use `$(pwd)` to get absolute path for current directory.
  * So in above command, we are mounting `/volume` directory that relative to where build process was started.

---
## Simple example
* Let's try this with `busybox` first.
* `docker run -v $(pwd)/volume/:/my-volume -it busybox`
* Create a file under `/my-volume` inside the container.
* You will see that file in host directory as well, under `"CRRENT_DIR"/volume`.

---
## Where is this syntax useful?
* Using volume on these case doesn't make sense.
* We are creating a file and checking if it exists in subsequent runs.
* But consider this scenario when you run `mysql` image.
* You would want to persist the data that you created inside container.
* If on subsequent run you start with clean database, what the meaning of using database right?

---
* To achieve this we can use `-v` option.
* Either `-v @volume-name:@path` or `-v @host-path:@container-path`.
* If you would like to specify the place where files gets stored in host, you may choose second option.
* For this demo I will choose second one i.e `-v @host-path:@container-path`.

---
## Using to persist `mysql` data
* `mysql` stores its data inside `/var/lib/mysql/`.
* So if we map our host path with `/var/lib/mysql/`, we will be able to persit `mysql` data.
* If you try to run the `mysql` image it expectes at least couple of **Environment Variable**.
  * MYSQL_ROOT_PASSWORD
  * MYSQL_DATABASE

```bash
docker run \
       -v $(pwd)/volume/mysql-data:/var/lib/mysql/ \
       -e MYSQL_ROOT_PASSWORD=test \
       -e MYSQL_DATABASE=test \
       mysql:5.7
```

---
**NOTE**
* I am using `-e` options to pass in **Environment Variables** to container.
* I am deliberatey not passing `-it` when running container.
* Default `CMD` for `mysql` image is used for starting mysql
* If we override it my passing **Shell** executable, to attach the pseudo tty, it won't start the mysql.

---
* After container is running, we can attach container tty into host terminal.
* You already learned this but in case you forgot. To attach the pseudo tty for a running container we use `docker container exec -it @container(id/name) sh`
 
```bash
➜ docker container exec -it 750d0ddbbe51 sh
```
* Login to mysql from container terminal.
* Create a table and insert some data into that table.

```bash
> mysql -u root -p

mysql> USE test;
Database changed

mysql> CREATE TABLE student(id INT(11), name VARCHAR(255));
Query OK, 0 rows affected (0.02 sec)

mysql> INSERT INTO student VALUES (1, 'Aman');
Query OK, 1 row affected (0.01 sec)

mysql> SELECT * FROM student;
+------+------+
| id   | name |
+------+------+
|    1 | Aman |
+------+------+
1 row in set (0.00 sec)

```

* Now you stop the running container and run a new one, with same path (`$(pwd)/volume/mysql-data`) mapped to `/var/lib/mysql/`, you will see data is persisted for new runs.
```bash
docker run \
       -v $(pwd)/volume/mysql-data:/var/lib/mysql/ \
       -e MYSQL_ROOT_PASSWORD=test \
       -e MYSQL_DATABASE=test \
       mysql:5.7
```

