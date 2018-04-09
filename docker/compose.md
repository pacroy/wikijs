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