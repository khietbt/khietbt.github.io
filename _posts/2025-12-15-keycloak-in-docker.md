---
layout: post
title: Keycloak in Docker
description: Getting Started with Keycloak
date: 2025-12-15T15:12:00.000+07:00
image: https://miro.medium.com/v2/resize:fit:1200/1*6WBzWyYBBTAzL40IguTl4w.png
categories:
  - Keycloak
tags:
  - keycloak
  - docker
---
[Docker](https://www.keycloak.org/getting-started/getting-started-docker) is the best way to work with [Keycloak](www.keycloak.org) - a popular open-source identity manager.

## Preparing a Database

Keycloak is compatible with many kinds of Relational DBMS. It works well with MySQL.

```yaml
services:
  # MySQL
  mysql:
    container_name: eda-mysql-service
    image: mysql:latest
    command: mysqld --character-set-server=utf8mb4 --collation-server=utf8mb4_0900_ai_ci
    env_file:
        - ./environments/env.local
    ports:
      - "3306:3306"
    volumes:
      - ./docker-entrypoint-initdb.d:/docker-entrypoint-initdb.d
    extra_hosts:
      - host.docker.internal:host-gateway
    restart: unless-stopped
```

**Notes:** 

1. `./environments/env.local` contains some variables:

```bash
#!/usr/bin/env bash

MYSQL_ROOT_PASSWORD=root
MYSQL_ALLOW_EMPTY_PASSWORD=no
```

2. `/docker-entrypoint-initdb.d` contains scripts inializing db & accounts.

```sql
CREATE DATABASE `keycloak-service-db`;

CREATE USER 'keycloak-service-db-username'@'%' IDENTIFIED BY 'keycloak-service-db-password';

GRANT ALL PRIVILEGES ON `keycloak-service-db`.* TO 'keycloak-service-db-username'@'%';
```

## Importing Realms into Keycloak

Please refer https://www.keycloak.org/server/importExport for importing / exporting realms.

After exporting data, a docker file is needed to start the keycloak service:

```yaml
services:
  keycloak-service:
    container_name: eda-keycloak-service
    image: quay.io/keycloak/keycloak:latest
    command: start --import-realm
    env_file:
      - ./environments/env.local
    ports:
      - "8080:8080"
      - "9000:9000"
    restart: unless-stopped
    extra_hosts:
      - host.docker.internal:host-gateway
    volumes:
      - ./realms:/opt/keycloak/data/import
```

**Notes:**

1. Environment Variables:

```bash
#!/usr/bin/env bash

KC_HOSTNAME=host.docker.internal
KC_HTTP_PORT=8080
KC_HOSTNAME_STRICT_BACKCHANNEL=false
KC_HTTP_ENABLED=true
KC_HOSTNAME_STRICT_HTTPS=false
KC_HEALTH_ENABLED=true
KEYCLOAK_ADMIN=admin
KEYCLOAK_ADMIN_PASSWORD=admin
KC_DB=mysql
KC_DB_URL=jdbc:mysql://host.docker.internal/keycloak-service-db
KC_DB_USERNAME=keycloak-service-db-username
KC_DB_PASSWORD=keycloak-service-db-password
```

2. `./realms` contains preset data

Keycloak should be ready at http://localhost:8080 after starting with docker compose.
