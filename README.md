# docker-commands

## Basic

List all the containers

    docker ps
    
List all the images available

    docker images

List all the containers that have runned

    docker ps -a
    
Remove the specified container

    docker rm <container name | container id (also first 3 characters)>
    docker rm website
    docker rm 3f4j2j542ji3
    docker rm 3f4
    
Force the remove even if the container is running

    docker rm -f <container>
    docker rm -f website
  
Run a container

    docker run <image name (:version optional)> --name <optional>
    docker run nginx
    docker run nginx:latest
    docker run nginx:latest --name website
    
Run a container with specific port or ports

    docker run -p <external:internal> <image>
    docker run -p 8080:80 nginx
    docker run -p 8080:80 -p 3000:80 nginx
    
Run a container detached to keep using the same console

    docker run -d <image>
    docker run -d nginx
    docker run -d -p 8080:80 nginx
    
Run with a specific volume (Folder that can be shared between the host and container) Use "ro" for read-only.
When you make a change in the host folder, those changes can be visible to the containers.
Volumes are useful for development process

    docker run --name website -v E:/html:/usr/share/nginx/html:ro -d -p 3000:80 nginx
    docker run --name website -v E:/html:/usr/share/nginx/html -d -p 3000:80 nginx
    
Run sharing volume between containers

    docker run --name website-copy --volumes-from website -d -p 8080:80 nginx
    
## Docker file

The next sample is the basic structure of a static image created using a Dockerfile. This file has no extensio and just this plain name

    FROM nginx:latest
    ADD . /usr/share/nginx/html
    
Where FROM is the base image and ADD is the instruction. The dot is used because the Dockerfile was created in the same folder. The next route is where the content should be placed.

To create the Docker file, use the next command. Where we tag the version and with the dot indicates where the files are located, for this example in the same directory

    docker build --tag website:latest .
    
In order to use the recent image created, we can run this command

    docker run --name website -d -p 8080:80 website:latest
