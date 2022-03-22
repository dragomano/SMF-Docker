# SMF Docker

This is a Docker image for testing the freshest version of SMF (from GitHub). Unless you're a developer, you're not likely to need it.

Это образ Docker для тестирования наисвежайшей версии SMF (с Гитхаба). Если вы не разработчик, вам вряд ли это пригодится.

## How to run

```sh
docker-compose up -d
```

## Hosts within your environment

Use the following information to install/configure your SMF:

Service|Hostname|Port number
------|---------|-----------
MariaDB|mariadb|3306 (default)
Postgres|postgres|5432 (default)
Memcached|memcached|11211 (default)

Use `mariadb` or `postgres` instead of `localhost`, and `user/pass` for SMF installing.

## Persisting your application

If you remove the container all your data will be lost, and the next time you run the image the database will be reinitialized. To avoid this loss of data, you should mount a volume that will persist even after the container is removed.

For persistence you should mount a directory at the `/var/www/html` path. If the mounted directory is empty, it will be initialized on the first run. Additionally you should mount a volume for persistence of the MariaDB or PostreSQL data.

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

### PostreSQL

Export:

```sh
docker exec postgres sh -c 'exec pg_dump "$POSTGRES_DB" -U "$POSTGRES_USER"' > pgsql_databases.sql
```

Import:

```sh
docker exec postgres sh -c 'exec psql "$POSTGRES_DB" -U "$POSTGRES_USER"' < pgsql_databases.sql
```
