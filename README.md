# ghost-docker-arm64v8
Docker-compose file for Ghost CMS

Ghost
Ghost is a free and open source blogging platform written in JavaScript and distributed under the MIT License, designed to simplify the process of online publishing for individual bloggers as well as online publications.

Example stack.yml for ghost:

version: '2'

services:

  ghost:
    build:
      context: ../..
      dockerfile: app/Docker/Dockerfile.release
    image: arm64v8/ghost:latest
    restart: always
    ports:
      - 3001:2368

    volumes:
      - ./content:/var/lib/ghost/content
    environment:
      # see https://ghost.org/docs/config/#configuration-options
      database__client: mysql
      database__connection__host: db
      database__connection__user: root_name
      database__connection__password: secure_pasword
      database__connection__database: ghost
      # this url value is just an example, and is likely wrong for your environment!
      url: https://yourdomain.com
      # contrary to the default mentioned in the linked documentation, this image defaults to NODE_ENV=production (so development mode needs to be explicitly specified if desired)
      #NODE_ENV: development
    labels:
      - traefik.http.routers.blog.rule=Host(`blog.your_domain`)
      - traefik.http.routers.blog.tls=true
      - traefik.http.routers.blog.tls.certresolver=lets-encrypt
      - traefik.port=80
    networks:
      - internal
      - web

  db:
    build:
      context: ../..
      dockerfile: app/Docker/Dockerfile.release
    image: arm64v8/mariadb:latest
    restart: always
    environment:
      # always have a strong password
      MYSQL_ROOT_USER: root_name
      MYSQL_ROOT_PASSWORD: secure_password
    volumes:
     - mysql-data:/var/lib/mysql
     
volumes:
  mysql-data:
