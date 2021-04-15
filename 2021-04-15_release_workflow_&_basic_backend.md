**1. Workflow to release dev/staging/production environment**

- Coding feature on laptop (local dev environment)
- After coding review, if ok -> merged into develop branch (dev environment)
- Release to staging environment & being tested by QC
- After well being tested, code is merged into master (production environment)

**2. Go dep vs go module, GOPATH vs GOMOD**

**Sources:** [golangbyexample.com](https://golangbyexample.com/package-vs-module-golang/), [reddit](https://www.reddit.com/r/golang/comments/ff6eet/go_modules_vs_gopath_creating_the_workspace/)

- Go dep (Go version >= 1.9)
  - Deprecated & archived in favor of Go module
  - Grab whatever version of library it can find (grab latest): If the new version of library have bugs or change function name -> Taking time to detect and fix our code that depends of these, or waiting for library developers to fix the bug of library
- Go module (Go version >= 1.11)
  - Project has `go.mod` configuration file with semantic versioning which version you are importing
  - All dependencies will be downloaded in $GOPATH/pkg/mod directory with versioning. If downloading many version of the same library, it does not overriding each other
  - Project can be out of $GOPATH/src
  - `go.mod` owing name + go version of module + dependency with version
  - `go.sum` owing checksum of direct & indirect dependency required by the module
- GOPATH
  - Impossible to track version of other packages were used to build a specific exe
  - Heavily contributes to "work on my machine" syndrome
- GOMOD
  - Modules allow dependencies to be explicit, not just something another dev has lying around your laptop

**3. Difference between loading config from .env file & loading config from environment variable**

**Source:** [dev.to](https://dev.to/techschoolguru/load-config-from-file-environment-variables-in-golang-with-viper-2j2d)

- Loading config from .env file:
  - For development: easily specifying default config for local dev & testing
  - .env file could have: API keys, encryption keys, API Urls
- Loading config from environment variable:
  - For development (in staging & production): easily override the default config when deploy with Docker containers

**4. Analyze a datasource (connection string) of any database**

**Source:** [postgresql.org](https://www.postgresql.org/docs/13/libpq-connect.html)

There are two accepted formats of connection string

- Key/Value connection string:
  - Each parameter setting in the form: keyword=value
  - Spaces around the equal sign are optional: E.g. kw=value the same to kw = value
  - Empty value/Value containing spaces -> surround with single quotes. E.g. kw = 'a value'
  - Single quotes & backslashes within the value must be escaped with back slash. E.g. \' and \\
- Connection URIs: postgresql://[user[:password]@][host][:port][,...][/dbname][?param1=value1&...]
  - postgresql://
  - postgresql://localhost
  - postgresql://localhost:5433
  - postgresql://localhost/mydb
  - postgresql://user@localhost
  - postgresql://user:password@localhost
  - postgresql://other@localhost/dbname?connect_timeout=10&app_name=myapp
  - postgresql://host1:123,host2:456/somedbname?target_session_attrs=any&app_name=myapp

**5. Using Docker, build a hello world server with port 8080 + PostgreSQL in local**

- Dockerfile

```
FROM golang:1.16.3
WORKDIR /go/src/code
EXPOSE 8080
COPY . .
CMD ["go", "run", "main.go"]
```

- docker-compose.yml

```
version: "3.9"
services:
  db:
    image: postgres
    environment:
      - POSTGRES_PASSWORD=admin
      - POSTGRES_USER=admin
      - POSTGRES_DB=sampledb
  web:
    build: .
    volumes:
      - .:/go/src/code
    ports:
      - "8080:8080"
    depends_on:
      - db
```

**Result**

![Golang server built by Docker](img/2021-04-15_golang_server_with_docker.png)
