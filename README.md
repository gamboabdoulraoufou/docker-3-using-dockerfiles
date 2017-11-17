## docker-3-using-dockerfiles

When building your images with docker, each action taken (i.e. a command executed such as yum install) forms a new layer on top of the previous one. These base images then can be used to create new containers.

In this post, we will see about automating this process via Dockerfiles: scripts to build containers, step-by-step, layer-by-layer, automatically from a source (base) image.

> Dockerfiles

Each Dockerfile is a script, composed of various commands (instructions) and arguments listed successively to automatically perform actions on a base image in order to create (or form) a new one.

Dockerfiles begin with defining an image FROM which the build process starts. Followed by various other methods, commands and arguments (or conditions), in return, provide a new image which is to be used for creating docker containers.

> Dockerfile Commands

### ADD
The ADD command gets two arguments: a source and a destination. 
It basically copies the files from the source on the host into the container's own filesystem at the set destination. If, however, the source is a URL (e.g. http://github.com/user/file/), then the contents of the URL are downloaded and placed at the destination.

# Usage: ADD [source directory or URL] [destination directory]
ADD /my_app_folder /my_app_folder


> How to Use Dockerfiles



