---
title: "docker compose up"
description: "Simple guide for Docker-Compose and Healthchecks"
date: 2025-06-15T20:14:00+03:00
tags: ["docker", "devops"]
---

Docker Compose is a tool used to define and run multi-container Docker applications using a simple YAML file docker-compose.yml
It lets you start, stop, and manage multiple containers (like app + database + cache) with a single command (docker compose up), making development and deployment easier.

```yaml fold:false title:docker-compose.yml
version: "3.8"

services:
  flask-app:
    build: . #path to dockerfile
    #to use existing image use image tag like in db and give path to image.
    ports:
      - "5000:5000"
    environment:
      - MYSQL_HOST=db
      - MYSQL_USER=root
      - MYSQL_PASSWORD=rootpassword
      - MYSQL_DB=flaskdb
    depends_on:
      - db
    networks:
      - app-network

  db:
    image: mysql:8.0
    restart: always
    environment:
      - MYSQL_ROOT_PASSWORD=rootpassword
      - MYSQL_DATABASE=flaskdb
    volumes:
      - mysql-data:/var/lib/mysql
    networks:
      - app-network

volumes:
  mysql-data:

networks:
  app-network:
    driver: bridge
```

### 1. Healthchecks

A way to tell its status beyond just 'is it running'

You define a healthcheck using the `HEALTHCHECK` instruction in a Dockerfile or inside a `docker-compose.yml`.
Docker then:

- Periodically runs the healthcheck command inside the container.
- Marks the container as:
  - **healthy**
  - **unhealthy**
  - **starting** (during initial delay or first checks)

```
db:
  image: mysql:8.0
  environment:
    - MYSQL_ROOT_PASSWORD=rootpassword
    - MYSQL_DATABASE=flaskdb
  healthcheck:
    test: ["CMD", "mysqladmin", "ping", "-h", "localhost"]
    interval: 10s
    timeout: 5s
    retries: 3
```

- `test`: The command to check health (`mysqladmin ping` checks if MySQL is accepting connections).
- `interval`: How often to run the check (every 10 seconds).
- `timeout`: Max time to wait before considering the check failed (5 seconds).
- `retries`: If it fails `3` times in a row, the container is marked **unhealthy**.
