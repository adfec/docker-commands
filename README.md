# docker-commands

## Basic

List all the containers

    docker ps
    
List all the images available

    docker images

List all the containers that you have runned

    docker ps -a
    
Remove the specified container

    docker rm <container name | container id (also first 3 characters)>
    docker rm website
    docker rm 3f4j2j542ji3
    docker rm 3f4
    
To remove an image

    docker image rm <image_name | id (also first 3 characters)>
    docker rmi <image_name | id (also first 3 characters)>
    
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
    
Sharing volume between containers as a copy

    docker run --name website-copy --volumes-from website -d -p 8080:80 nginx
    
To explore the content inside the container. When the bash command is not available, first you need to inspect (find below) the container and look for "Cmd", there you might find the right way to invoke the console

    docker exec -it website bash
    
## Docker file

The next example is the basic structure of a static image created using a Dockerfile. This file has no extension and just this name

    FROM nginx:latest
    ADD . /usr/share/nginx/html
    
Where FROM is the base image and ADD is the instruction. The dot is used because the Dockerfile was created in the same folder, the next route is where the content should be placed.

To create the Docker image, use the bellow command. Where we tag the version and with the dot indicates where the files are located, for this case in the same directory

    docker build --tag website:latest .
    
In order to use the recent image created, we can run this command

    docker run --name website -d -p 8080:80 website:latest
    
## Creating a Docker file with app

For this scenario, let's say we have a simple NodeJS application that says "Hello world"
To create the Dockerfile we need the base image (FROM), the directory (WORKDIR), from where to where (ADD), the command to run if necesary (in this case to install dependencies of the Node sample) and finally the instruction to run the application

    FROM node:latest
    WORKDIR /app
    ADD . .
    RUN npm install
    CMD node index.js
    
Example with Spring Boot application

    FROM openjdk:8-jre-alpine
    COPY "target/app-0.0.1-SNAPSHOT.jar" "app.jar"
    EXPOSE 8080
    ENTRYPOINT ["java","-jar","app.jar"]
    
Example with Spring Boot and Packeto.io (buildpack)

First you need to update the pom.xml definition as shown

	<build>
		<plugins>
			<plugin>
				<groupId>org.springframework.boot</groupId>
				<artifactId>spring-boot-maven-plugin</artifactId>
				<configuration>
					<image>
						<name>myapp/${project.artifactId}</name>
					</image>
				</configuration>
			</plugin>
		</plugins>
	</build>
    
Then you can run 
 
    mvn spring-boot:build-image 

    
To ignore files from being copied, we can create a _.dockerignore_ file. Inside the file you can list folders or files to ignore. For example:

    node_modules
    Dockerfile
    .git
    *.ts
    folder/**

## Caching and layers

The caching system allows us to avoid repetitive tasks while the image is being created, like copying files or compiling instructions. This is useful when the only changes between images versions are the sources

    FROM node:latest
    WORKDIR /app
    ADD package*.json ./
    RUN npm install
    ADD . .
    CMD node index.js
    
## Tags

How to move our latest image to a previous one with a tag. 
Taggin allows not only version control but also a fast way to change the running container if a rollback is needed

    docker tag my-website:latest my-website:1
    
When you want to load your image to the Docker registry

    docker tag my-website:1 mydockeraccount/my-website:1
    
Before you upload your images, you need to log in: _docker login_

    docker push mydockeraccount/my-website:1

## Inspect

This is a way to fully get information about containers

    docker inspect <image_name | id (also first 3 characters)>

## Logs

Monitor any event that is registered by the application or container. Use -f for tail mode

    docker logs <image_name | id (also first 3 characters)>
    docker logs -f <image_name | id (also first 3 characters)>

## Docker Compose

Multiple containers at once. By default the feature is installed with the Windows installer.
To verify you can use _docker-compose_ version or _docker-compose --version_

## Considerations

- Always try to use Apline versions for small size
- Docker images are immutable, if you try to override a tag during compilation of the same image, you will end up with <none> tag images
- To get detailed information about any Docker command, we type *docker {command} --help*
