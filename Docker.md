## Docker

### 1. What is docker?

Docker is a way to package applicaitons with all dependencies and configurations



### 2. Docker command

#### Images

image name convention: domain/image:Nametag

```shell
# List docker images
docker image ls

# create a new a docker instance, make sure the name is not used
docker run  --name <name> <image> 

# run a docker in background , detached
docker run -d <image>

# Pull the image and run
docker run <image[:tag]>

# port binding, and name
docker run -p<hostPort>:<containerPort> \
     --name <yourownName> <image> \
     --net <network> \
     -e  <name=value> \ # environmental variables 
     
# remove docker image
docker rmi <dockerImageId>

```

#### Conainters

```shell
# list containers
docker ps

#list containers running or not running
docker ps -a

# start container
docker start <containerId|name>
docker stop <containerId|name>

# login into container
docker exec -it <dockerId|dockerName> /bin/bash

# Remove docker
docker rm <containerId>


```



### Docker Compose

To organize docker contains in a structural way; docker-compose takes care of the creation of common network.

compose.yaml

```yaml
version: '3' # docker-compose version
services:
	mongodb:  # container name
		image: mongodb # docker image name
		ports:
		 	-27071:27071  # port mapping host:container
	  environment:
    	-MONGODB_USER=admin
    volumes:
     - db-data:/var/lib/mysql/data
volumes:
  - db-data:
      driver: local
    	
```



command

```shell
docker-compose -f compose.yaml up|down
```



DockerFile



```shell
FROM <image>
RUN <any linux command>
ENV  MONGODB_USER=admin
COPY . /home/app  # copy files from host to container
CMD ["echo","'hello'"]  # entry point command
```



Build docker image

```shell
docker build -t <tag_name> <dockerimageOutDir>
docker tag my-app:1.0 domain/my-app:1.0
docker push domain/my-app:1.0
```



### Volume

Docker volume is directory on host *** mounted ** into  docker virtual file system.

```
docker run -v <hostDirectory>:<dockerDirectory>

# named volume, the default location on host is: /var/lib/docker/volumes
# the directory pattern is: <docker_name>_<volume_name>
docker run -v <name>:<dockerDirectory>	
```

