## docker-3-using-dockerfiles

When building your images with docker, each action taken (i.e. a command executed such as yum install) forms a new layer on top of the previous one. These base images then can be used to create new containers.

In this post, we will see about automating this process via Dockerfiles: scripts to build containers, step-by-step, layer-by-layer, automatically from a source (base) image.

> Dockerfiles

Each Dockerfile is a script, composed of various commands (instructions) and arguments listed successively to automatically perform actions on a base image in order to create (or form) a new one.

Dockerfiles begin with defining an image FROM which the build process starts. Followed by various other methods, commands and arguments (or conditions), in return, provide a new image which is to be used for creating docker containers.

> Dockerfile Commands

#### ADD
The ADD command gets two arguments: a source and a destination. 
It basically copies the files from the source on the host into the container's own filesystem at the set destination. If, however, the source is a URL, then the contents of the URL are downloaded and placed at the destination.
```sh
ADD /my_app_folder /my_app_folder
```
#### CMD
The command CMD, similarly to RUN, can be used for executing a specific command. However, unlike RUN it is not executed during build, but when a container is instantiated using the image being built. Therefore, it should be considered as an initial, default command that gets executed (i.e. run) with the creation of containers based on the image.

To clarify: an example for CMD would be running an application upon creation of a container which is already installed using RUN (e.g. RUN apt-get install …) inside the image. This default application execution command that is set with CMD becomes the default and replaces any command which is passed during the creation.

Example:

```sh
CMD "echo" "Hello docker!"
```

#### ENTRYPOINT
ENTRYPOINT argument sets the concrete default application that is used every time a container is created using the image. For example, if you have installed a specific application inside an image and you will use this image to only run that application, you can state it with ENTRYPOINT and whenever a container is created from that image, your application will be the target.

If you couple ENTRYPOINT with CMD, you can remove "application" from CMD and just leave "arguments" which will be passed to the ENTRYPOINT.

```sh
ENTRYPOINT echo
```sh

```sh
CMD "Hello docker!"
ENTRYPOINT echo  
```

#### ENV
The ENV command is used to set the environment variables (one or more). These variables consist of “key = value” pairs which can be accessed within the container by scripts and applications alike. This functionality of docker offers an enormous amount of flexibility for running programs.

```sh
ENV SERVER_WORKS 4
```

#### EXPOSE
The EXPOSE command is used to associate a specified port to enable networking between the running process inside the container and the outside world (i.e. the host).

Example:

```sh
EXPOSE 8080
```

#### FROM
FROM directive is probably the most crucial amongst all others for Dockerfiles. It defines the base image to use to start the build process. It can be any image, including the ones you have created previously. If a FROM image is not found on the host, docker will try to find it (and download) from the docker image index. It needs to be the first command declared inside a Dockerfile.

```sh
FROM ubuntu
```

#### MAINTAINER
One of the commands that can be set anywhere in the file - although it would be better if it was declared on top - is MAINTAINER. This non-executing command declares the author, hence setting the author field of the images. It should come nonetheless after FROM.

```sh
MAINTAINER authors_name
```

#### RUN
The RUN command is the central executing directive for Dockerfiles. It takes a command as its argument and runs it to form the image. Unlike CMD, it actually is used to build the image (forming another layer on top of the previous one which is committed).

```sh
RUN aptitude install -y riak
```

#### USER
The USER directive is used to set the UID (or username) which is to run the container based on the image being built.

```sh
USER 751
```

#### VOLUME
The VOLUME command is used to enable access from your container to a directory on the host machine (i.e. mounting it).

```sh
VOLUME ["/my_files"]
```

#### WORKDIR
The WORKDIR directive is used to set where the command defined with CMD is to be executed.

```sh
WORKDIR ~/
```

> How to Use Dockerfiles
```sh
docker build -t [name] .
``` 

> Dockerfile Example: Creating an Image to Install MongoDB

```sh
# create docker file and append configuration
cat <<EOF >Dockerfile
 
 ############################################################
# Dockerfile to build MongoDB container images
# Based on Ubuntu
############################################################

# Set the base image to Ubuntu
FROM ubuntu

# File Author / Maintainer
MAINTAINER Example McAuthor

# Update the repository sources list
RUN apt-get update

################## BEGIN INSTALLATION ######################
# Install MongoDB Following the Instructions at MongoDB Docs
# Ref: http://docs.mongodb.org/manual/tutorial/install-mongodb-on-ubuntu/

# Add the package verification key
RUN apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 7F0CEB10

# Add MongoDB to the repository sources list
RUN echo 'deb http://downloads-distro.mongodb.org/repo/ubuntu-upstart dist 10gen' | tee /etc/apt/sources.list.d/mongodb.list

# Update the repository sources list once more
RUN apt-get update

# Install MongoDB package (.deb)
RUN apt-get install -y mongodb-10gen

# Create the default data directory
RUN mkdir -p /data/db

##################### INSTALLATION END #####################

# Expose the default port
EXPOSE 27017

# Default port to execute the entrypoint (MongoDB)
CMD ["--port 27017"]

# Set default container command
ENTRYPOINT usr/bin/mongod

EOF

# building image
docker build -t my_mongodb .

# running A MongoDB Instance
docker run -it my_mongodb
```
