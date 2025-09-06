# SMF Docker

So, there are some great Docker images like [Wordpess](https://hub.docker.com/_/wordpress).

Why not make a similar solution for SMF? Let's try :)

This is a Docker image for testing the latest stable version of SMF.

Это образ Docker для тестирования текущей стабильной версии SMF.

## Requirements

- Docker Desktop
- Docker Compose

## Environments

See `.env` file.

## How to change PHP version

- Change `PHP_VERSION` in `.env` file
- Run `docker compose build php-fpm`
- Run `docker compose down`
- Run `docker compose up -d`

## How to run and stop

### Apache (default)

```sh
docker compose up -d
```

```sh
docker compose down
```

### Nginx

```sh
docker compose -f nginx.yaml up -d
```

```sh
docker compose -f nginx.yaml down
```

## Hosts within your environment

Use the following information to install/configure your SMF:

| Service   | Hostname  | Port number     |
| --------- | --------- | --------------- |
| MySQL     | mysql     | 3306 (default)  |
| MariaDB   | mariadb   | 3306 (default)  |
| Postgres  | postgres  | 5432 (default)  |
| Memcached | memcached | 11211 (default) |
| Redis     | redis     | 6379 (default)  |
| FTP       | localhost | 21 (default)    |

Login/pass by default - `user/pass`.

## Persisting your application

If you remove the container all your data will be lost, and the next time you run the image the database will be reinitialized. To avoid this loss of data, you should mount a volume that will persist even after the container is removed.

For persistence you should mount a directory at the `/app` path. If the mounted directory is empty, it will be initialized on the first run. Additionally you should mount a volume for persistence of the MariaDB or PostgreSQL data.

To avoid inadvertent removal of volumes, you can mount host directories as data volumes. Alternatively you can make use of volume plugins to host the volume data.

## Backup databases

### MySQL/MariaDB:

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
        php${PHP_VERSION}-redis \
+       php${PHP_VERSION}-xdebug; \
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

## Add the section “environment” to the php-fpm service in `compose.yaml`:

```
environment:
  PHP_IDE_CONFIG: "serverName=Docker"
```

### Create a server configuration in PHPStorm:

- In PHPStorm open Preferences | Languages & Frameworks | PHP | Servers
- Add new server
- The “Name” field should be the same as the parameter “serverName” value in “environment” in _compose.yaml_ (i.e. _Docker_ in the example above)
- A value of the "port" field should be the same as first port(before a colon) in "webserver" service in _compose.yaml_
- Select "Use path mappings" and set mappings between a path to your project on a host system and the Docker container.
- Finally, add “Xdebug helper” extension in your browser, set breakpoints and start debugging

## Do you want to know more about Docker?

See [Docker for local web development, introduction: why should you care?](https://tech.osteel.me/posts/docker-for-local-web-development-introduction-why-should-you-care)

## Alternatives

If this solution seems too complicated for you, try [Laradock](https://laradock.io/getting-started/#installation).
