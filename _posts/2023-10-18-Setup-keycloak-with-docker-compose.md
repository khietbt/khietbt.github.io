---
layout: post
title: "Setup keycloak with docker-compose"
date: 2023-10-18 11:43:01 +0700
categories: keycloak docker docker-compose
---

In my opinion, docker-compose is the best way to setup local environment, and a keycloak can be run
with minimal effort like this:

```yaml
services:
  postgres:
    image: postgres:latest
    environment:
      POSTGRES_DB: sesame-keycloak-service
      POSTGRES_USER: sesame
      POSTGRES_PASSWORD: sesame
  auth:
    image: quay.io/keycloak/keycloak:latest
    ports:
      - "8180:8180"
    environment:
      KEYCLOAK_ADMIN: sesame
      KEYCLOAK_ADMIN_PASSWORD: sesame
      KC_DB: postgres
      KC_DB_URL: jdbc:postgresql://postgres/sesame-keycloak-service
      KC_DB_USERNAME: sesame
      KC_DB_PASSWORD: sesame
      KC_HOSTNAME: localhost
    command:
      - start-dev
      - --http-port 8180
    depends_on:
      - postgres
```

Please take a look at [sesame-keycloak-service](git@github.com:khietbt/sesame-keycloak-service.git)
which is ready to run with sample realms.