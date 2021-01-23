# Artemis Docker-Compose

This is a complete docker-compose stack to run [Artemis](https://github.com/ls1intum/Artemis)
using [LDAP/artemis-server](https://github.com/LDAP/docker-artemis-server) and [LDAP/artemis-clinet](https://github.com/LDAP/docker-artemis-client)

## Getting started

Copy `.env.template` to `.env` and fill in the necessary information. Then run:

```bash
docker-compose up -d
```

This will create the directories and put a default configuration into `ARTEMIS_CONFIG_DIR`.
Adapt this configuration to your needs. A zipped example configuration for all containers can be requested via mail. (~300mb).
Artemis may not start until the configuration is adapted properly. As a starting point use:

`application-artemis.yml:`
```YAML
artemis:
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
Set the new `ARTEMIS_TAG` in `.env`. If the `ARTEMIS_TAG` is set to `latest`, restating will automatically pull the latest release.
Make sure to adapt the version in `application.yml` and to fill newly added properties. The container will not overwrite an existing configuration.

```bash
docker-compose up -d
```