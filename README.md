# Dockerized Ghost with Crisp Theme

[![Docker Pulls](https://img.shields.io/docker/pulls/talmai/docker-ghost-crisp.svg)](https://hub.docker.com/r/talmai/docker-ghost-crisp/)
[![Docker Stars](https://img.shields.io/docker/stars/talmai/docker-ghost-crisp.svg)](https://hub.docker.com/r/talmai/docker-ghost-crisp/)

Everything needed to build [Ghost](https://github.com/docker-library/ghost) with [Crisp](https://github.com/kathyqian/crisp) theme.

## GHOST_URL Environmental variable

Set ```GHOST_URL``` to wherever you are actually deploying your ghost instance. See example below.

## POST_PER_PAGE Environmental variable

Set ```POST_PER_PAGE``` to the number of posts visible in the main page. See example below.

## Mounting your follow.hbs file for personalized menu

Crisp allows for a nice menu for different social services.

```
<div id="follow-icons">
  <a href="http://facebook.com/username"><i class="fa fa-facebook-square fa-2x"></i></a>
  <a href="http://twitter.com/username"><i class="fa fa-twitter-square fa-2x"></i></a>
  <a href="http://linkedin.com/in/username"><i class="fa fa-linkedin-square fa-2x"></i></a>
  <a href="mailto:you@example.com"><i class="fa fa-envelope-square fa-2x"></i></a>
  <a href="{{@blog.url}}/rss"><i class="fa fa-rss-square fa-2x"></i></a> 
  <a href="http://github.com/username"><i class="fa fa-github-square fa-2x"></i></a>
  <a href="http://plus.google.com/+username"><i class="fa fa-google-plus-square fa-2x"></i></a>
  <a href="http://instagram.com/username"><i class="fa fa-instagram fa-2x"></i></a>
  <a href="http://vimeo.com/username"><i class="fa fa-vimeo-square fa-2x"></i></a>
  <a href="http://youtube.com/username"><i class="fa fa-youtube-square fa-2x"></i></a>
  <a href="http://flickr.com/username"><i class="fa fa-flickr fa-2x"></i></a>
  <a href="http://pinterest.com/username"><i class="fa fa-pinterest-square fa-2x"></i></a>
  <a href="http://username.tumblr.com"><i class="fa fa-tumblr-square fa-2x"></i></a>
</div>                                                                  
```

You can, in fact, change this by mounting your own file. See example below.

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
      POST_PER_PAGE: 15
    volumes:
      - /your_image_upload_path:/var/lib/ghost/content/images
      - /your_follow_hbs_file:/var/lib/ghost/content/themes/crisp/partials/follow.hbs
```

In practice, I've noticed that whenever I change some of the environmental variables, docker refuses to correctly rebuild the image. Therefore...

### Suggested run steps

```
docker pull talmai/docker-ghost-crisp
docker-compose -f ghost.yml stop ## if running
docker-compose -f ghost.yml rm ghost ## if container existed in the past
docker-compose -f ghost.yml create --force-recreate
docker-compose -f ghost.yml start
```
