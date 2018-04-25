# Dockerized Ghost with Crisp Theme

Everything needed to build [Ghost](https://github.com/docker-library/ghost) with [Crisp](https://github.com/kathyqian/crisp) theme. 

## GHOST_URL Environmental variable

Set ```GHOST_URL``` to wherever you are actually deploying your ghost instance. See example below.

## Example ghost.yml compose file

```
version : '2.0'

services:
  nginx-proxy:
    image: jwilder/nginx-proxy
    ports:
      - 443:443
      - 80:80
    environment:
      - MAX_UPLOAD_SIZE=200000000M
    volumes:
      - /var/run/docker.sock:/tmp/docker.sock:ro
    restart: always

  ghost:
    image: talmai/docker-ghost-crisp
    depends_on:
      - nginx-proxy
    restart: always
    expose:
      - 2368
    environment:
      database__client: mysql
      database__connection__host: your_mysql_host
      database__connection__user: your_mysql_user
      database__connection__password: your_mysql_password
      database__connection__database: ghost
      VIRTUAL_HOST: www.your.site.for.nginx.proxy.parsing
      VIRTUAL_PROTO: http
      NODE_ENV: production
      GHOST_URL: www.your.site
    volumes:
      - /your_image_upload_path:/var/lib/ghost/content/images
```

## Suggested run steps

```
docker pull talmai/docker-ghost-crisp
docker-compose -f ghost.yml create
docker-compose -f ghost.yml start
```