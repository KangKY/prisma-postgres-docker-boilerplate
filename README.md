# prisma-postgres-docker-boilerplate

## Getting Started

### Prerequisites

`Prisma` should be installed with global

```shell
$ npm install -g prisma
```

### .env

you have to fill out `.env` for `docker-compose.yml`

```dosini
PRISMA_MANAGEMENT_API_SECRET=mySecret
PRISMA_PORT=4466 # default prisma port
PRISMA_VERSION=1.33 # recommend version is 1.33
POSTGRES_VERSION=11.4 # recommend version is 11.4
POSTGRES_USER=postgres
POSTGRES_PASSWORD=961205
POSTGRES_PORT=5432
POSTGRES_VOLUMES_TO=postgres

PRISMA_MANAGEMENT_API_SECRET=mySecret
PRISMA_PORT=4466 # default port
LATEST_PRISMA_VERSION=1.33  # recommend version is 1.33
POSTGRES_USER=postgres
POSTGRES_PASSWORD=postgres
POSTGRES_PORT=5432 # default port
```

### Start graphql server
```shell
$ docker-compose up -d
Creating network "test_default" with the default driver
Creating test_db_1     ... done
Creating test_prisma_1 ... done
$ prisma deploy
```
⚠️ it takes some times to start server before `prisma deploy`
after deploying prisma, you can enter these url

- Playground - http://localhost:4466/
- Prisma Admin - http://localhost:4466/_admin

## Repository

### docker-compose.yml

more detailed informations on here "[Set up and connect Prisma with a database](https://www.prisma.io/docs/get-started/01-setting-up-prisma-new-database-JAVASCRIPT-a002/#set-up-and-connect-prisma-with-a-database)"
```yaml
version: '3'
services:
  db:
    image: postgres # official postgres docker image
    environment:
      POSTGRES_PASSWORD: ${POSTGRES_PASSWORD}
      POSTGRES_USER: ${POSTGRES_USER}
    volumes:
      - ./pgdata:/var/lib/postgresql/data
    ports:
      - ${POSTGRES_PORT}:${POSTGRES_PORT}
  prisma:
    image: prismagraphql/prisma:${LATEST_PRISMA_VERSION}
    restart: always
    ports:
      - ${PRISMA_PORT}:${PRISMA_PORT}
    environment:
      PRISMA_CONFIG: |
        managementApiSecret: ${PRISMA_MANAGEMENT_API_SECRET}
        port: ${PRISMA_PORT}
        databases:
          default:
            connector: postgres
            host: host.docker.internal # localhost
            port: ${POSTGRES_PORT}
            user: ${POSTGRES_USER}
            password: ${POSTGRES_PASSWORD}
```
