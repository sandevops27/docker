# Installing Docker on Amazon Linux server

### Pre-requisites
1. Amazon Linux EC2 Instance

## Installation Steps

1. Install docker and start docker services
   ```sh 
   yum install docker -y
   docker --version 
   
   # start docker services
   service docker start
   service docker status
   ```
1. Create a user called dockeradmin
   ```sh
   useradd dockeradmin
   passwd dockeradmin
   ```
1. add a user to docker group to manage docker 
   ```
   usermod -aG docker dockeradmin
   ```
### Validation test
1. Create a tomcat docker container by pulling a docker image from the public docker registry
   ```sh
   docker run -d --name test-tomcat-server -p 8090:8080 tomcat:latest
   ```

## Docker Installation on CentOS server
##### Referance URL : https://docs.docker.com/install/linux/docker-ce/centos/#install-using-the-repository
### Pre-requisites

Please follow below steps to install docker CE on CentoOS server instance. For RedHat only Docker EE available 

1. Install the required packages.

   ```sh 
   sudo yum install -y yum-utils \
   device-mapper-persistent-data \
   lvm2
   ```
  
1. Use the following command to set up the stable repository.
 
   ```sh 
   sudo yum-config-manager \
   --add-repo \
   https://download.docker.com/linux/centos/docker-ce.repo
   ```

### INSTALLING DOCKER CE

1. Install the latest version of Docker CE.
   ```sh 
   sudo yum install docker-ce
   ```

   Note: If prompted to accept the GPG key, verify that the fingerprint matches 
060A 61C5 1B55 8A7F 742B 77AA C52F EB6B 621E 9F35, and if so, accept it.

1. Start Docker.
   ```sh 
   sudo systemctl start docker
   ```

1. Verify that docker is installed correctly by running the hello-world image.
   ```sh
   sudo docker run hello-world
   ```
   
###  DOCKER COMMANDS
  
1. how to search a docker image in hub.docker.com
```sh
docker search httpd
```
2. Download a docker image from hub.docker.com
```sh
docker image pull <image_name>:<image_version/tag>
```

3. List out docker images from your local system
```sh
docker image ls
```

4. Create/run/start a docker container from image
```sh
docker run -d --name <container_Name> <image_name>:<image_version/tag>

d - run your container in back ground (detached)
```

5. Expose your application to host server
```sh
docker run -d  -p <host_port>:<container_port> --name <container_Name> <image_name>:<Image_version/tag>

docker run -d --name httpd_server -p 8080:80 httpd:2.2
```

6. List out running containers
```sh
docker ps
```

7. List out all docker container (running, stpooed, terminated, etc...)
```sh
docker ps -a
```

8. run a OS based container which interactive mode (nothing but login to container after it is up and running)

```sh
docker run -i -t --name centos_server centos:latest
i - interactive
t - Terminal
```

9. Stop a container 
```sh
docker stop <container_id>
```

10. Start a container which is in stopped or exit state

```sh
docker start <container_id>
```
11. Remove a container

```sh
docker rm <container_id>
```

12. login to a docker container
```sh
docker exec -it <container_Name> /bin/bash
```



## Dockerfile

1. A Dockerfile is a text document that contains all the commands a user could call on the command line to assemble an image.   
2. The docker build command builds an image from a Dockerfile and a context.  
 ```sh 
  docker build .
  docker build -f /path/to/a/Dockerfile .
  docker build -t shykes/myapp .
 ```

3. The Docker daemon runs the instructions in the Dockerfile one-by-one, committing the result of each instruction to a new image if necessary, before finally outputting the ID of your new image.  
4. Whenever possible, Docker uses a build-cache to accelerate the docker build process significantly.  
5. When you’re done with your build, you’re ready to look into scanning your image with docker scan  
 ```sh
 docker scan hello-world
 
 ```

## Dockerfile Creation  

 Docker file format
  ```sh
   # Comment
   INSTRUCTION arguments
  ```

1. A Dockerfile must begin with a FROM instruction.  

2. Docker distributes official versions of the images that can be used for building Dockerfiles under docker/dockerfile repository on Docker Hub.

- `FROM` - A valid Dockerfile must start with a FROM instruction.

- `RUN` - The RUN instruction will execute any commands in a new layer on top of the current image and commit the results. The resulting committed image will be used for the next step in the Dockerfile.

- `CMD` - The main purpose of a CMD is to provide defaults for an executing container. 

   `Note:` There can only be one CMD instruction in a Dockerfile. If you list more than one CMD then only the last CMD will take effect.

   `Note` - Do not confuse RUN with CMD. RUN actually runs a command and commits the result; CMD does not execute anything at build time, but specifies the intended command for the image.

- `LABEL` -The LABEL instruction adds metadata to an image. A LABEL is a key-value pair. T

- `EXPOSE`
  1. The EXPOSE instruction informs Docker that the container listens on the specified network ports at runtime. 
  2. To set up port redirection on the host system, see using the -P flag.
  3. The docker network command supports creating networks for communication among containers without the need to expose or publish specific ports, because the containers connected to the network can communicate with each other over any port. 

- `ENV` - The ENV instruction sets the environment variable <key> to the value <value>.

- `ADD` - The ADD instruction copies new files, directories or remote file URLs from <src> and adds them to the filesystem of the image at the path <dest>.

    If <src> is a local tar archive in a recognized compression format (identity, gzip, bzip2 or xz) then it is unpacked as a directory. 

- `COPY` - The COPY instruction copies new files or directories from <src> and adds them to the filesystem of the container at the path <dest>.

- `ENTRYPOINT` - An ENTRYPOINT allows you to configure a container that will run as an executable.

  1. ENTRYPOINT will override all elements specified using CMD
  2. Both CMD and ENTRYPOINT instructions define what command gets executed when running a container. There are few rules that describe their co-operation.

  3. Dockerfile should specify at least one of CMD or ENTRYPOINT commands.

  4. ENTRYPOINT should be defined when using the container as an executable.

  5. CMD should be used as a way of defining default arguments for an ENTRYPOINT command or for executing an ad-hoc command in a container.

  6. CMD will be overridden when running the container with alternative arguments.

- `VOLUME` - The VOLUME instruction creates a mount point with the specified name and marks it as holding externally mounted volumes from native host or other containers. 

- `USER` - The USER instruction sets the user name to use when running the image
- `WORKDIR` - The WORKDIR instruction sets the working directory for any RUN, CMD, ENTRYPOINT, COPY and ADD instructions

- `ARG` - The ARG instruction defines a variable that users can pass at build-time to the builder with the docker build command using the --build-arg <varname>=<value> flag. 

- `SHELL` - The SHELL instruction allows the default shell used for the shell form of commands to be overridden. 
   
   
   
   
## Tomcat File Creation 

   
```sh 
FROM centos
RUN yum install java -y
RUN mkdir /opt/tomcat/
WORKDIR /opt/tomcat
ADD https://dlcdn.apache.org/tomcat/tomcat-9/v9.0.54/bin/apache-tomcat-9.0.54.tar.gz /opt/tomcat
RUN tar xvfz apache*.tar.gz
RUN mv apache-tomcat-9.0.54/* /opt/tomcat 
EXPOSE 8080
CMD ["/opt/tomcat/bin/catalina.sh", "run"]
```

