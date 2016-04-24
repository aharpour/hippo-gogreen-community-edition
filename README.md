# Usage
## without docker compose
```bash
docker network create --driver bridge hippo-demo-network

docker run --name hippo-demo-mysql \
    --net hippo-demo-network \
    -e MYSQL_ROOT_PASSWORD=rootPassword \
    -e MYSQL_DATABASE=hippo \
    -e MYSQL_USER=hippo \
    -e MYSQL_PASSWORD=hippoPassword \
    -d mysql:5.5

docker run -d -p 8585:8080 --name hippo-demo-node1 \
    --net hippo-demo-network \
    -e DB_HOST=hippo-demo-mysql \
    -e DB_PORT=3306 -e DB_NAME=hippo \
    -e DB_USER=hippo \
    -e DB_PASS=hippoPassword \
    openweb/gogreen
```
## with docker compose

Create a file called docker-compose.yml with the following content.
```yml
version: '2'

services:
  hippo:
    image: openweb/gogreen
    networks:
      - app_network
    volumes:
      - hippo_repository:/usr/local/repository/
      - hippo_logs:/usr/local/tomcat/logs
    environment:
      DB_HOST: "database"
      DB_PORT: "3306"
      DB_NAME: "hippo"
      DB_USER: "hippo"
      DB_PASS: "hippoPassword"
    depends_on:
      - mysql
    ports:
      - "8585:8080"
    restart: always
  mysql:
    image: mysql:5.5
    volumes:
      - mysql_data:/var/lib/mysql
    environment:
      MYSQL_ROOT_PASSWORD: "rootPassword"
      MYSQL_DATABASE: "hippo"
      MYSQL_USER: "hippo"
      MYSQL_PASSWORD: "hippoPassword"
    networks:
      app_network:
        aliases:
          - database
    restart: always
volumes:
  mysql_data:
    driver: local
  hippo_repository:
    driver: local
  hippo_logs:
    driver: local
networks:
  app_network:
    driver: bridge
```
Then start the containers with running the following command
```bash
docker-compose up -d
```
