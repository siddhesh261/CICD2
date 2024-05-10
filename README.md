# A Sample Python Flask Application

## Overview

- This is a basic Python Flask application that can used to demonstrate how to package an application in the form of Docker Images.
- This application is a web based app with a landing page; it displays HOSTNAME and IP of the container.

## Application Components

- **src**

- **.gitignore**

- **Dockerfile**
- **Jenkinsfile**
- **Readme.md**
- **requirements.txt**

### Build application
Build the Docker image manually by cloning the Git repo.
```
$ git clone https://github.com/kbindesh/sample-python-flask-app.git
$ docker build -t <your_dockerhub>/python-flask-docker .
```

### Run the container
Create a container from the image.
```
$ docker run --name my-container -d -p 8080:8080 kbindesh/python-flask-docker
```

Now visit http://<your_vm_public_ip_or_dns_name>:8080
```
 The hostname of the container is 6095273a4e9b and its IP is 172.17.0.2. 
```

### Verify the running container
Verify by checking the container ip and hostname (ID):
```
$ docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' my-container
172.17.0.2
$ docker inspect -f '{{ .Config.Hostname }}' my-container
6095273a4e9b
```


