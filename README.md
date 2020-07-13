## Development

### Create you .env

```bash
cp .env.example .env
```

You can use the default values in this file to run the container. 
You only need to change the variable `SSH_PATH` to a ABSOLUTE PATH of you ssh directory so that the container get accesss
to you keys.

### Prepare DB

Into `mariadb/docker-entrypoint-initdb.d` directory and copy the `createdb.sql.example` to `createdb.sql` 

### Prepare the source code of the other projects

Clone all the applications of the platform before continue in the same level of this project. For example:

```
/
 - docker
 - challenges
 - other-apps
```

### Debug in Linux

In Linux, you need to create the following environment variable to debug from command line

```bash
export DOCKER_HOST_IP=$(docker network inspect bridge |awk '/"Gateway"/ {gsub ("\"","") ;print $2}')
```

you can add to your `~/.zshrc` or `~/.bashrc` to avoid run this command each time a new terminal is open

### Build and Run

Builde the base image. It has add xdebug, composer and other development tools

```bash
docker-compose build laravel
```

Up and running the platform

```bash
docker-compose up -d nginx mariadb
```

### Edit hosts file

Add the following entries to the `/etc/hosts`

```bash
127.0.0.1   challenges.local.webwork.com
```

Now you can access to the app in your browser through this url.

### Connections to services

If you want to connect to MariaDB you need see the .env file. You need to be awarded that in the host machine
the hosts is always _localhost_ and inside the containers is the service name, for example, _mariadb_
to MariaDB.

### Shutdown 

```bash
docker-compose down
```

### Run commands

To run command inside the apps, you need to enter to the container. For example, for enter to the challenges container you need to run:

```bash
docker-compose exec --user=www-data challenges sh
```

Now you can run the project instructions. For example, run migration or install dependencies with composer.

```bash
/var/www/challenges $ artisan queue:work
```

