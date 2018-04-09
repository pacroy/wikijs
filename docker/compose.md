<!-- TITLE: Docker Compose -->
<!-- SUBTITLE: My reference about Docker Compose -->

# Docker Compose Basics
## Why
- configure relationships between containers
- save our docker container run settings in easy-to-read file
- create one-liner developer environment startups
## Components
Comprised of 2 separate but related things
1. YAML-formatted file that describes our solution options for:
  - containers
  - networks
  - volumes
2. A CLI tool docker-compose used for local dev/test automation with those YAML files
## docker-compose.yml
- Compose YAML format has it's own versions: 1, 2, 2.1, 3, 3.1
- YAML file can be used with `docker-compose` command for local docker automation or..
- With `docker` directly in production with Swarm (as of v1.13)
- `docker-compose --help`
- `docker-compose.yml` is default filename, but any can be used with `docker-compose -f`

## Template
```yml
version: '3.1'  # if no version is specificed then v1 is assumed. Recommend v2 minimum

services:  # containers. same as docker run
  servicename: # a friendly name. this is also DNS name inside network
    image: # Optional if you use build:
    command: # Optional, replace the default CMD specified by the image
    environment: # Optional, same as -e in docker run
    volumes: # Optional, same as -v in docker run
  servicename2:

volumes: # Optional, same as docker volume create

networks: # Optional, same as docker network create

```
## Example 1
This create a Jekyll service.
```yml
version: '2'

# same as 
# docker run -p 80:4000 -v $(pwd):/site bretfisher/jekyll-serve

services:
  jekyll:
    image: bretfisher/jekyll-serve
    volumes:
      - .:/site
    ports:
      - '80:4000'

```
## Example 2
This creates a wordpress service.
```yml
version: '2'

services:

  wordpress:
    image: wordpress
    ports:
      - 8080:80
    environment:
      WORDPRESS_DB_HOST: mysql
      WORDPRESS_DB_NAME: wordpress
      WORDPRESS_DB_USER: example
      WORDPRESS_DB_PASSWORD: examplePW
    volumes:
      - ./wordpress-data:/var/www/html

  mysql:
    image: mariadb
    environment:
      MYSQL_ROOT_PASSWORD: examplerootPW
      MYSQL_DATABASE: wordpress
      MYSQL_USER: example
      MYSQL_PASSWORD: examplePW
    volumes:
      - mysql-data:/var/lib/mysql

volumes:
  mysql-data:
	
```
## Useful Links
- [docker-compose.yml sample for Ghost (A web log) with load-balanced MySQL](https://github.com/BretFisher/udemy-docker-mastery/blob/master/compose-sample-1/compose-3.yml)
- [Compose file version 3 reference](https://docs.docker.com/compose/compose-file/)