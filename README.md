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
PRISMA_MANAGEMENT_API_SECRET=my-secret
PRISMA_PORT=4466 # default prisma port
PRISMA_VERSION=1.34 # recommend version is 1.34
POSTGRES_VERSION=11.4 # recommend version is 11.4
POSTGRES_USER=prisma
POSTGRES_PASSWORD=prisma
POSTGRES_PORT=5432 # default postgres port
```

### Start graphql server

#### Run docker container
`docker-compose.yml`에 정의된 설정대로 `prismagraphql`과 `postgres` 이미지를 컨테이너화합니다.

```shell
$ docker-compose up -d
Creating network "prisma-docker_default" with the default driver
Creating volume "prisma-docker_postgres" with default driver
Creating prisma-docker_prisma_1   ... done
Creating prisma-docker_postgres_1 ... done
```

`docker-compose`과정이 마무리되면 로컬에서 `Prisma Server Playground`와 `Prisma Server Admin`에 접속이 가능합니다. 현재는 `prisma`의 데이터 모델에서 `deploy`된 것이 없기 때문에 `Schema`는 비어있는 상태입니다.

- Prisma Server Playground - http://localhost:4466/
- Prisma Server Admin - http://localhost:4466/_admin

#### Deploy prisma server
`prisma.yml`에 정의된 설정대로 `postgres`의 `schema`를 생성함과 동시에 해당 `schema`에 대한 `CRUD`를 포함한 `Query`, `Mutation` 그리고 `Subscription`에 해당하는 `resolver`를 자동으로 생성합니다.


```shell
$ yarn deploy
```
⚠️ it takes some times to start server before `prisma deploy`
after deploying prisma, you can enter these url

이제 `Prisma Server Playground`에서 `datamodel.graphql`에 정의된 데이터에 대한 `Schema`를 확인할 수 있다.

- Prisma Server Playground - http://localhost:4466/
- Prisma Server Admin - http://localhost:4466/_admin

`Prisma Server Playground`를 `GraphQL Server`로 써도 되긴 하지만, 복잡한 `Resolver`를 사용할 수 없기 때문에 `graphql-yoga`를 이용하여 `Prisma Client`에 해당하는 `GraphQL Server`를 `index.ts`에 작성한다. 그리고 `prisma.yml`의 `generate`와 `nexus-prisma-generate` 과정을 통해 `Prisma Client`에 내가 원하는 `Prisma Server`의 `Schema`가 연결된다.

#### Run graphql dev server
`Prisma Client`를 실행한다.
```shell
$ yarn start
```

- GraphQL Dev Server - http://localhost:4000/

## Repository

### prisma.yml
```yaml
endpoint: http://localhost:4466
datamodel: datamodel.graphql
secret: my-secret
generate:
  - generator: typescript-client
    output: ./generated/prisma-client/
hooks:
  post-deploy:
    - npx nexus-prisma-generate --client ./generated/prisma-client --output ./generated/nexus-prisma
```

### docker-compose.yml

more detailed informations on here "[Set up and connect Prisma with a database](https://www.prisma.io/docs/get-started/01-setting-up-prisma-new-database-TYPESCRIPT-t002/)"
```yaml
version: '3'
services:
  prisma:
    image: prismagraphql/prisma:${PRISMA_VERSION}
    restart: always
    ports:
      - '${PRISMA_PORT}:${PRISMA_PORT}'
    environment:
      PRISMA_CONFIG: |
        managementApiSecret: ${PRISMA_MANAGEMENT_API_SECRET}
        port: ${PRISMA_PORT}
        databases:
          default:
            connector: postgres
            host: postgres
            port: ${POSTGRES_PORT}
            user: ${POSTGRES_USER}
            password: ${POSTGRES_PASSWORD}
  postgres:
    image: postgres:${POSTGRES_VERSION}
    restart: always
    environment:
      POSTGRES_USER: ${POSTGRES_USER}
      POSTGRES_PASSWORD: ${POSTGRES_PASSWORD}
    volumes:
      - postgres:/var/lib/postgresql/data
volumes:
  postgres: ~
```
