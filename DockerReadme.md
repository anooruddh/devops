DOCKER - Personal Tutorial


Docker Swarm - Raghav Pal
https://www.youtube.com/watch?v=bU2NNFJ-UXA&t=539s

	docker run -d -p 5000:5000 –name registry registry:2 is all it takes. 

organization/image:tag name, which is a shortcut for docker.io/organization/image:tag
When a private registry is used (likely hosted at example.com), images need to be named explicitly: example.com:port/namespace/image:tag.

docker data stored in /var/lib/docker
	  -volumes
	  -swarm
	  -runtimes
	  -plugins
	  -network
	  -images
	  -container
	  -overlay2
	  -containerd


DOCKER

Docker is an open-source lightweight virtualization tool. It is containerizing platform in which user can run and deploy application and its dependencies and form containers to run over any linux infrastructure.


Maintainer — This command is used to give the information about the author or manager who is managing this image.

Advantages

Advantage of using Docker

	1) Application that runs on docker require lesser size.

	2) Better utilization cpu.

	3) Short boot-up process.

	4) Docker can be integrated with other tools.


https://www.youtube.com/watch?v=_vHTaIJm9uY&list=PLF3s2WICJlqOiymMaTLjwwHz-MSVbtJPQ

	Install required packages. yum-utils 

	sudo yum install -y yum-utils

	Install Docker CE

	sudo yum install docker-ce

	yum list docker-ce --showduplicates | sort -r

	sudo yum install docker-ce-<VERSION STRING>

	sudo docker run hello-world

	sudo yum remove docker-ce

	sudo service docker restart

	docker container stop <Container NAME or ID>
-------------------------------------

## List Docker CLI commands

docker

		docker container --help


## Display Docker version and info

		docker --version

		docker version

		docker info

## Execute Docker image

		docker run hello-world

## List Docker images

		docker image ls

## List Docker containers (running, all, all in quiet mode)

		docker container ls

		docker container ls --all

		docker container ls -aq

Log in with your Docker ID

If you don’t have a Docker account, sign up for one at hub.docker.com. Make note of your username.

Log in to the Docker public registry on your local machine.

		$ docker login
----------------------------------
		docker image ls
----------------------------------

## Adding insecure registries

If you want to connect to a private docker registry that is either
* not configured for https
* or configured for https using self-signed certificates

then add these configuration to **/etc/docker/daemon.json**
```
{
  "insecure-registries": ["<ip>:<port>"]
}
```
And restart docker service
```
sudo systemctl restart docker
```


Publish the image

Upload your tagged image to the repository:

		docker tag 244914b4ac0a webserver:v1
		docker push webserver:v1

		docker tag webserver:v1 webserver:v1

		docker push sendtoanoo/webserver:v1

		docker push username/repository:tag

		docker build -t web:v1

docker rmi -f 244914b4ac0a
[root@ip-172-31-24-94 ag1]# docker rmi -f 244914b4ac0a
		Untagged: web:v1
		Untagged: webserver:v1
		Deleted: sha256:244914b4ac0a2f12488d2dc9fab69a8024c79bbd55126dfd8018e4a5c291bc51
		Deleted: sha256:31305a802bf1b0c271b2a3be2d5138a61252dc3391671a5232def7235a7c1638
		Deleted: sha256:754637a2ab9ba3fe0d9993094c58340456cb89ba3780ca35c351d4e1b43478f8
		Deleted: sha256:e217655a1e456f8d883ff3ead20f54a274c65bab2ecd69fedb7f97eab1c7ec15
		Deleted: sha256:4d285fd0e10b0dc63041ed0902099995ae0ee6b6e580205b70833c6169043434


[root@ip-172-31-24-94 ag1]# docker history sendtoanoo/web1:v1
IMAGE               CREATED              CREATED BY                                      SIZE                COMMENT
		e22879a4fcf4        55 seconds ago       /bin/sh -c #(nop)  EXPOSE 80                    0B
		2b3077f22610        55 seconds ago       /bin/sh -c #(nop)  CMD ["/usr/sbin/httpd" "-…   0B
		baf9702b2797        55 seconds ago       /bin/sh -c #(nop) COPY file:777daed709f64bdd…   201B
		81883c37d778        56 seconds ago       /bin/sh -c yum install -y httpd                 132MB
		c626e03f491a        About a minute ago   /bin/sh -c #(nop)  MAINTAINER Anooruddh_Gajb…   0B
		75835a67d134        4 days ago           /bin/sh -c #(nop)  CMD ["/bin/bash"]            0B
		<missing>           4 days ago           /bin/sh -c #(nop)  LABEL org.label-schema.sc…   0B
		<missing>           4 days ago           /bin/sh -c #(nop) ADD file:fbe9badfd2790f074…   200MB
[root@ip-172-31-24-94 ag1]#




-------------------------------------

List all exited containers

		docker ps -aq -f status=exited

Remove stopped containers

		docker ps -aq --no-trunc -f status=exited | xargs docker rm

----------
go in docker container

		docker exec -it MyFirstContainer bash
		docker exec -t -i container_name /bin/bash

===================================

Environment variables are supported by the following list of instructions in the Dockerfile:

    ADD
    COPY
    ENV
    EXPOSE
    FROM
    LABEL
    STOPSIGNAL
    USER
    VOLUME
    WORKDIR

as well as:

    ONBUILD (when combined with one of the supported instructions above)
----------------------

docker build - < Dockerfile

=======================================================================================
	[root@ip-172-31-32-101 ec2-user]# cat Dockerfile
	FROM ubuntu
	MAINTAINER sendtoanoo@gmail.com
	CMD ["echo", "Hello from Dockerfile"]


	[root@ip-172-31-32-101 ec2-user]# sudo docker build -t="myUbuntuImage" .
	invalid argument "myUbuntuImage" for "-t, --tag" flag: invalid reference format: repository name must be lowercase
	See 'docker build --help'.
	[root@ip-172-31-32-101 ec2-user]# sudo docker build -t="myubuntuimage" .
	Sending build context to Docker daemon  580.3MB
	Step 1/3 : FROM ubuntu
	 ---> 16508e5c265d
	Step 2/3 : MAINTAINER sendtoanoo@gmail.com
	 ---> Running in bb19b840ab8b
	Removing intermediate container bb19b840ab8b
	 ---> b4d6ead9ed18
	Step 3/3 : CMD ["echo", "Hello from Dockerfile"]
	 ---> Running in 38d071e80372
	Removing intermediate container 38d071e80372
	 ---> 469c838b5e76
	Successfully built 469c838b5e76
	Successfully tagged myubuntuimage:latest
	[root@ip-172-31-32-101 ec2-user]# docker ps -a

	[root@ip-172-31-32-101 ec2-user]# docker images
	REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
	myubuntuimage       latest              469c838b5e76        40 seconds ago      84.1MB
	<none>              <none>              cb0ce27f743f        4 hours ago         643MB
	<none>              <none>              531059125c7d        4 hours ago         643MB
	ubuntu              latest              16508e5c265d        4 days ago          84.1MB
	centos              latest              5182e96772bf        2 weeks ago         200MB
	busybox             latest              e1ddd7948a1c        3 weeks ago         1.16MB
	hello-world         latest              2cb0d9787c4d        6 weeks ago         1.85kB
	java                8                   d23bdf5b1b1b        19 months ago       643MB


=============================================================
access the page from my web browser installed in my PC (which is out of the Virtual Box, the one that is installed in my Windows)

Seems there's a better answer available since Docker-Tools was introduced a few months ago. If you're not using Docker Tools, take a look at https://docs.docker.com/machine

If you are using Docker Tools,

    find the docker machine name with $ docker-machine ls
    find the docker IP address with $ docker-machine ip <machine-name>
    use the ip address to connect, e.g. $ curl 192.168.99.100


=============================================================	
Install tomcat on docker container

docker pull tomcat
docker run -it --rm -p 8888:8080 tomcat:8.0


=================================================
Let’s try to create an interactive shell inside a Docker container:

docker run -i -t --rm ubuntu /bin/bash

    -t flag assigns a pseudo-tty or terminal inside the new container.
    -i flag allows you to make an interactive connection by grabbing the standard input (STDIN) of the container.
    — rm flag automatically removes the container when the process exits. By default, containers are not deleted. This container exists until we keep the shell session and terminates when we exit the session (like an SSH session with a remote server).
	
=================================================
docker run -t --name MyImage

docker run --name agjenkins4 -v ag1volume:/var/jenkins_home -p 8282:8282 jenkins


Create a Hello.sh file
#!/bin/sh
echo Hi Anooruddh, This is from container


FROM ubuntu:latest
RUN mkdir /home/ag1
RUN chmod 777 /home/ag1
COPY . /home/ag1
CMD ["/home/ag1/Hello.sh"] # CMD /home/ag1/Hello.sh

docker build -t hello-anooruddh .
docker image 
docker tag <image_id> hello-anooruddh:v3

Repository - hello-anooruddh, TAG-v3


docker push sendtoanoo/hello-anooruddh:v3

docker create -t -i hello-anooruddh bash

go inside container

docker run -t -i image_name bash

A swarm is a group of machines that are running Docker and joined into a cluster. 
After that has happened, you continue to run the Docker commands you’re used to, but now they are executed on a cluster by a swarm manager. 
The machines in a swarm can be physical or virtual. 
After joining a swarm, they are referred to as nodes.

A swarm is made up of multiple nodes, which can be either physical or virtual machines. 
The basic concept is simple enough: run docker swarm init to enable swarm mode and make your current machine a swarm manager, 
then run docker swarm join on other machines to have them join the swarm as workers. Choose a tab below to see how this plays out in various contexts. 
We use VMs to quickly create a two-machine cluster and turn it into a swarm.

-----------------------------------------
run

Use this to run a command in a new container. It suits the situation where you do not have a container running, and you want to create one, start it and then run a process on it.

docker run [OPTIONS] IMAGE [COMMAND] [ARG...]

exec

This is for when you want to run a command in an existing container. This is better if you already have a container running and want to change it or obtain something from it. For example, if you are using docker-compose you will probably spin-up multiple containers and you may want to access one or more of them once they are created.

docker exec [OPTIONS] CONTAINER COMMAND [ARG...]
===========================

4. Dockerfile Commands

	    ADD
	    CMD
	    ENTRYPOINT
	    ENV
	    EXPOSE
	    FROM
	    MAINTAINER
	    RUN
	    USER
	    VOLUME
	    WORKDIR

	What is a Dockerfile?

    A Dockerfile is a text configuration file written in a popular, human-readable Markup Language called YAML.
    It is a step-by-step script of all the commands you need to run to assemble a Docker Image.
    The docker build command processes this file generating a Docker Image in your Local Image Cache, which you can then start-up using the docker run command, or push to a permanent Image Repository.
	
	
	Dockerfile Commands

    ADD – Defines files to copy from the Host file system onto the Container
		
        	ADD ./local/config.file /etc/service/config.file

    CMD – This is the command that will run when the Container starts
		
        	CMD ["nginx", "-g", "daemon off;"]
		
    ENTRYPOINT – Sets the default application used every time a Container is created from the Image. If used in conjunction with CMD, you can remove the application and just define the arguments there
		
		CMD Hello World!
		ENTRYPOINT echo
		
    ENV – Set/modify the environment variables within Containers created from the Image.
		
        	ENV VERSION 1.0
		
    EXPOSE – Define which Container ports to expose
		
        	EXPOSE 80
    FROM – Select the base image to build the new image on top of
		
        	FROM ubuntu:latest
		
    MAINTAINER – Optional field to let you identify yourself as the maintainer of this image
		
        	MAINTAINER Some One "someone@xyz.xyz"
		
    RUN – Specify commands to make changes to your Image and subsequently the Containers started from this Image. This includes updating packages, installing software, adding users, creating an initial database, setting up certificates, etc. These are the commands you would run at the command line to install and configure your application
		
        	RUN apt-get update && apt-get upgrade -y && apt-get install -y nginx && rm -rf /var/lib/apt/lists/*
		
    USER – Define the default User all commands will be run as within any Container created from your Image. It can be either a UID or username
		
        	USER docker
		
    VOLUME – Creates a mount point within the Container linking it back to file systems accessible by the Docker Host. New Volumes get populated with the pre-existing contents of the specified location in the image. It is specially relevant to mention is that defining Volumes in a Dockerfile can lead to issues. Volumes should be managed with docker-compose or “docker run” commands.
		
        	VOLUME /var/log
		
    WORKDIR – Define the default working directory for the command defined in the “ENTRYPOINT” or “CMD” instructions
	
	
		docker-compose -f docker-compose.json up
	
==============================================================================================================================
[root@ip-172-31-24-94 ag1]# docker build -t sendtoanoo/web1:v1 .
Sending build context to Docker daemon    129kB
Step 1/6 : FROM centos:latest
 ---> 75835a67d134
Step 2/6 : MAINTAINER Anooruddh_Gajbhiye
 ---> Running in d37a1d7b7882
Removing intermediate container d37a1d7b7882
 ---> c626e03f491a
Step 3/6 : RUN yum install -y httpd
 ---> Running in d0abb0579c67
Loaded plugins: fastestmirror, ovl
Determining fastest mirrors
 * base: mirrors.fibergrid.in
 * extras: mirrors.fibergrid.in
 * updates: mirrors.fibergrid.in
Resolving Dependencies
--> Running transaction check
---> Package httpd.x86_64 0:2.4.6-80.el7.centos.1 will be installed
--> Processing Dependency: httpd-tools = 2.4.6-80.el7.centos.1 for package: httpd-2.4.6-80.el7.centos.1.x86_64
--> Processing Dependency: system-logos >= 7.92.1-1 for package: httpd-2.4.6-80.el7.centos.1.x86_64
--> Processing Dependency: /etc/mime.types for package: httpd-2.4.6-80.el7.centos.1.x86_64
--> Processing Dependency: libaprutil-1.so.0()(64bit) for package: httpd-2.4.6-80.el7.centos.1.x86_64
--> Processing Dependency: libapr-1.so.0()(64bit) for package: httpd-2.4.6-80.el7.centos.1.x86_64
--> Running transaction check
---> Package apr.x86_64 0:1.4.8-3.el7_4.1 will be installed
---> Package apr-util.x86_64 0:1.5.2-6.el7 will be installed
---> Package centos-logos.noarch 0:70.0.6-3.el7.centos will be installed
---> Package httpd-tools.x86_64 0:2.4.6-80.el7.centos.1 will be installed
---> Package mailcap.noarch 0:2.1.41-2.el7 will be installed
--> Finished Dependency Resolution

Dependencies Resolved

================================================================================
 Package           Arch        Version                       Repository    Size
================================================================================
Installing:
 httpd             x86_64      2.4.6-80.el7.centos.1         updates      2.7 M
Installing for dependencies:
 apr               x86_64      1.4.8-3.el7_4.1               base         103 k
 apr-util          x86_64      1.5.2-6.el7                   base          92 k
 centos-logos      noarch      70.0.6-3.el7.centos           base          21 M
 httpd-tools       x86_64      2.4.6-80.el7.centos.1         updates       90 k
 mailcap           noarch      2.1.41-2.el7                  base          31 k

Transaction Summary
================================================================================
Install  1 Package (+5 Dependent packages)

Total download size: 24 M
Installed size: 31 M
Downloading packages:
warning: /var/cache/yum/x86_64/7/base/packages/apr-util-1.5.2-6.el7.x86_64.rpm: Header V3 RSA/SHA256 Signature, key ID f4a80eb5: NOKEY
Public key for apr-util-1.5.2-6.el7.x86_64.rpm is not installed
Public key for httpd-tools-2.4.6-80.el7.centos.1.x86_64.rpm is not installed
--------------------------------------------------------------------------------
Total                                               15 MB/s |  24 MB  00:01
Retrieving key from file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
Importing GPG key 0xF4A80EB5:
 Userid     : "CentOS-7 Key (CentOS 7 Official Signing Key) <security@centos.org>"
 Fingerprint: 6341 ab27 53d7 8a78 a7c2 7bb1 24c6 a8a7 f4a8 0eb5
 Package    : centos-release-7-5.1804.4.el7.centos.x86_64 (@Updates)
 From       : /etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
  Installing : apr-1.4.8-3.el7_4.1.x86_64                                   1/6
  Installing : apr-util-1.5.2-6.el7.x86_64                                  2/6
  Installing : httpd-tools-2.4.6-80.el7.centos.1.x86_64                     3/6
  Installing : centos-logos-70.0.6-3.el7.centos.noarch                      4/6
  Installing : mailcap-2.1.41-2.el7.noarch                                  5/6
  Installing : httpd-2.4.6-80.el7.centos.1.x86_64                           6/6
  Verifying  : mailcap-2.1.41-2.el7.noarch                                  1/6
  Verifying  : httpd-tools-2.4.6-80.el7.centos.1.x86_64                     2/6
  Verifying  : apr-util-1.5.2-6.el7.x86_64                                  3/6
  Verifying  : httpd-2.4.6-80.el7.centos.1.x86_64                           4/6
  Verifying  : apr-1.4.8-3.el7_4.1.x86_64                                   5/6
  Verifying  : centos-logos-70.0.6-3.el7.centos.noarch                      6/6

Installed:
  httpd.x86_64 0:2.4.6-80.el7.centos.1

Dependency Installed:
  apr.x86_64 0:1.4.8-3.el7_4.1
  apr-util.x86_64 0:1.5.2-6.el7
  centos-logos.noarch 0:70.0.6-3.el7.centos
  httpd-tools.x86_64 0:2.4.6-80.el7.centos.1
  mailcap.noarch 0:2.1.41-2.el7

Complete!
Removing intermediate container d0abb0579c67
 ---> 81883c37d778
Step 4/6 : COPY docker.html /var/www/html/
 ---> baf9702b2797
Step 5/6 : CMD ["/usr/sbin/httpd","-D","FOREGROUND"]
 ---> Running in 173a218a483e
Removing intermediate container 173a218a483e
 ---> 2b3077f22610
Step 6/6 : EXPOSE 80
 ---> Running in a2c1ff7bb4c0
Removing intermediate container a2c1ff7bb4c0
 ---> e22879a4fcf4
Successfully built e22879a4fcf4
Successfully tagged sendtoanoo/web1:v1
[root@ip-172-31-24-94 ag1]# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
sendtoanoo/web1     v1                  e22879a4fcf4        4 seconds ago       332MB
centos              latest              75835a67d134        4 days ago          200MB
docker              latest              fd7e073eb60f        8 days ago          152MB
hello-world         latest              4ab4c602aa5e        5 weeks ago         1.84kB
[root@ip-172-31-24-94 ag1]# docker history sendtoanoo/web1
Error response from daemon: No such image: sendtoanoo/web1:latest
[root@ip-172-31-24-94 ag1]# docker history sendtoanoo/web1:v1
IMAGE               CREATED              CREATED BY                                      SIZE                COMMENT
e22879a4fcf4        55 seconds ago       /bin/sh -c #(nop)  EXPOSE 80                    0B
2b3077f22610        55 seconds ago       /bin/sh -c #(nop)  CMD ["/usr/sbin/httpd" "-…   0B
baf9702b2797        55 seconds ago       /bin/sh -c #(nop) COPY file:777daed709f64bdd…   201B
81883c37d778        56 seconds ago       /bin/sh -c yum install -y httpd                 132MB
c626e03f491a        About a minute ago   /bin/sh -c #(nop)  MAINTAINER Anooruddh_Gajb…   0B
75835a67d134        4 days ago           /bin/sh -c #(nop)  CMD ["/bin/bash"]            0B
<missing>           4 days ago           /bin/sh -c #(nop)  LABEL org.label-schema.sc…   0B
<missing>           4 days ago           /bin/sh -c #(nop) ADD file:fbe9badfd2790f074…   200MB
[root@ip-172-31-24-94 ag1]# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
sendtoanoo/web1     v1                  e22879a4fcf4        2 minutes ago       332MB
centos              latest              75835a67d134        4 days ago          200MB
docker              latest              fd7e073eb60f        8 days ago          152MB
hello-world         latest              4ab4c602aa5e        5 weeks ago         1.84kB
[root@ip-172-31-24-94 ag1]# docker push sendtoanoo/web1:v1
The push refers to repository [docker.io/sendtoanoo/web1]
1c881e15c66f: Pushed
cae11e721c46: Pushed
f972d139738d: Pushed
v1: digest: sha256:3bce787eca6028ab0a912d12cee93da9e79a6e07854fc3785cb642bdc003ee87 size: 948
[root@ip-172-31-24-94 ag1]#

	[root@ip-172-31-24-94 ag1]# docker images
	REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
	sendtoanoo/web1     v1                  e22879a4fcf4        6 minutes ago       332MB
	centos              latest              75835a67d134        4 days ago          200MB
	docker              latest              fd7e073eb60f        8 days ago          152MB
	hello-world         latest              4ab4c602aa5e        5 weeks ago         1.84kB

	[root@ip-172-31-24-94 ag1]# docker run -dit -p 1234:80 sendtoanoo/web1:v1
	b00998c116899e8f24676291a11363f3997ecc7241c01dc838d488ee1ec0ebcd
	[root@ip-172-31-24-94 ag1]# docker ps
	CONTAINER ID        IMAGE                COMMAND                  CREATED             STATUS              PORTS                  NAMES
	b00998c11689        sendtoanoo/web1:v1   "/usr/sbin/httpd -D …"   6 seconds ago       Up 5 seconds        0.0.0.0:1234->80/tcp   sad_booth


==========================================================================================================================	
[root@ip-172-31-24-94 /]# curl http://172.31.24.94:1234/docker.html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Learn Docker at Tecmint.com</title>
</head>
<body>
    <h1>This is Anooruddh, Learn Docker With Us</h1>
</body>
</html>
[root@ip-172-31-24-94 /]# curl http://172.31.24.94:1234/docker.html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Learn Docker at Tecmint.com</title>
</head>
<body>
    <h1>This is Anooruddh, Learn Docker With Us</h1>
</body>
</html>
[root@ip-172-31-24-94 /]#

----------------------------------------------------------
base=https://github.com/docker/machine/releases/download/v0.14.0 &&
  curl -L $base/docker-machine-$(uname -s)-$(uname -m) >/tmp/docker-machine &&
  sudo install /tmp/docker-machine /usr/local/bin/docker-machine
  
	
# RUN vs CMD vs ENTRYPOINT	
	
### RUN. Mainly used to build images and install applications and packages. Builds a new layer over an existing image by committing the results.
	
### CMD. CMD defines default commands and/or parameters for a container. CMD is an instruction that is best to use if you need a default command which users can easily override. If a Dockerfile has multiple CMDs, it only applies the instructions from the last one.
	
### ENTRYPOINT. Default parameters that cannot be overridden when Docker Containers run with CLI parameters.ENTRYPOINT is preferred when you want to define a container with a specific executable

# Docker CMD - An essential feature of a CMD command is its ability to be overridden. This allows users to execute commands through the CLI to override CMD instructions within a Dockerfile.
	
		FROM centos:7
		RUN    apt-get update
		RUN     apt-get -y install python
		COPY ./opt/source code
		CMD ["echo", "Hello, Darwin"]		
	
		$ docker build -t Darwin .
	
		$ docker run Darwin
		OUTPUT
	 	Hello, Darwin
	
		$ docker run Darwin hostname
		OUTPUT
		6e14beead430		(As a CMD default command gets overridden, the above command will run the container and display the hostname, thereby ignoring the echo instruction in the Dockerfile)

# When to use CMD
	
The best way to use a CMD instruction is by specifying default programs that should run when users do not input arguments in the command line.

This instruction ensures the container is in a running state by starting an application as soon as the container image is run. By doing so, the CMD argument loads the base image as soon as the container starts.

Additionally, in specific use-cases, a docker run command can be executed through a CLI to override instructions specified within the Dockerfile.	

# Docker ENTRYPOINT
	
In Dockerfiles, an ENTRYPOINT instruction is used to set executables that will always run when the container is initiated. Unlike CMD commands, ENTRYPOINT commands cannot be ignored or overridden—even when the container runs with command line arguments stated.
	
		FROM centos:7
		RUN    apt-get update
		RUN     apt-get -y install python
		COPY ./opt/source code
		ENTRYPOINT ["echo", "Hello, Darwin"]
	
		$ docker build -t Darwin .
		$ docker run Darwin hostname
		OUTPUT
		Hello, Darwin 6e14beead430	(will execute the ENTRYPOINT, echoing Hello, Darwin then displaying the hostname to return the output)

# When to use ENTRYPOINT
	
ENTRYPOINT instructions are suitable for both single-purpose and multi-mode images where there is a need for a specific command to always run when the container starts.

One of its popular use-cases is building wrapper containers-images that encapsulate legacy programs for containerization, which leverages an ENTRYPOINT instruction to ensure the program will always run.	
	

Combine ENTRYPOINT with CMD if you need a container with a specified executable and a default parameter that can be modified easily. For example, when containerizing an application use ENTRYPOINT and CMD to set environment-specific variables.	

# How to Override Entrypoint Using Docker Run	(--entrypoint flag)

		FROM ubuntu
		MAINTAINER sofija
		RUN apt-get update
		ENTRYPOINT [“echo”, “Hello”]
		CMD [“World”]		
	
		$ docker run container_name
		OUTPUT
		Hello World
	
		OVERRIDE CMD
	
		$ docker run container_name ANOORUDDH
		OUTPUT
		Hello ANOORUDDH
		
		OVERRIDE ENTRYPOINT
		docker run --entrypoint [new_command] [docker_image] [optional:value]
		sudo docker run -it --entrypoint /bin/bash [docker_image]
		OUTPUT
		Instead of showing you Hello World, You will be see that you are inside docker container 	

Don’t forget that this is only temporary. Once you exit out of the container and run it again using the standard docker run command, it executes the default ENTRYPOINT instruction.	
	
  ======================================================================
  
  docker commands
  
	  docker --version
	  docker --help
	  docker run
	  docker build
	  docker push
	  docker pull
	  docker login
	  docker ps
	  docker images   docker run -it <image_name>
	  docker stop
	  docker start
	  docker service status
	  service docker status
	  docker kill
	  docker rm - remove containers
	  docker rmi - remve imgages
	  docker exec
	  docker import
	  docker commit
	  docker container
	  docker compose
	  docker swarm
	  docker service
	  docker rename <container_name> <container_name>
	  [root@ip-172-31-24-94 ec2-user]# docker run -it sendtoanoo/web1:v1 /bin/bash
	[root@9182d45f77f5 /]#

  
  Why you should not use ADD

The ADD instruction has similar syntax to COPY. As well as copying local files and directories into the destination within the Docker image, it has some

  Although ADD and COPY are functionally similar, generally speaking, COPY is preferred. That’s because it’s more transparent than ADD. COPY only supports the basic copying of local files into the container, while ADD has some features (like local-only tar extraction and remote URL support) that are not immediately obvious. Consequently, the best use for ADD is local tar file auto-extraction into the image, as in ADD rootfs.tar.xz /
  
  
  ADD rootfs.tar.xz /opt/ag1/
  
  One of ADD‘s additional features is that it can copy files from a URL, but Docker advises against using it for this purpose.
  
  Best practices for copying files from a URL

If ADD’s source is a URL, it will download and copy the file into the destination within the Docker image. Docker suggests that it is often not efficient to copy from a URL using ADD, and it is best practice to use other strategies to include the required remote files.

    Because image size matters, using ADD to fetch packages from remote URLs is strongly discouraged; you should use curl or wget instead. That way you can delete the files you no longer need after they’ve been extracted and you don’t have to add another layer in your image.

For example, you should avoid doing things like:

	ADD http://example.com/big.tar.xz /usr/src/things/
	RUN tar -xJf /usr/src/things/big.tar.xz -C /usr/src/things
	RUN make -C /usr/src/things all

	And instead, do something like:

	RUN mkdir -p /usr/src/things \
	    && curl -SL http://example.com/big.tar.xz \
	    | tar -xJC /usr/src/things \
	    && make -C /usr/src/things all

When you could use ADD

If <source> is a local tar archive in a recognized compression format, then it is automatically unpacked as a directory into the Docker image. For example: ADD rootfs.tar.xz /. This is the main recommended use of ADD over COPY in Dockerfiles.

For other items (files, directories) that do not require ADD’s tar auto-extraction capability, you should always use COPY.	
========================================================================================================================

Other Public Registries

Other companies host paid online Docker registries for public use. Cloud providers like AWS and Google, who also offer container-hosting services, market the high availability of their registries.

    Amazon Elastic Container Registry (ECR) integrates with AWS Identity and Access Management (IAM) service for authentication. It supports only private repositories and does not provide automated image building.

	Registry Concepts

    The URL for your default registry is https://aws_account_id.dkr.ecr.region.amazonaws.com.

    By default, you have read and write access to the repositories and images you create in your default registry.

    You must authenticate your Docker client to a registry so that you can use the docker push and docker pull commands to push and pull images to and from the repositories in that registry. For more information, see Registry Authentication.

    Repositories can be controlled with both IAM user access policies and repository policies.

	technically you can use the Amazon ECR API to push and pull images, you are much more likely to use Docker CLI (or a language-specific Docker library). 
	
	----
	Some vendors provide their own extensions of the open source Docker registry. These can help alleviate some of the above operational concerns:

    Docker Trusted Registry is Docker Inc’s commercially supported version, providing high availability via replication, image auditing, signing and security scanning, integration with LDAP and Active Directory.

    Harbor is a VMWare open source offering which also provides high availability via replication, image auditing, integration with LDAP and Active Directory.

    GitLab Container Registry is tightly integrated with GitLab CI’s workflow, with minimal setup.

    JFrog Artifactory for strong artifact management (not only Docker images but any artifact).
	
	docker login ${server-name}-{repo-name}.jfrog.io
	docker tag <image name> ${server-name}-{repo-name}.jfrog.io/<image name>
	docker push ${server-name}-{repo-name}.jfrog.io/<image name>
	docker pull ${server-name}-{repo-name}.jfrog.io/<image name>
	
	docker login acme-dockerv2-virtual.jfrog.io

--------------------------------------------------------------------------------------------
    Google Container Registry (GCR) authentication is based on Google’s Cloud Storage service permissions. It supports only private repositories and provides automated image builds via integration with Google Cloud Source Repositories, GitHub, and Bitbucket.

    Azure Container Registry (ACR) supports multi-region registries and authenticates with Active Directory. It supports only private repositories and does not provide automated image building.

    CoreOS Quay supports OAuth and LDAP authentication. It offers both (paid) private and (free) public repositories, automatic security scanning and automated image builds via integration with GitLab, GitHub, and Bitbucket.
    Private Docker Registry supports OAuth, LDAP and Active Directory authentication. It offers both private and public repositories, free up to 3 repositories (private or public). 

	
	
-------------------------------------------------------------------------------------------------------------------------------------------------------------------
	
	Here is the docker-compose.yml that powers the whole setup.

	version: "3"
	services:
	  web:
	    build: web
	    command: python app.py
	    ports:
	     - "5000:5000"
	    volumes:
	     - ./web:/code # modified here to take into account the new app path
	    links:
	     - redis
	    environment:
	     - DATADOG_HOST=datadog # used by the web app to initialize the Datadog library
	  redis:
	    image: redis
	  # agent section
	  datadog:
	    build: datadog
	    links:
	     - redis # ensures that redis is a host that the container can find
	     - web # ensures that the web app can send metrics
	    environment:
	     - DD_API_KEY=__your_datadog_api_key_here__
	    volumes:
	     - /var/run/docker.sock:/var/run/docker.sock
	     - /proc/:/host/proc/:ro
	     - /sys/fs/cgroup:/host/sys/fs/cgroup:ro


Test
	docker-compose up  ( docker-compose -f docker-compose.json or yml)	 



RUN and CMD are very important pieces to a Dockerfile and they both perform much different tasks. Here's what they do.

RUN and CMD are both Dockerfile instructions.

RUN lets you execute commands inside of your Docker image. These commands get executed once at build time and get written into your Docker image as a new layer.

For example if you wanted to install a package or create a directory inside of your Docker image then RUN will be what you’ll want to use. For example, RUN mkdir -p /path/to/folder.

CMD lets you define a default command to run when your container starts.

You could say that CMD is a Docker run-time operation, meaning it’s not something that gets executed at build time. It happens when you run an image. A running image is called a container.

For example, if you were creating a Dockerfile for your own web application, a reasonable CMD to have would be to start your web application’s app server.

-------------------------------------------------------------------------------------------------------------------------------------------------------------------
A Dockerfile is a simple text file that contains the commands a user could call to assemble an image.

Example, Dockerfile

	FROM ubuntu:latest
	MAINTAINER john doe 

	RUN apt-get update
	RUN apt-get install -y python python-pip wget
	RUN pip install Flask

	ADD hello.py /home/hello.py

	WORKDIR /home
-------------------------------------------------------------------------------------------------------------------------------------------------------------------
Docker Compose

    is a tool for defining and running multi-container Docker applications.

    define the services that make up your app in docker-compose.yml so they can be run together in an isolated environment.

    get an app running in one command by just running docker-compose up

Example, docker-compose.yml

	version: '3'
	services:
	  web:
	    build: .
	    ports:
	    - "5000:5000"
	    volumes:
	    - .:/code
	    - logvolume01:/var/log
	    links:
	    - redis
	  redis:
	    image: redis
	volumes:
	  logvolume01: {}	

-------------------------------------------------------------------------------------------------------------------------------------------------------------------

Difference between COPY and ADD



COPY copies a file/directory from your host to your image.

ADD copies a file/directory from your host to your image, but can also fetch remote URLs, extract TAR files, etc...

Use COPY for simply copying files and/or directories into the build context.

Use ADD for downloading remote resources, extracting TAR files, etc..

-ADD lets you do that too, but it also supports 2 other sources. First, you can use a URL instead of a local file / directory. Secondly, you can extract a tar file from the source directly into the destination

A valid use case for ADD is when you want to extract a local tar file into a specific directory in your Docker image.

If you’re copying in local files to your Docker image, always use COPY because it’s more explicit.


		[root@ansible docker_java]# docker build -t="sendtoanoo/anoo-hello:v1" .
		Sending build context to Docker daemon  2.048kB
		Step 1/3 : FROM ubuntu:latest
		 ---> 1d9c17228a9e
		Step 2/3 : MAINTAINER anooruddh gajbhiye sendtoanoo@gmail.com
		 ---> Running in 22f8bd9b3c09
		Removing intermediate container 22f8bd9b3c09
		 ---> 199bbdd6f3da
		Step 3/3 : CMD ["echo", "Hi Anooruddh Gajbhiye"]
		 ---> Running in 94fb28236d1e
		Removing intermediate container 94fb28236d1e
		 ---> 4709107341ec
		Successfully built 4709107341ec
		Successfully tagged sendtoanoo/anoo-hello:v1
		[root@ansible docker_java]# docker push sendtoanoo/anoo-hello:v1
		The push refers to repository [docker.io/sendtoanoo/anoo-hello]
		2c77720cf318: Pushed
		1f6b6c7dc482: Pushed
		c8dbbe73b68c: Pushed
		2fb7bfc6145d: Pushed
		v1: digest: sha256:88197b5f77af1bb3f44dae15746fa49871f8c48bb3402bb7ebc9ead12e0f4dfe size: 1150
		[root@ansible docker_java]#

-------------------------------------------------------------------------------------------------------------------------------------------------------------------

		docker volume create --name jenkins_data
		docker run --name jenkins -d -v jenkins_data:/var/jenkins_home -p 8080:8080 -p 50000:50000 jenkins:latest
 
 
 
		Ubuntu image directory structure within a container
		root@152410a09186:/# ls -lrt
		total 64
		drwxr-xr-x  2 root root 4096 Apr 24  2018 home
		drwxr-xr-x  2 root root 4096 Apr 24  2018 boot
		drwxr-xr-x  1 root root 4096 Dec  4 17:11 usr
		drwxr-xr-x  2 root root 4096 Dec  4 17:11 srv
		drwxr-xr-x  2 root root 4096 Dec  4 17:11 opt
		drwxr-xr-x  2 root root 4096 Dec  4 17:11 mnt
		drwxr-xr-x  2 root root 4096 Dec  4 17:11 media
		drwxr-xr-x  8 root root 4096 Dec  4 17:11 lib
		drwxr-xr-x  2 root root 4096 Dec  4 17:11 lib64
		drwx------  2 root root 4096 Dec  4 17:12 root
		drwxr-xr-x  1 root root 4096 Dec  4 17:12 var
		drwxr-xr-x  2 root root 4096 Dec  4 17:12 bin
		drwxrwxrwt  2 root root 4096 Dec  4 17:12 tmp
		drwxr-xr-x  1 root root 4096 Dec 28 23:22 sbin
		drwxr-xr-x  1 root root 4096 Dec 28 23:22 run
		dr-xr-xr-x 13 root root    0 Jan 15 05:32 sys
		drwxr-xr-x  1 root root 4096 Jan 15 07:50 etc
		dr-xr-xr-x 96 root root    0 Jan 15 07:50 proc
		drwxr-xr-x  5 root root  360 Jan 15 07:50 dev
		root@152410a09186:/# pwd
		/


-------------------------------------------------------------------------------------------------------------------------------------------------------------------

Getting started with IBM Cloud Container Registry

IBM® Cloud Container Registry provides a multi-tenant private image registry that you can use to safely store and share your Docker images with users in your IBM Cloud account.

Note - Do not put personal information in your container images, namespace names, description fields (for example, in registry tokens), or in any image configuration data (for example, image names or image labels).


Install the IBM Cloud Container Registry CLI

    Install the IBM Cloud CLI External link icon so that you can run the IBM Cloud ibmcloud commands. This installation also installs the plug-ins for IBM Cloud Kubernetes Service and IBM Cloud Container Registry.

Set up a namespace

Log in to IBM Cloud.
		ibmcloud login

Add a namespace to create your own image repository. Replace <my_namespace> with your preferred namespace.
		ibmcloud cr namespace-add <my_namespace>

To ensure that your namespace is created, run the ibmcloud cr namespace-list command.
		ibmcloud cr namespace-list

Pull images from another registry to your local machine

Install the Docker CLI External link icon. For Windows 8, or OS X Yosemite 10.10.x or earlier, install Docker Toolbox External link icon instead. IBM Cloud Container Registry supports Docker Engine v1.12.6, or later.

Download (pull) the image to your local machine. Replace <source_image> with the repository of the image and <tag> with the tag of the image that you want to use, for example, latest.

	docker pull <source_image>:<tag>
Example, where <source_image> is hello-world and <tag> is latest:

	docker pull hello-world:latest

Tag the image. Replace <source_image> with the repository and <tag> with the tag of your local image that you pulled earlier. Replace <region> with the name of your region. Replace <my_namespace> with the namespace that you created in Set up a namespace. Define the repository and tag of the image that you want to use in your namespace by replacing <new_image_repo> and <new_tag>.

	docker tag <source_image>:<tag> registry.<region>.bluemix.net/<my_namespace>/<new_image_repo>:<new_tag>

Example, where <source_image> is hello-world, <tag> is latest, <region> is eu-gb, <my_namespace> is namespace1, <new_image_repo> is hw_repo, and <new_tag> is 1:

docker tag hello-world:latest registry.eu-gb.bluemix.net/namespace1/hw_repo:1

	Push Docker images to your namespace

Run the ibmcloud cr login command to log your local Docker daemon into IBM Cloud Container Registry.

	ibmcloud cr login

Upload (push) the image to your namespace. Replace <my_namespace> with the namespace that you created in Set up a namespace, and <image_repo> and <tag> with the repository and the tag of the image that you chose when you tagged the image.

	docker push registry.<region>.bluemix.net/<my_namespace>/<image_repo>:<tag>

Example, where <region> is eu-gb, <my_namespace> is namespace1, <image_repo> is hw_repo, and <tag> is 1:

	docker push registry.eu-gb.bluemix.net/namespace1/hw_repo:1

Verify that the image was pushed successfully by running the following command.

	ibmcloud cr image-list

-------------------------------------------------------------------------------------------------------------------------------------------------------------------

	docker push <login server>/<repository name>/aci-helloworld:v1

	The push refers to repository [specificregistryname.azurecr.io/myrepo/aci-helloworld]
	31ba1ebd9cf5: Pushed
	cd07853fe8be: Pushed
	73f25249687f: Pushed
	d8fbd47558a8: Pushed
	44ab46125c35: Pushed
	5bef08742407: Pushed
	v1: digest: sha256:565dba8ce20ca1a311c2d9485089d7ddc935dd50140510050345a1b0ea4ffa6e size: 1576


-------------------------------------------------------------------------------------------------------------------------------------------------------------------

Initialize a swarm with autolocking enabled

When you initialize a new swarm, you can use the --autolock flag to enable autolocking of swarm manager nodes when Docker restarts.

	$ docker swarm init --autolock

Swarm initialized: current node (k1q27tfyx9rncpixhk69sa61v) is now a manager.

To add a worker to this swarm, run the following command:

	    docker swarm join \
	    --token SWMTKN-1-0j52ln6hxjpxk2wgk917abcnxywj3xed0y8vi1e5m9t3uttrtu-7bnxvvlz2mrcpfonjuztmtts9 \
	    172.31.46.109:2377

To add a manager to this swarm, run 'docker swarm join-token manager' and follow the instructions.

To unlock a swarm manager after it restarts, run the `docker swarm unlock`
command and provide the following key:

    	SWMKEY-1-WuYH/IX284+lRcXuoVf38viIDK3HJEKY13MIHX+tTt8

	$ sudo service docker restart

	$ docker service ls

Error response from daemon: Swarm is encrypted and needs to be unlocked before it can be used. Use "docker swarm unlock" to unlock it.

Enable or disable autolock on an existing swarm

To enable autolock on an existing swarm, set the autolock flag to true.

	$ docker swarm update --autolock=true

Swarm updated.
To unlock a swarm manager after it restarts, run the `docker swarm unlock`
command and provide the following key:

    SWMKEY-1-+MrE8NgAyKj5r3NcR4FiQMdgu+7W72urH0EZeSmP/0Y

Please remember to store this key in a password manager, since without it you
will not be able to restart the manager.

-------------------------------------------------------------------------------------------------------------------------------------------------------------------
Before you can store your Docker images in IBM® Cloud Container Registry, you must install the IBM Cloud CLI and the container-registry plug-in, and then set up a registry namespace to create your own image repository in IBM Cloud Container Registry.

ibmcloud login
ibmcloud plugin update container-registry -r Bluemix

Using Docker Container in Cloud Foundry

As we all know, we can push source code to CF directly, and CF will compile it and create a container to run our application. Life is so great with CF.

But sometimes, for some reason, such as our App needs a special setup or we want to run an app on different platforms or infrastructures, we may already have a preconfigured container for our App. This won’t block our way to CF at all. This post will show you how to push docker images to CF.
Enable docker feature for CF

We can turn on docker support with the following cf command
1

  cf enable-feature-flag diego_docker
We can also turn it off by

1

  cf disable-feature-flag diego_docker


-------------------------------------------------------------------------------------------------------------------------------------------------------------------

1. Push an image to IBM Cloud Container Registry

If you haven’t already:

    Install the CLIs and Docker, as described in the “Prerequesite” section.
    Provision a cluster:
    bx cs cluster-create --name <name-of-cluster>

To push an image:

    Clone or download the GitHub repository associated with this course.
    Change directory to Lab 1:
    cd "Lab 1"
    Log in to the IBM Cloud CLI:
    bx login
    To specify an IBM Cloud region, include the API endpoint.
    Note: If you have a federated ID, use bx login --sso to log in to the IBM Cloud CLI. You know you have a federated ID when the login fails without the --sso and succeeds with the --sso option.
    Run bx cr login, and log in with your IBM Cloud credentials. This will allow you to push images to the IBM Cloud Container Registry.
    Tip: This course’s commands show the ng region. Replace ng with the region outputted from the bx cr login command.
    In order to upload images to the IBM Cloud Container Registry, you first need to create a namespace with the following command:
    bx cr namespace-add <my_namespace>
    Build the example Docker image:
    docker build --tag registry.ng.bluemix.net/<my_namespace>/hello-world .
    Verify the image is built:
    docker images
    Now push that image up to IBM Cloud Container Registry:
    docker push registry.ng.bluemix.net/<namespace>/hello-world
    If you created your cluster at the beginning of this, make sure it’s ready for use.
        Run bx cs clusters and make sure that your cluster is in “Normal” state.
        Use bx cs workers <yourclustername>, and make sure that all workers are in “Normal” state with “Ready” status.
        Make a note of the public IP of the worker.

You are now ready to use Kubernetes to deploy the hello-world application.

-------------------------------------------------------------------------------------------------------------------------------------------------------------------
# Remove java 7
	sudo yum remove -y java

# Install basic packages
	sudo yum install -y git

# Download and install java 8
	wget --no-cookies --no-check-certificate --header "Cookie: gpw_e24=http%3A%2F%2Fwww.oracle.com%2F; oraclelicense=accept-securebackup-cookie" "http://download.oracle.com/otn-pub/java/jdk/8u131-b11/d54c1d3a095b4ff2b6607d096fa80163/jdk-8u131-linux-x64.tar.gz"
	tar -xzvf jdk-8u131-linux-x64.tar.gz
	rm -rf jdk-8u131-linux-x64.tar.gz

# Configure JAVA_HOME
	sudo vim ~/.bashrc

alias cls='clear'

export JAVA_HOME=/opt/jdk1.8.0_131
export JRE_HOME=/opt/jdk1.8.0_131/jre
export PATH=$PATH:/opt/jdk1.8.0_131/bin:/opt/jdk1.8.0_131/jre/bin

source ~/.bashrc
java -version


### MULTISTAGE DOCKER IMAGE BUILD

Multistage builds make use of one Dockerfile with multiple FROM instructions. Each of these FROM instructions is a new build stage that can COPY artifacts from the previous stages. By going and copying the build artifact from the build stage, you eliminate all the intermediate steps such as downloading of code, installing dependencies, and testing. All these steps create additional layers, and you want to eliminate them from the final image.

The build stage is named by appending AS name-of-build to the FROM instruction. The name of the build stage can be used in a subsequent FROM and COPY command by providing a convenient way to identify the source layer for files brought into the image build. The final image is produced from the last stage executed in the Dockerfile.


Sample Dockerfile

		# STAGE 1
		FROM node:12.13.0-alpine as build
		WORKDIR /app
		COPY package*.json ./
		RUN npm install
		COPY . .
		RUN npm run build

	        # STAGE 2
		FROM nginx
		EXPOSE 3000
		COPY ./nginx/default.conf /etc/nginx/conf.d/default.conf
		COPY --from=build /app/build /usr/share/nginx/html
-------------------------------------------------------------------------------------------------------------------------------------------------------------------
	
		FROM golang:latest as builder
		RUN mkdir -p /go/src/github.com/ruanbekker
		WORKDIR /go/src/github.com/ruanbekker
		RUN useradd -u 10001 app
		COPY . .
		ENV GO111MODULE=auto
		RUN go get
		RUN CGO_ENABLED=0 GOOS=linux go build -a -installsuffix cgo -o main .

		FROM scratch
		COPY --from=builder /go/src/github.com/ruanbekker/main /main
		COPY --from=builder /etc/passwd /etc/passwd
		USER app
		CMD ["/main"]

This Dockerfile has two FROM commands, with each one constituting a distinct build stage. These distinct commands are numbered internally, stage 0 and stage 1 respectively. However, stage 0 is given a friendly alias of build. This stage builds the application and stores it in the directory specified by the WORKDIR command. The resultant image is over 420 MB in size.

The second stage starts by pulling the official Nginx image from Docker Hub. It then copies the updated virtual server configuration to replace the default Nginx configuration. Then the COPY –from command is used to copy only the production-related application code from the image built by the previous stage. The final image is approximately 127 MB.	
	
	FROM maven as maven
	RUN mkdir /usr/src/mymaven
	WORKDIR /usr/src/mymaven
	COPY . .
	RUN mvn install -DskipTests

	FROM tomcat 
	WORKDIR webapps 
	COPY --from=maven /usr/src/mymaven/target/java-tomcat-maven-example.war .
	RUN rm -rf ROOT && mv java-tomcat-maven-example.war ROOT.war	

-------------------------------------------------------------------------------------------------------------------------------------------------------------------
### How to Use docker-compose Environment Variables
	
	Specify environment variables in dockerfile
		# Version of Docker compose file
		version: "2.2"

		services:
		# Defining the service
		  mysql:
		# Defining the base image to be used 
		    image: mysql:5.7
		    hostname: mysql
		    container_name: mysql
		# Defining the environmental variable
		    environment:
		      # ENVIRONMET_VARIABLE_NAME: "environment variable value" 
		      MYSQL_ROOT_PASSWORD: "root_password"
		      MYSQL_ALLOW_EMPTY_PASSWORD: "password"
		      MYSQL_RANDOM_ROOT_PASSWORD: "password"
	
### .ENV	
Substituting the Environment Variables
	
store the values for environment variables in a file named .env that only the admin can read.
Create a file named .env in the same ~/docker-compose-demo directory and copy the code below in the .env file. The file will have all the environment variables with their respective values.
	
		# Defining the values of environmental variables
		 MYSQL_ROOT_PASSWORD="root_password"
		 MYSQL_ALLOW_EMPTY_PASSWORD="password"
		 MYSQL_RANDOM_ROOT_PASSWORD="password"
	
Again, execute the docker-compose up command. When you run the docker-compose up command, the docker-compose.yml file looks for the values of the environment variables in the .env file. The docker-compose command automatically looks for a .env file in the project directory or in the parent folder of your compose file.
	
### .ENV FILE PER ENVIRONMENT ( Dev,Stage,QA,PROD)
Using Multiple Environment Variables Files for Multiple Environments
	
The example below will emulate the usual environments one might encounter in IT Ops: Dev, QA, and Prod. Back on your terminal:
		
		# Enviornmental Variables file .env.dev
		MYSQL_ROOT_PASSWORD="password_DEV"
		MYSQL_ALLOW_EMPTY_PASSWORD="password1"
		MYSQL_RANDOM_ROOT_PASSWORD="password1"
	
		# Enviornmental Variables file .env.dev
		MYSQL_ROOT_PASSWORD="password_DEV"
		MYSQL_ALLOW_EMPTY_PASSWORD="password1"
		MYSQL_RANDOM_ROOT_PASSWORD="password1"
	
	
		# Enviornmental Variables file .env.prod
		MYSQL_ROOT_PASSWORD="password_PROD"
		MYSQL_ALLOW_EMPTY_PASSWORD="password3"
		MYSQL_RANDOM_ROOT_PASSWORD="password3"
	
Next, execute the docker-compose command with the --env-file option, specifying the .env.dev file. The docker-compose command looks for the .env.dev file in the current ~/docker-compose-demo directory.
	
		docker-compose --env-file .env.dev up
	
	Passing the file as an argument allows you to store the file anywhere and with an appropriate name.
	
### env_file 
	
When you store environment variables values in the .env file separately, you end up with lots of lines and references in the docker-compose.yml.

To reduce the references and the number of lines for environment variables in the docker-compose.yml file, consider incorporating the env_file in your docker-compose.yml file.
Create a file ./var.env, refer this in your docker/compose file
	
		version: "2.2"
		services:
		  mysql_svc:
		    image: mysql:5.7
		    hostname: mysql
		    container_name: mysql
		# Replacing the environment: with env_file: 
		  # environment:
		  #   MYSQL_ROOT_PASSWORD: ${MYSQL_ROOT_PASSWORD}
		  #   MYSQL_ALLOW_EMPTY_PASSWORD: ${MYSQL_ALLOW_EMPTY_PASSWORD}
		  #   MYSQL_RANDOM_ROOT_PASSWORD: ${MYSQL_RANDOM_ROOT_PASSWORD}
		    env_file:
		     - ./var.env
	

## Skip Build Stage ( --target )
	
		FROM golang:1.7.3 AS golang_builder 
		WORKDIR /go/src/github.com/alexellis/href-counter/ 
		RUN go get -d -v golang.org/x/net/html 
		COPY app.go . RUN CGO_ENABLED=0 GOOS=linux go build -a -installsuffix cgo -o app . 
		
		FROM alpine:latest 
	        RUN apk --no-cache add ca-certificates WORKDIR /root/ 
		COPY --from=golang_builder /go/src/github.com/alexellis/href-counter/app . CMD ["./app"]

## Build Command	
		$ docker build --target golang_builder -t alexellis2/href-counter:latest .
