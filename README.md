# Docker-based environment for Drupal

Use this Docker compose file to spin up local environment for Drupal with a *native Docker app* on Linux, Mac OS X and Windows. 

* [Overview](#overview)
* [Instructions](#instructions)
* [Importing database](#importing-database)
* [Creating database dumps](#creating-database-dumps)
* [Drush](#drush)
* [Accessing containers](#accessing-containers)
* [Logs](#logs)
* [Updating](#updating)
* [Status](#status)
* [Troubleshooting](#troubleshooting)
* [Going beyound local machine](#going-beyond-local-machine)

## Overview

The Drupal bundle consist of the following containers:

| Container | Service name | Image | 
| --------- | ------- | ----- |
| Nginx   | web | <a href="https://hub.docker.com/r/wodby/drupal-nginx/" target="_blank">wodby/drupal-nginx</a> |
| PHP 7   | app | <a href="https://hub.docker.com/r/wodby/drupal-php/" target="_blank">wodby/drupal-php</a> |
| MariaDB | db  |<a href="https://hub.docker.com/r/wodby/drupal-mariadb/" target="_blank">wodby/drupal-mariadb</a> |

PHP, Nginx and MariaDB configs are optimized to be used with Drupal. We regularly update this bundle with performance improvements, bug fixes and newer version of Nginx/PHP/MariaDB.

## Instructions 

Supported Drupal versions: 7 and 8

1\. Install docker for <a href="https://docs.docker.com/engine/installation/" target="_blank">Linux</a>, <a href="https://docs.docker.com/engine/installation/mac" target="_blank">Mac OS X</a> or <a href="https://docs.docker.com/engine/installation/windows" target="_blank">Windows</a>. You need to install docker 1.12, not docker toolbox. 

For Linux additionally install <a href="https://docs.docker.com/compose/install/" target="_blank">docker compose</a>

2\. Download <a href="https://raw.githubusercontent.com/Wodby/drupal-compose/master/docker-compose.yml" target="_blank">the compose file</a> from this repository

3\. Since containers <a href="https://docs.docker.com/engine/tutorials/dockervolumes/" target="_blank">do not have a permanent storage</a>, directories from the host machine (volumes) should be mounted: one with your Drupal project and another with database files. By default `docroot` and `mariadb` directories will be automatically created in the same directory as the compose file. 

First, let's edit the path to volume with your Drupal project for the PHP container. Find the following line in the downloaded `drupal-compose.yml` file
```yml
- ./docroot:/var/www/html
```

and replace it with your path:
```yml
- /path/to/my/drupal/docroot:/var/www/html
```

Additionally, you can adjust path to the database files directory (`- ./mariadb:/var/lib/mysql`). 

**Linux only**: fix permissions for your files directory with:
```bash
$ sudo chgrp -R 82 sites/default/files
$ sudo chmod -R 775 sites/default/files
```

4\. Choose Drupal version by modifying the following environment variable (could be 7 or 8) in the compose file:
```yml
DRUPAL_VERSION: 8
```

5\. Update database credentials in your settings.php file:
```
database: drupal
username: drupal
password: drupal
host: db
```

6\. If you want to import your database, uncomment the following line in the compose file:
```yml
#    - ./mariadb-init:/docker-entrypoint-initdb.d
```

Create the volume directory `mariadb-init` in the same directory as the compose file and put there your SQL file(s). All SQL files will be automatically imported once MariaDB container has started.

7\. If you want to create a database dump just execute:
```bash
# Dump all databases.
docker-compose exec db sh -c 'exec mysqldump --all-databases -uroot -p"root-password"' > databases.sql

# Dump specific one.
docker-compose exec db sh -c 'exec mysqldump -uroot -p"root-password" my-db' > my-db.sql
```

8\. Now, let's run the compose file. It will download the images and run the containers:
```bash
$ docker-compose up -d
```

9\. Make sure all containers are running by executing:

```bash
$ docker-compose ps
```

10\. That's it! You drupal website should be up and running at http://localhost:8000. 

## Drush

PHP container has installed drush, connect to the container to use drush.

## Accessing containers

You can connect to any container by executing the following command (example for PHP container):
```bash
$ docker-compose exec app sh
```

Replace `app` with the name of your service: `db` (mariadb) or `web` (nginx).

## Logs

To get logs from a container simply run (skip the last param to get logs form all the containers):
```
$ docker-compose logs [service]
```

Example: real-time logs of php container:
```
$ docker-compose logs -f app
```

## Updating

We update containers from time to time, to get the lastest changes simply run again:
```
$ docker-compose up -d
```

## Status

We're actively working on this instructions and containers. More options will be added soon. If you have a feature request or found a bug please sumbit an issue.

## Troubleshooting

In case you have any problems submit an issue or contact us at hello [at] wodby.com.

## Going beyond local machine

Check out <a href="https://wodby.com" target="_blank">Wodby</a> if you need optimized consistent docker-based environment for Drupal on your dev/staging or production servers. 
