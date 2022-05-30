# Artemis Docker-Compose

This is a complete docker-compose stack to run [Artemis](https://github.com/ls1intum/Artemis)
using [LDAP/artemis-server](https://github.com/LDAP/docker-artemis-server) and [LDAP/artemis-client](https://github.com/LDAP/docker-artemis-client)

## Getting started

Copy `.env.template` to `.env` and fill in the necessary information. Then run:

```bash
docker-compose up -d
```

Docker-Compose downloads and builds the `ARTEMIS_VERSION` set in `.env`. Use `ARTEMIS_VERSION=develop` to build the most current commit (only for development, testing).

On startup the containers will create the directories and Artemis puts a default configuration into `ARTEMIS_CONFIG_DIR`.
Adapt this configuration to your needs. A zipped example configuration for all containers can be requested via mail. (~300mb).
Artemis may not start until the configuration is adapted properly. 

Follow the instructions [here](https://github.com/ls1intum/Artemis/blob/develop/docs/dev/setup/jenkins-gitlab.rst) to set up GitLab and Jenkins.

As a starting point for Artemis use:

`application-artemis.yml:`
```YAML
artemis:
    course-archives-path: data/exports/courses
    repo-clone-path: data/repos
    repo-download-clone-path: data/repos-download 
...
version-control:
        url: http://gitlab:80
...
continuous-integration:
        user: <user>
        password: <pw>
        token: <token>
        url: http://jenkins:8080
...
user-management:
        use-external: false
...
```

`application-prod.yml:`
```YAML
spring:
    ...
    datasource:
        ...
        url: jdbc:mysql://db:3306/Artemis?allowPublicKeyRetrieval=true&createDatabaseIfNotExist=true&useUnicode=true&characterEncoding=utf8&useSSL=false&u
        username: # MYSQL_USER from .env
        password: # MYSQL_ROOT_PASSWORD from .env
...
jhipster:
    ...
    cors:
        allowed-origin-patterns: "*" # Adapt this in production use!
        allowed-methods: "*"
        allowed-headers: "*"
        exposed-headers: "Authorization,Link,X-Total-Count"
        allow-credentials: true
        max-age: 1800
```

`application.yml:`
```YAML
artemis:
    ...
    file-upload-path: data/uploads
    submission-export-path: data/exports
```

## Stopping the stack
```bash
docker-compose down
```

## Updating
```bash
docker-compose down
```
Set the new `ARTEMIS_VERSION` in `.env`. If the `ARTEMIS_VERSION` is set to `develop`, restarting will automatically pull the latest commit.
Make sure to adapt the version in `application.yml` and to fill in newly added properties. The container will not overwrite an existing configuration.

```bash
docker-compose up --build -d
```

## Development
The repository includes a development overwrite. Set the optional variables in `.env`.
This mounts the .jar and static files directly from the Artemis directory. Build Artemis with:
```bash
# Server as jar
./gradlew build -x webapp -x test -x jacocoTestCoverageVerification
# Client
APP_VERSION=$(./gradlew properties -q | grep "^version:" | awk '{print $2}') NODE_OPTIONS="--max_old_space_size=6144" npm run webapp:prod
```
Then run the stack with:
```bash
docker-compose -f docker-compose.yml -f docker-compose.dev.yml up -d
```
