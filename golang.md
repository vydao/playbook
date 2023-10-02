## Starter Kit

- Logging: [zap](https://github.com/uber-go/zap) or zerolog

- Dependency Injection: [wire](https://github.com/google/wire)

- DB migration: [sql-migrate](https://github.com/rubenv/sql-migrate)

- Web framework: [gin](https://github.com/gin-gonic/gin)

- Router: chi

- Configuration: [viper](https://github.com/spf13/viper)

- Command line: [cobra](https://github.com/spf13/cobra)

- Project structure:
  - [golang-standards/project-layout](https://github.com/golang-standards/project-layout)
  - https://medium.com/golang-learn/go-project-layout-e5213cdcfaa2

- JWT: [golang-jwt](https://github.com/golang-jwt/jwt)

- Redis: go-redis

- Generate sql type-safe code: [sqlc](https://github.com/sqlc-dev/sqlc)

- Metrics and Tracing: opentelementry

- UUID: [google/uuid](https://github.com/google/uuid)

- Testing: [testify](https://github.com/stretchr/testify)

- Linter: [golangci-lint](https://github.com/golangci/golangci-lint)

- Kafka Go client: [sarama](https://github.com/IBM/sarama)

## Load balancer health checks

Often the application is running behind a load balaner. Load balancers typically can monitor application servers by polling a given URL. The health check is used so that the load balancer can stop routing traffic to the failing application servers.

The load balancer health check page return 200 status code if the application is healthy.

Recommend URL: `/health` or `/status/health`

## Dockerize your app

We recommend [Docker Compose](https://docs.docker.com/compose/) to make setting up a project on a new development machine fast and easy

Your `docker-compose.yml` file should cover everything your app needs to function properly, including database and migrations. This also ensures everyone is locked on the same Go/Ruby version

For Golang projects, leverage Docker Compose to build & run your app in Alpine/Linux environment, this ensures your app shall work correctly when deployed to the cloud

Avoid putting secrets in the source code. Those should be stored in environment variables to make it configurable in CI/CD pipelines as well as deployment process

