Table of Contents
-------------------

 * [Installation](#installation)
 * [Quick Start](#quick-start)
 * [Persistence](#developmentpersistence)
 * [Linked to other container](#linked-to-other-container)
 * [Logging](#logging)
 * [Out of the box](#out-of-the-box)

Installation
-------------------

 * [Install Docker 1.9+](https://docs.docker.com/installation/) or [askubuntu](http://askubuntu.com/a/473720)
 * Pull the latest version of the image.
 
```bash
docker pull maicost/docker-apache-php
```
Alternately you can build the image yourself.

```bash
git clone https://github.com/maicost/docker-apache-php.git
cd docker-apache-php
docker build -t="$USER/docker-apache-php" .
```

Quick Start
-------------------

Run the application container:

```bash
docker run --name app -d -p 8080:80 maicost/docker-apache-php
```

or

```bash
sudo docker run --name app -v $HOME/Documentos/Docker/docker-apache-php/7.3/app/:/var/www/app/ -d -p 8080:80 $USER/docker-apache-php
```

The simplest way to login to the app container is to use the `docker exec` command to attach a new process to the running container.

```bash
docker exec -it app bash
```

Development/Persistence
-------------------

For development a volume should be mounted at `/var/www/app/`.

The updated run command looks like this.

```bash
docker run --name app -d -p 8080:80 \
  -v /host/to/path/app:/var/www/app/ \
  romeoz/docker-apache-php
```

This will make the development.

Linked to other container
-------------------

As an example, will link with RDBMS PostgreSQL. 

```bash
docker network create pg_net

docker run --name db -d romeoz/docker-postgresql
```

Run the application container:

```bash
docker run --name app -d -p 8080:80 \
  --net pg_net \
  -v /host/to/path/app:/var/www/app/ \
  romeoz/docker-apache-php
```

>See installed php-extension: `docker exec -it app php -m`

Logging
-------------------

All the logs are forwarded to stdout and sterr. You have use the command `docker logs`.

```bash
docker logs app
```

####Split the logs

You can then simply split the stdout & stderr of the container by piping the separate streams and send them to files:

```bash
docker logs app > stdout.log 2>stderr.log
cat stdout.log
cat stderr.log
```

or split stdout and error to host stdout:

```bash
docker logs app > -
docker logs app 2> -
```

####Rotate logs

Create the file /etc/logrotate.d/docker-containers with the following text inside:

```
/var/lib/docker/containers/*/*.log {
    rotate 31
    daily
    nocompress
    missingok
    notifempty
    copytruncate
}
```
> Optionally, you can replace `nocompress` to `compress` and change the number of days.

Out of the box
-------------------
 * Ubuntu 18.04 LTS
 * Apache 2.4.x/2.2.x
 * PHP 7.3
 * Composer (package manager)

>Environment depends on the version of PHP.

License
-------------------

Apache + PHP docker image is open-sourced software licensed under the [MIT license](http://opensource.org/licenses/MIT)