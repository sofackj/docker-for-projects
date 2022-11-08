# Docker for some projects

## Docker with Ansible

### Build the image
```sh
# Build the image with the tag name ansible-controller
docker build -t ansible-controller ansible/ansible_controller/
```
### Check if the controller is working
First, let explain the commands:
- **docker run** : basic command to run the container
- **--rm** : it will delete the container after completing the command
- **-v $(pwd)/ansible/ansible_volumes/init_volume:/ansible_files** : The content of the directory init_volume from the host will be shared with the directory ansible_files from the container
- **ansible-controller** : the name we set when building the image (see above)
- **ansible-playbook -i "my-local-node," --connection=local init-playbook.yml** : ansible-playbook command (for more details, check [here](https://docs.ansible.com/ansible/latest/cli/ansible-playbook.html))
```sh
# Command to run the container
# It will launch the playbook at the same time
docker run \
--rm \
-v $(pwd)/ansible/ansible_volumes/init_volume:/ansible_files \
ansible-controller \
ansible-playbook -i "my-local-node," --connection=local init-playbook.yml
```

## Docker with Jenkins

### Pull the jenkins container
To see more details about this image check [here](https://hub.docker.com/r/jenkins/jenkins)
```sh
# Download the docker image from DockerHub
docker pull jenkins/jenkins:lts
```
