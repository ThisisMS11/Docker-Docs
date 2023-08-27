## To pull a image from dockerHub

```
docker pull image_name
```

## to know about docker images installed on pc

```
docker images
```

## to know about all the docker containers which are either running or stopped .

```
docker ps -a
```

## to know about the running docker containers

```
docker ps
```

## docker run vs docker start

docker run is to create a new container and docker start is to stop and restart the container

## To run a program (if not already present will pull first and then start)

```
docker run redis
```

## To stop a program

```
docker stop process_id
```

## To stop all the running containers 
```
docker stop $(docker ps -aq)
``` 

## To remove all the containers 
```
docker rm $(docker ps -aq)
``` 

## To Bind different versions of a image to listen to the same port

> you cannot make the two versions listen to the same host port like for below example you can
> _Ex: redis , redis 4.0_

6000 and 6001 are computer hosts

---

```
docker run -p6001:6379 redis
```

```
docker run -p6000:6379 redis4.0
```

Here the containers are listening to request on the same port but are bound to different ports at the host.

## To know about image logs

```
docker logs image_name or id
```

![](1.png)

## If we do not give any name to our container then it will be allocated some random name so give some good name by ourselves we do the following

here running in detached mode

```
docker run -d -p6003:6379 --name your_name image_name(like redis or something)
```

# to get into the image and checkout different files present in the image

```
docker exec -it container_id /bin/bash
```

![](3.png)

# Work flow with Docker

![](4.png)

# Docker

## To see the default docker networks

```
docker network ls
```

## To create your own docker container

```
docker network create network_name
```

## to run the docker image and connect it to the network

![](6.png)

> to see what's happening inside the container just log the image like
> just do docker logs process_id

## To run mongo-express to our server

![](7.png)

## connecting node js app with mongo

![](8.png)


# Creating Docker containers 
suppose you install some docker image let's say of **mongodb** and you now want to use that docker image to create a docker container for using it in your project so for that you can execute the following command : 
```
docker run -d -p 27017:27017 -e MONGO_INITDB_ROOT_USERNAME=admin -e MONGO_INITDB_ROOT_PASSWORD=password --name mongodb --net mongo-network mongo
```
>username and password are credentials that you are configuring to authorize connections to your mongodb containers .

Now If you want to use mongo-express to get a ui interface to interact with the previously created mongodb container image then you can must install mongo-express image and then create a container to use it in practice.
```
docker run -d -p 8081:8081 -e ME_CONFIG_MONGODB_ADMINUSERNAME=admin -e ME_CONFIG_MONGODB_ADMINPASSWORD=password --net mongo-network --name mongo-express -e ME_CONFIG_MONGODB_SERVER=mongodb mongo-express
```
* Note that for you to connect mongo-express and mongodb containers they must be in the same network which in the above example is **mongo-network**

* Same credentials have to be given (makes sense right :)

## Using MongoDB Compass 
If you don't want to use mongo-express as your UI interface then alternatively you can also use MongoDB compass where in the connection string you just have to give the following thing : 
```
mongodb://admin:password@localhost:27017/
```
and this will connect you to that specific mongodb container instance that is running of your system.



## Some important Points to Remember : 
1. When you create a docker image container then that container is like an isolated environment which will uphold your data and other configurations seperately , It is like a copy of mongodb .

2. when a container is stopped using **docker stop  container_id** , the container data will still persist and when you will restart that container , the data will be there . 

3. When you completely delete a container instance then all the data that you had stored using that container instance will be lost because a container is a like your project version of some dependency that you need to work with.


## Using Docker-Compose file
When you are actually using docker in your project you will never want to go to the terminal and hit those lengthy commands every time ,so for that issue docker-compose file comes to your rescue in which you can predefine the configuration ,port Numbers, services, networks and environment for all the docker dependencies that you require in  your project.

### Sample docker-compose.yml for Mongo
```
version: '3'
services:
  mongodb:
    image: mongo
    container_name: mongodb
    ports:
      - "27017:27017"
    environment:
      MONGO_INITDB_ROOT_USERNAME: admin
      MONGO_INITDB_ROOT_PASSWORD: password
volumes:
  mongodb_data:
```

you can define as many services in the same docker compose file as you like below :
```
version: '3'
services:
  mongodb:
    image: mongo
    container_name: mongodb
    ports:
      - "27017:27017"
    environment:
      MONGO_INITDB_ROOT_USERNAME: admin
      MONGO_INITDB_ROOT_PASSWORD: password
    networks:
      - mongo-network

  mongo-express:
    image: mongo-express
    container_name: mongo-express
    ports:
      - "8081:8081"
    environment:
      ME_CONFIG_MONGODB_ADMINUSERNAME: admin
      ME_CONFIG_MONGODB_ADMINPASSWORD: password
      ME_CONFIG_MONGODB_SERVER: mongodb
    networks:
      - mongo-network

networks:
  mongo-network:

```

## Some Points About docker-compose files 
1. To run the docker-compose file
```
docker-compose up
```
2. A docker compose when started for the first time will serach for docker in your system if not found it will thrown an error ***docker-compose command not found*** .
And if you have docker installed then it will search for the required images if found then well and good it will create and start the containers based on the images but in case not found it will pull those images from the docker default registry (docker Hub) and then once being fully installed it will do the same.

3. when you will return to work on your project after a restart or development server quit , then you again will have to run the docker-compose up command to restart the containers that you had created initially .

4. If you want to recreate the containers forcefully then you can use the following command : 
```
docker-compose up --force-recreate
```
Even though you have recreated your container it will still persist your data.

>Ever Delete your container if You want to retain your data .



