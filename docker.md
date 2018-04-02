<!-- TITLE: Docker -->
<!-- SUBTITLE: Docker command references -->

# Basics

| Command | Description |
|---|---|
| `docker version` | Display docker version of client and server |
| `docker info` | Display various  docker information and configuration |
| `docker` | Display all available commands |

## New Management Command Line

```
docker <command> <sub-command> (options)
```

| Command | Description |
|---|---|
| `docker container run --publish 80:80 nginx` | Create a NGINX container in the foreground |
| `docker container run --publish 80:80 --detach nginx` | Create a NGINX container in the background |
| `docker container ls` | List all active containers |
| `docker container stop 7e4` | Stop a container 7e4 |
| `docker container ls -a`| List all containers (active and inactive) |
| `docker container run --publish 80:80 --detach --name webhost nginx` | Create a NGINX container with given name webhost |
| `docker container logs webhost` | Show logs of the container webhost |
| `docker container top webhost` | Display running processes of the container webhost |
| `docker container --help` | Display all container’s sub-commands |
| `docker container rm 860 74e 957` | Delete container 860, 74e, and 957 (non-active containers only) |
| `docker container rm -f 860` | Forced delete container 860 even it is running |

## What’s Happened?

1. Look for image locally
2. Look for image in remote repository (Docker Hub is the default repo)
3. Download the latest version by default
4. Create a new container and prepare to start
5. Give a virtual IP on a private network inside docker engine
6. Open port 80 on host and forward to port 80 in container
7. Start container by using the CMD in Dockerfile

## Old Command Line

| Command | Description |
|---|---|
| `docker run  --name mongo -d mongo` | Create a new mongo DB container named mongo |
| `docker ps` | List all active containers |
| `docker top mongo`| Display running processes of the container mongo |
| `docker stop mongo` | Stop the container mongo |
| `docker start mongo` | Start the container mongo |

## Manage Multiple Containers

```
docker container run -d -p 3306:3306 --name db -e MYSQL_RANDOM_ROOT_PASSWORD=true mysql
```

Create a MySQL container 

- in detach/background (-d) mode
- publish port 3306 on host to 3306 on container
- assign name db
- set environment variable MYSQL_RANDOM_ROOT_PASSWORD to true to generate root password and print out in the log

| Command | Description |
|---|---|
| `docker container run -d --name webserver -p 8080:80 httpd` | Create a new HTTPd (Apache) container |
| `docker container run -d --name proxy -p 80:80 nginx` | Create a new NGINX container |
| `docker container inspect mysql` | See information on how the container mysql started in JSON format |
| `docker container stats` | See live stream of container statistics |

# Shell Inside Containers

```
docker container run -it --name proxy nginx bash
```

Create a new NGINX container named proxy and run bash command right after and keep terminal opened (-it.)  
*Container will stop after exit

## Ubuntu Container

```
docker container run -it --name ubuntu ubuntu
```

Try more commands:

```
apt-get update  
apt-get install -y curl  
curl google.com  
```

| Command | Description |
|---|---|
| `docker container start -ai ubuntu` | Start the container ubuntu and open shell |
| `docker container exec -it mysql bash` | Create a new process and run bash inside container mysql |
| `docker pull alpine` | Pull the image alpine |
| `docker image ls` | List all local images |
| `docker container run -it alpine sh` | Create a new alpine container and open sh (bash is not available in alpine) |
| `docker container run --rm -it centos:7` | Create CentOS v7 container and open shell. Container is removed after exit. |
| `docker container run --rm -it ubuntu` | Create Ubuntu latest version and open shell. Container is removed after exit. |

# Docker Networking

## Default Network

- Each container connected to a private virtual network ‘bridge’ or ‘docker0’
- Each virtual network routes through NAT firewall on host IP i.e. containers can goes out to the internet via the host machine
- All containers can talk to each other without -p
- Best practice is to create a new virtual network for each app (which may containing multiple containers)
- “Batteries includes, but removable”

| Command | Description |
|---|---|
| `docker container run -p 80:80 --name webhost -d nginx` | Create a new NGINX container named webhost and map port 80—>80 |
| `docker container port webhost` | See port mapping for container webhost |
| `docker container inspect --format '{{ .NetworkSettings.IPAddress }}' webhost` | See IP address of the container webhost |
| `ifconfig en0` |  See IP address of local machine |
| `docker network ls` | List all networks attached to Docker |
| `docker network inspect bridge` | See which containers attach to the network bridge |

## Creating a New Virtual Network

| Command | Description |
|---|---|
| `docker network create my_app_net` | Create a new bridge network named my_app_net |
| `docker container run -d --name new_nginx --network my_app_net nginx` | Create a new container in the specified networt my_app_net |
| `docker network connect webhost my_app_net` | Connect container webhost to network my_app_net |
| `docker container inspect webhost` | See which network the container webhost connect to |
| `docker network disconnect my_app_net webhost` | Disconnect container webhost from network my_app_net |

## DNS

Because containers are always moving, come and go, all the times. Should not rely on IPs but names.

| Command | Description |
|---|---|
| `docker container run -d --name new_nginx --network my_app_net nginx:alpine` | Create NGINX container named new_nginx and attach my_app_net |
| `docker container run -d --name my_nginx --network my_app_net nginx:alpine` | Create NGINX container named my_nginx and attach my_app_net |
| `docker network inspect my_app_net` | See both containers are attached to my_app_net |
| `docker container exec -it my_nginx ping new_nginx` | Ping new_nginx from my_nginx |
| `docker container exec -it new_nginx ping my_nginx` | Ping my_nginx from new_nginx |

## DNS Round Robin

| Command | Description |
|---|---|
| `docker network create dude` | Create a new network named dude |
| `docker container run -d --network dude --net-alias search elasticsearch:2` | Create a new Elasticsearch container and attach to network dude with alias search |
| `docker container run -d --network dude --net-alias search elasticsearch:2` | Create another Elasticsearch container and attach to network dude with alias search |
| `docker container run --rm --network dude alpine nslookup search` | See NS entries mapped to DNS search |
| `docker container run --rm --network dude centos:7 curl -s search:9200` | Get Elastic search result from DNS search |

# Docker Image

![](uploads/docker/what-is-docker-image.jpg)


Visit [Docker Hub](https://hub.docker.com/)

Try to select image with more pulls and stars

| Command | Description |
|---|---|
| `docker pull centos` | Download the latest (default) version of CentOS image |
| `docker pull nginx:1.11` | Download the version 1.11 of NGINX image |
| `docker image ls` | See all downloaded images |

## Official Images
- [https://hub.docker.com/explore/](https://hub.docker.com/explore/)
- [https://github.com/docker-library/official-images/tree/master/library](https://github.com/docker-library/official-images/tree/master/library)

## Image Layer

- Docker always stores only one copy of an image layer.
- Image is checked via SHA hash for identical validation.
- When running a container, Docker create a R/W layer on top of an image 

| Command | Description |
|---|---|
| `docker history nginx:latest` | See history of image nginx:’latest  |
| `docker image inspect nginx` | Display metadata of the image nginx e.g. exposed port, available environment variable, commands executed when a container is created |

## Image Tagging and Pushing 

- Image can be uniquely referred by `<user>/<image>:<tag>`
- `<user>` is omitted for official images
- Tag is a label to image ID. One image ID can have many tags

| Command | Description |
|---|---|
| `docker image tag nginx pacroy/nginx` | Create a new tag |
| `docker image push pacroy/nginx` | Upload tag to Docker Hub
| `cat .docker/config.json` | See local Docker config |
| `docker image tag pacroy/nginx pacroy/nginx:testing` | Create another tag |
| `docker image push pacroy/nginx:testing` | Upload tag which layers already exist |

# Dockerfile

A recipe to create your own image.

## Basic

| Command | Description |
|---|---|
| `FROM` | Base image |
| `WORKDIR` | Works like cd |
| `ENV` | Key-value of environment variables |
| `RUN` | Commands to execute when building image *Use && to add more commands to make sure changes are put on the same layer |
| `EXPOSE` | Ports to expose |
| `CMD` | Commands to execute when running/starting container |
| `COPY` | Copy file from host into image |

## Docker Build

When rebuilding the image, only image layer that changed are rebuilt.

| Command | Description |
|---|---|
| `docker image build -t customnginx .` | Build a new image tag customnginx from Dockerfile in current directory |
| `docker image build -t nginx-with-html .` | Build a new image tag nginx-with-html from Dockerfile in current directory |
| `docker container run -p 80:80 --rm nginx-with-html` | Run a container of the new image tag |
| `docker image tag nginx-with-html:latest pacroy/nginx-with-html:latest` | Re-tagging by creating a new tag |

## Dockerfile Exercise

```dockerfile
# Instructions from the app developer
# - you should use the 'node' official image, with the alpine 6.x branch
FROM node:6-alpine

# - this app listens on port 3000, but the container should launch on port 80
#  so it will respond to http://localhost:80 on your computer
EXPOSE 3000

# - then it should use alpine package manager to install tini: 'apk add --update tini'
# - then it should create directory /usr/src/app for app files with 'mkdir -p /usr/src/app'
RUN apk add --update tini && \
    mkdir -p /usr/src/app

WORKDIR /usr/src/app

# - Node uses a "package manager", so it needs to copy in package.json file
COPY package.json package.json

# - then it needs to run 'npm install' to install dependencies from that file
# - to keep it clean and small, run 'npm cache clean --force' after above
RUN npm install && npm cache clean --force

# - then it needs to copy in all files from current directory
COPY . .

# - then it needs to start container with command 'tini -- node ./bin/www'
# - in the end you should be using FROM, RUN, WORKDIR, COPY, EXPOSE, and CMD commands
CMD [ "tini", "--", "node", "./bin/www" ]
```

# Data Volume

![](uploads/docker/container-life-persistent.jpg)

## VOLUME in Dockerfile

- Tells docker to create a new volume and mount at the specified location
- Can also be inspected (docker image inspect mysql)

| Command | Description |
|---|---|
| `docker container run -d --name mysql -e MYSEL_ALLOW_EMPTY_PASSWORD=true mysql` | Create a new MySQL container |
| `docker volume ls` | List all created volumes |
| `docker container inspect mysql` | See which volume is mounted |

## Named Volume

| Command | Description |
|---|---|
| `docker container run -d --name mysql -e MYSEL_ALLOW_EMPTY_PASSWORD=true -v mysql-db:/var/lib/mysql mysql` | Create a new MySQL container with named volume |
| `docker container run -d --name mysql2 -e MYSEL_ALLOW_EMPTY_PASSWORD=true -v mysql-db:/var/lib/mysql mysql` | Create another MySQL container using the same volume |
| `docker volume create test-volume` | Create a new volume named test-volume |

## Bind Mounting

- Two locations pointing to the same thing
- Can’t specified in Dockerfile
- Must be specified at `docker run`

| Command | Description |
|---|---|
| `docker container run -d --name nginx -p 80:80 -v $(pwd):/usr/share/nginx/html nginx` | Create a new NGINX container with bind mount from current directory to /usr/share/nginx/html in the container |
| `docker run -p 80:4000 -v $(pwd):/site bretfisher/jekyll-serve` | Create a new Jekyll container with bind mount from current directory to /site |

# Docker Compose

![Docker Compose](/uploads/docker/docker-compose.png "Docker Compose")

## Sample YAML File

```yaml
--- !clarkevans.com/^invoice
invoice: 34843
date   : 2001-01-23
bill-to: &id001
    given  : Chris
    family : Dumars
    address:
        lines: |
            458 Walkman Dr.
            Suite #292
        city    : Royal Oak
        state   : MI
        postal  : 48046
ship-to: *id001
product:
    - sku         : BL394D
      quantity    : 4
      description : Basketball
      price       : 450.00
    - sku         : BL4438H
      quantity    : 1
      description : Super Hoop
      price       : 2392.00
tax  : 251.42
total: 4443.52
comments: >
    Late afternoon is best.
    Backup contact is Nancy
    Billsmer @ 338-4338.
```
Source: http://www.yaml.org/start.html

## YAML Reference Card

```yaml
%YAML 1.1   # Reference card
---
Collection indicators:
    '? ' : Key indicator.
    ': ' : Value indicator.
    '- ' : Nested series entry indicator.
    ', ' : Separate in-line branch entries.
    '[]' : Surround in-line series branch.
    '{}' : Surround in-line keyed branch.
Scalar indicators:
    '''' : Surround in-line unescaped scalar ('' escaped ').
    '"'  : Surround in-line escaped scalar (see escape codes below).
    '|'  : Block scalar indicator.
    '>'  : Folded scalar indicator.
    '-'  : Strip chomp modifier ('|-' or '>-').
    '+'  : Keep chomp modifier ('|+' or '>+').
    1-9  : Explicit indentation modifier ('|1' or '>2').
           # Modifiers can be combined ('|2-', '>+1').
Alias indicators:
    '&'  : Anchor property.
    '*'  : Alias indicator.
Tag property: # Usually unspecified.
    none    : Unspecified tag (automatically resolved by application).
    '!'     : Non-specific tag (by default, "!!map"/"!!seq"/"!!str").
    '!foo'  : Primary (by convention, means a local "!foo" tag).
    '!!foo' : Secondary (by convention, means "tag:yaml.org,2002:foo").
    '!h!foo': Requires "%TAG !h! <prefix>" (and then means "<prefix>foo").
    '!<foo>': Verbatim tag (always means "foo").
Document indicators:
    '%'  : Directive indicator.
    '---': Document header.
    '...': Document terminator.
Misc indicators:
    ' #' : Throwaway comment indicator.
    '`@' : Both reserved for future use.
Special keys:
    '='  : Default "value" mapping key.
    '<<' : Merge keys from another mapping.
Core types: # Default automatic tags.
    '!!map' : { Hash table, dictionary, mapping }
    '!!seq' : { List, array, tuple, vector, sequence }
    '!!str' : Unicode string
More types:
    '!!set' : { cherries, plums, apples }
    '!!omap': [ one: 1, two: 2 ]
Language Independent Scalar types:
    { ~, null }              : Null (no value).
    [ 1234, 0x4D2, 02333 ]   : [ Decimal int, Hexadecimal int, Octal int ]
    [ 1_230.15, 12.3015e+02 ]: [ Fixed float, Exponential float ]
    [ .inf, -.Inf, .NAN ]    : [ Infinity (float), Negative, Not a number ]
    { Y, true, Yes, ON  }    : Boolean true
    { n, FALSE, No, off }    : Boolean false
    ? !!binary >
        R0lG...BADS=
    : >-
        Base 64 binary value.
Escape codes:
 Numeric   : { "\x12": 8-bit, "\u1234": 16-bit, "\U00102030": 32-bit }
 Protective: { "\\": '\', "\"": '"', "\ ": ' ', "\<TAB>": TAB }
 C         : { "\0": NUL, "\a": BEL, "\b": BS, "\f": FF, "\n": LF, "\r": CR,
               "\t": TAB, "\v": VTAB }
 Additional: { "\e": ESC, "\_": NBSP, "\N": NEL, "\L": LS, "\P": PS }
...
```
Source: http://www.yaml.org/refcard.html

## Docker Compose YAML Template

```yaml
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

| Notation | Description |
|---|---|
| `version: '3.1'` | if no version is specificed then v1 is assumed. Recommend v2 minimum |
| `services:` | containers. same as docker run |
| `  servicename:` | a friendly name. this is also DNS name inside network. like --name in docker run |
| `    image:` | Optional if you use build: |
| `    command:` | Optional, replace the default CMD specified by the image |
| `    environment:` | Optional, same as -e in docker run |
| `    volumes:` | Optional, same as -v in docker run |
| `volumes:` | Optional, same as docker volume create |
| `networks:` | Optional, same as docker network create |

## Sample Docker Compose YAML

```yaml
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

# Useful Commands

| Command | Description | Reference |
|---|---|---|
| `uname -a` | Print linux kernel version | [whatsmyos.com](http://whatsmyos.com/) |
| `lsb_release -a` | Print linux distro and version | [askubuntu.com](https://askubuntu.com/questions/686239/how-do-i-check-the-version-of-ubuntu-i-am-running) |
| `cat /etc/*release` | Print linux distro and version | [whatsmyos.com](http://whatsmyos.com/) |
| `docker ps -a --filter volume=VOLUME_NAME_OR_MOUNT_POINT` | Determine what containers use the docker volume |
| `openssl req -x509 -newkey rsa:2048 -keyout key.pem -out cert.pem -days 30000 -nodes` | Generate self-signed certificates | [krishnasrinivas/wetty](https://hub.docker.com/r/krishnasrinivas/wetty/) |
| `ssh-keygen -t rsa -b 4096 -C “your_comment_or_email”` | Generate SSH key | [Connecting to GitHub with SSH](https://medium.com/pacroy/connecting-to-github-with-ssh-f54248ccf30d) |

# Useful Links

- [How to Set Up Command Aliases in Linux/Ubuntu/Debian](http://www.hostingadvice.com/how-to/set-command-aliases-linuxubuntudebian/)