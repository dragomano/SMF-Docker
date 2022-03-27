# SMF Docker

This is a Docker image for testing the freshest version of SMF (from GitHub). Unless you're a developer, you're not likely to need it.

Это образ Docker для тестирования наисвежайшей версии SMF (с Гитхаба). Если вы не разработчик, вам вряд ли это пригодится.

## How to run/stop

```sh
docker-compose up -d
```
```sh
docker-compose down
```

## Hosts within your environment

Use the following information to install/configure your SMF:

Service|Hostname|Port number
------|---------|-----------
MariaDB|mariadb|3306 (default)
Postgres|postgres|5432 (default)
Memcached|memcached|11211 (default)
Redis|redis|6379 (default)

Use `mariadb` or `postgres` instead of `localhost`, and `user/pass` for SMF installing or Adminer connection.

## Persisting your application

If you remove the container all your data will be lost, and the next time you run the image the database will be reinitialized. To avoid this loss of data, you should mount a volume that will persist even after the container is removed.

For persistence you should mount a directory at the `/var/www/html` path. If the mounted directory is empty, it will be initialized on the first run. Additionally you should mount a volume for persistence of the MariaDB or PostgreSQL data.

To avoid inadvertent removal of volumes, you can mount host directories as data volumes. Alternatively you can make use of volume plugins to host the volume data.

### Mount host directories as data volumes with Docker Compose

This requires a minor change to the [`docker-compose.yml`](https://github.com/dragomano/SMF-Docker/blob/main/docker-compose.yml) file present in this repository:

```diff
    mariadb:
        ...
        volumes:
-           - mysql:/var/lib/mysql
+           - /path/to/mariadb-persistence:/var/lib/mysql
    postgres:
        ...
        volumes:
-           - pgsql:/var/lib/postgresql/data
+           - /path/to/postgres-persistence:/var/lib/postgresql/data
    ...
    php-fpm:
        ...
        volumes:
-           - smf:/var/www/html
+           - /path/to/smf-persistence:/var/www/html
    ...
-volumes:
-    smf:
-    mysql:
-    pgsql:
```

## Backup databases

### MariaDB:

Export:

```sh
docker exec mariadb sh -c 'exec mysqldump "$MYSQL_DATABASE" -u"$MYSQL_USER" -p"$MYSQL_PASSWORD"' > mysql_databases.sql
```

Import:

```sh Import
docker exec -i mariadb sh -c 'exec mysql "$MYSQL_DATABASE" -u"$MYSQL_USER" -p"$MYSQL_PASSWORD"' < mysql_databases.sql
```

### PostgreSQL

Export:

```sh
docker exec postgres sh -c 'exec pg_dump "$POSTGRES_DB" -U "$POSTGRES_USER"' > pgsql_databases.sql
```

Import:

```sh
docker exec postgres sh -c 'exec psql "$POSTGRES_DB" -U "$POSTGRES_USER"' < pgsql_databases.sql
```

## Xdebug 3

To install **xdebug** extension just add this line in `php-fpm/Dockerfile`:

```diff
        php7.4-redis \
+       php7.4-xdebug; \
```

To configure **Xdebug 3** you need add these lines in `php-fpm/php-ini-overrides.ini`:

### For Linux:

```ini
xdebug.mode = debug
xdebug.remote_connect_back = true
xdebug.start_with_request = yes
```

### For MacOS and Windows:

```ini
xdebug.mode = debug
xdebug.remote_host = host.docker.internal
xdebug.start_with_request = yes
```

## Add the section “environment” to the php-fpm service in docker-compose.yml:

```
environment:
  PHP_IDE_CONFIG: "serverName=Docker"
```

### Create a server configuration in PHPStorm:

* In PHPStorm open Preferences | Languages & Frameworks | PHP | Servers
* Add new server
* The “Name” field should be the same as the parameter “serverName” value in “environment” in docker-compose.yml (i.e. *Docker* in the example above)
* A value of the "port" field should be the same as first port(before a colon) in "webserver" service in docker-compose.yml
* Select "Use path mappings" and set mappings between a path to your project on a host system and the Docker container.
* Finally, add “Xdebug helper” extension in your browser, set breakpoints and start debugging
