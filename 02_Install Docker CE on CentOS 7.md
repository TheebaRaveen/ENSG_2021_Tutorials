## Installation : 
Ensure that the basic post-install steps are met, then follow this procedure :

```
https://docs.docker.com/engine/install/centos/
```

Don't forget to drive the **post-install** steps too 

```
https://docs.docker.com/engine/install/linux-postinstall/
```

Verify that Docker CE is installed correctly by running the hello-world image.
```
user@host> sudo docker run hello-world
	Unable to find image 'hello-world:latest' locally
	latest: Pulling from library/hello-world
	1b930d010525: Pull complete
	Digest: sha256:6540fc08ee6e6b7b63468dc3317e3303aae178cb8a45ed3123180328bcc1d20f
	Status: Downloaded newer image for hello-world:latest
	 
	Hello from Docker!
	This message shows that your installation appears to be working correctly.
	 
	To generate this message, Docker took the following steps:
	 1. The Docker client contacted the Docker daemon.
	 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
	    (amd64)
	 3. The Docker daemon created a new container from that image which runs the
	    executable that produces the output you are currently reading.
	 4. The Docker daemon streamed that output to the Docker client, which sent it
	    to your terminal.
	 
	To try something more ambitious, you can run an Ubuntu container with:
	 $ docker run -it ubuntu bash
	 
	Share images, automate workflows, and more with a free Docker ID:
	 https://hub.docker.com/
	 
	For more examples and ideas, visit:
	 https://docs.docker.com/get-started/
```

## Docker-compose : let's the magic happen!
Compose is a tool for defining and running multi-container Docker applications. With Compose, you use a YAML file to configure your application’s services. Then, with a single command, you create and start all the services from your configuration. 
 
Using Compose is basically a three-step process:
	• Define your app’s environment with a Dockerfile so it can be reproduced anywhere.
	• Define the services that make up your app in docker-compose.yml so they can be run together in an isolated environment.
	• Run docker-compose up and Compose starts and runs your entire app.
 
Compose provides such an elegant way to maintain app containers configuration and deployment, that I'm now using it for every deployment, event for a single container. For a complete refence of the YAML syntax, please refer to the official documentation, very well-built (https://docs.docker.com/compose/compose-file/)
 
Install the latest available docker-compose package.
**Don't install docker-compose from apt-get!** Remember, you've setup the latest up to date docker repositories, and we are using the Proxmox official image, based on a really stable, but not so fresh Linux distribution.
	 
	For up to date version of docker-compose, go to the github official repository -> https://github.com/docker/compose/releases, and follow the latest stable release installatoin instructions.
 
At the time I wrote thoses lines, the latest version was the 1.24.1.

```
user@host> curl -L https://github.com/docker/compose/releases/download/1.24.1/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose
```
Change the rights associated with the docker-compose executable

```
user@host> sudo chmod +x /usr/local/bin/docker-compose
```
 
Run the "docker-compose" command; it should list the quick help reference.