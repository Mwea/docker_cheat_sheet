# Docker cheat sheet

## Installation 

Docker installation 

```bash
curl -fsSL get.docker.com -o get-docker.sh
sudo sh get-docker.sh
```

Setting up proxy 

```bash
touch /etc/systemd/system/docker.service.d/http-proxy.conf
echo /etc/systemd/system/docker.service.d/http-proxy.conf >> "[Service]
Environment=\"HTTP_PROXY=http://proxy.univ-lyon1.fr:3128/\""
sudo systemctl daemon-reload
sudo systemctl restart docker
```

## Images

Search

```bash
docker search nginx
```

Pulling image

```bash
docker pull image:tag
```

List images

```bash
docker images
```

## Networks 


Create a network

> **Bridge** networks are **isolated networks on a single Engine** installation. If you want to create a network 
> that **spans multiple Docker hosts each running an Engine**, you must create an **overlay** network. 

```bash
docker create network --subnet 172.18.100.0/24 my_network
```

List networks 

```bash
docker network ls
```

Remove a network 

```bash
docker network rm my_network
```

## Running containers

Running an (detached `-d`) image 

```bash
docker run -d image:tag
```

Set a name for a docker

```bash
docker run --name myname image:tag
```

Set a hostname for a docker

```bash
docker run --hostname myname image:tag
```

Bind port with a container 

```bash
docker run -p hostPort:containerPort image:tag
```

Bind a volume with a container 

```bash
docker run -v HOST_ABSOLUTE_PATH:CONTAINER_ABSOLUTE_PATH image:tag 
```

Set an IP and a network for a container 

```bash
docker run --network=mynetwork --ip=IP_ADDRESS image:tag  
```
 
Add host to container 
```bash
docker run --add-host OTHER_CONTAINER_NAME:OTHER_IP image:tp
```
 
Run a bash inside a container 

```bash
docker exec -it containrName bash
```

## Containers manipulation 

List containers 

```bash
docker ps -a 
```

Remove a container 

```bash
docker stop containerID
docker rm containerID 
```

Remove all running containers

```bash
docker stop $(docker ps -a -q)
docker rm $(docker ps -a -q)
```

Copy files from a docker
 
```bash
docker cp container_name:CONTAINER_ABSOLUTE_PATH HOST_ABSOLUTE_PATH
```

## Dockerfile

Build an image with a given name 

```bash
docker build -t given_name:my_version ABSOLUTE_PATH_TO_DOCKERFILE
```

Dockerfile example

```Dockerfile
FROM ubuntu:vivid
MAINTAINER Fabien Rico<fabien.rico@univ-lyon1.fr>
RUN echo "\
export http_proxy=http://proxy.univ-lyon1.fr:3128;\
" >> /etc/bash.bashrc
# ? is replaced with any single character, e.g., "home.txt"
ADD hom?.txt /mydir/  
RUN apt-get update && apt-get -y install apache2 && apt-get clean
WORKDIR /var/www/html
EXPOSE 80
ENV APACHE_RUN_USER www-data
ENV APACHE_RUN_GROUP www-data
ENV APACHE_LOG_DIR /var/log/apache2
CMD ["/usr/sbin/apache2ctl", "-D", "FOREGROUND"]
```

## Docker compose 

Deploy a docker compose
```bash
docker compose up
```

Docker compose example 

```yaml
version: '2.1'

networks:
  rescomp:
    driver: bridge
    ipam:
      driver: default
      config:
      - subnet: 172.20.20.0/24

services:
     service_name_1:
        build: PATH_TO_A_DOCKERFILE
        volumes:
            - /home/ubuntu/compose/apache/html:/var/www/html
        networks:
            rescomp:
                ipv4_address: 172.20.20.10
    service_name_2:
        image: nginx
        volumes:
            - HOST_PATH:CONTAINER_PATH
        ports:
            - HOST_PORT:CONTAINER_PORT
        extra_hosts:
            - "chary:172.20.20.11"
            - "bousquet:172.20.20.10"
        environment:
            - HTTP_PROXY=http://proxy.univ-lyon1.fr:3128
        networks:
            rescomp:
                ipv4_address: IP_ADDRESS
```

## Docker swarm 

Initialize docker swarm 

```bash
docker swarm init --advertise-addr 192.168.99.121
```

Join a cluster swarm

```bash
docker swarm join \
    --token TOKENPRINTED \
    192.168.XXX.YYY:2377
```

Get a new token 

```bash
docker swarm join-token worker
```

List nodes
 
```bash
docker node ls
```

Remove a node properly 

```bash
docker node update --availability drain node-1
docker rm node-1
``` 

Pause a node
```bash
docker node update --availability pause node-1
```

Re-active a node 
```bash
docker node update --availability active node-1
```

Create a service ( options are the same as docker run )

```bash
docker service create --name Web --publish 80:80 --replicas=3 nginx:latest
```
List services

```bash
docker service ls
```

Scale a service
 
```bash
docker service scale Web=1
```
Remove a service 

```bash
docker rm Web
```

## Docker machine

Install Docker machine

```bash
curl -L https://github.com/docker/machine/releases/download/v0.13.0/docker-machine-`uname -s`-`uname -m` > docker-machine \
&& chmod +x ./docker-machine
```

Export user password to environment 

```bash
export OS_PASSWORD=$(python3 -c "import getpass; pa=getpass.getpass('Password : '); print (pa)")
```

Create an instance on openstack

```bash
docker-machine create \
 --driver openstack \
 --openstack-auth-url https://cloud-info.univ-lyon1.fr:5000/v3/  \
 --openstack-tenant-id 0172faff8a6c4c63ad7095af44d86415 \
  --openstack-image-name "Ubuntu 16.04.3 LTS Xenial" \
  --openstack-username "YOUR_USERNAME" \
  --engine-env HTTP_PROXY="http://proxy.univ-lyon1.fr:3128"  \
  --engine-env http_proxy="http://proxy.univ-lyon1.fr:3128"  \
  --engine-env https_proxy="http://proxy.univ-lyon1.fr:3128"  \
  --engine-env HTTPS_PROXY="http://proxy.univ-lyon1.fr:3128"  \
  --openstack-flavor-name "m1.xsmall"  \
  --openstack-domain-name "UNIV-LYON1.fr" \
   MACHINE_NAME
```

Run commands on docker-machine node 

```bash

```

Instantiate a local registry

```bash

```

Push a local image to local registry

```bash

```

Join a Swarm from docker machine
```bash

```

## Docker stack 

Docker stack example 

```bash

```

Deploy a stack 

```bash

```

Create a secret 
```bash

```