# Docker for some projects

## Docker with Ansible

### Build the image
```sh
# Build the image with the tag name ansible-controller
docker build -t ansible-controller ansible/ansible_controller/
```

## Docker with Jenkins

### Pull the jenkins container
To see more details about this image check [here](https://hub.docker.com/r/jenkins/jenkins)
```sh
# Download the docker image from DockerHub
docker pull jenkins/jenkins:lts
```
