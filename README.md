# Dockerfile-ansible-probe

This image makes it possible to test services by probing a specified host:port. For example, it could
be useful when you start mysql container and want to ensure that mysql-server is already running.

### Example

In the example below there are two containers. When you execute `make run-mysql`, docker-compose will try to create `mysql-agent` container first.
Since `mysql-agent` is linked with `mysql`, `mysql` container also will be created. After that, while mysql server is starting, `mysql-agent`
will test mysql server until it is ready. When mysql server is ready, `mysql-agent` will be stopped and removed.

**docker-compose.yml**
```yaml
version: '2'

services:
  mysql-agent:
    image: oliosinter/ansible-probe:1.0.1
    links:
      - mysql
    environment:
      PROBE_HOST: "mysql"
      PROBE_PORT: "3306"
      PROBE_TIMEOUT: 60

  mysql:
    image: mysql:5.7
    command:
      - --character-set-server=utf8
      - --collation-server=utf8_general_ci
    ports:
      - "3306"
    environment:
      MYSQL_ROOT_PASSWORD: ${MYSQL_ROOT_PASSWORD}
      MYSQL_DATABASE: ${MYSQL_DATABASE}
      MYSQL_USER: ${MYSQL_USER}
      MYSQL_PASSWORD: "${MYSQL_PASSWORD}
```

**Makefile**
```bash
.PHONY: run-mysql
run-mysql:
	docker-compose run --rm mysql-agent
	@echo "Mysql container is up and running"
```