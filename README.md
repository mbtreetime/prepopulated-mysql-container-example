A prepopulated mysql container ?
================================

Yeah why not, i like my test faster :D

But how ?
---------

The mysql/mariadb container image contain an init script that will execute everything in `/docker-entrypoint-initdb.d/`

see `Initializing a fresh instance` @ https://hub.docker.com/_/mysql/

So let's run this initialization in a multi-stage build and copy the initialized DB in the new image :D

(see comments in dockerfile for details of the hack) 

Try it!
======

Clone this, then...

```
}> docker build --tag my-prepopulated-image .
...

}> docker run -d --rm --name my-container my-prepopulated-image
...

}> docker logs my-container

(there was is initialization here, therefore we win)

2022-05-17 18:37:01+00:00 [Note] [Entrypoint]: Entrypoint script for MySQL Server 5.7.38-1debian10 started.
...
2022-05-17T18:37:01.372794Z 0 [Note] mysqld (mysqld 5.7.38) starting as process 1 ...
...
2022-05-17T18:37:01.744932Z 0 [Note] Event Scheduler: Loaded 0 events
2022-05-17T18:37:01.745182Z 0 [Note] mysqld: ready for connections.
Version: '5.7.38'  socket: '/var/run/mysqld/mysqld.sock'  port: 3306  MySQL Community Server (GPL)

}> docker run -it --rm --link my-container mysql:latest mysql -hmy-container -umyuser -pmypassword mydatabase -e "select * from mytable;" 2>/dev/null
+---------+
| myfield |
+---------+
| Hello   |
| Dolly   |
+---------+
```
