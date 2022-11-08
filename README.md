# Docker for some projects

## Docker with Ansible

### Build the image
```sh
# In path/to/the/repo/
# Build the image with the tag name ansible-controller
docker build -t ansible-controller ansible/ansible_controller/
```
### Check if the controller is working
Structure of the directory shared while running the container :
```sh
# Structure of the targeted directory
ansible/ansible_volumes/init_volume/
└── init-playbook.yml
```
First, let explain the commands:
- **docker run** : basic command to run the container
- **--rm** : it will delete the container after completing the command
- **-v $(pwd)/ansible/ansible_volumes/init_volume:/ansible_files** : The content of the directory init_volume from the host will be shared with the directory ansible_files from the container
- **ansible-controller** : the name we set when building the image (see above)
- **ansible-playbook -i "my-local-node," --connection=local init-playbook.yml** : ansible-playbook command (for more details, check [here](https://docs.ansible.com/ansible/latest/cli/ansible-playbook.html))
```sh
# In path/to/the/repo/
# Command to run the container
# It will launch the playbook at the same time
docker run \
--rm \
-v $(pwd)/ansible/ansible_volumes/init_volume:/ansible_files \
ansible-controller \
ansible-playbook -i "my-local-node," --connection=local init-playbook.yml
```
### Interact with the Docker host
The following steps will permit us to interact with the docker host where the ansible-controller container is running.

Structure of the directory shared while running the container :
```sh
# Structure of the targeted directory
ansible/ansible_volumes/host_interaction/
├── ansible.cfg
├── host-int-playbook.yml
└── inventory
    ├── 00_inventory.yml
    └── host_vars
        ├── my-docker-host.yml
        └── my-local-node.yml
```

#### What changes compare to an ansible controller set up on a VM ?
For the moment, 2 ocurring errors were found :
- **Prompt command is not working** : the ansible options -k, -K are not working
***Resolved*** : Put everything in variables. For sensitive ones, it will be explained in the ansible-vault chapter
- **Fingerprint check not working** : *host_key_checking = False* option in ansible.cfg not working
***Resolved*** : Add **ansible_ssh_extra_args: '-o StrictHostKeyChecking=no'** for the concerned hosts

#### Let's try our Ansible controller container
For your configuration, let's change few things. We will work in remote and your IP, user and password are not the same.
- Modify *my-docker-host.yml* file
```sh
# In path/to/the/repo/
cat ansible/ansible_volumes/host_interaction/inventory/host_vars/my-docker-host.yml
# It should look like this
---
my_var: "my-docker-host"
ansible_host: <IP of the docker host>
ansible_connection: ssh
ansible_user: "<username on your docker host>"
ansible_password: "<Password of the username>"
ansible_ssh_extra_args: '-o StrictHostKeyChecking=no'
```
**WARNING** : Displaying your password like that is not secure at all. Don't worry, we will fix that later.
- Run the ansible-controller container
In the playbook, we changed one of the tasks:
```sh
- name: Catch the linux distribution of the host
      shell: ls
      register: MY_DISTRIB
```
Instead of giving us the distribution, it will display the list of files from the /ansible_files drectory for the container and from your home directory for your docker host.
```sh
# In path/to/the/repo/
docker run \
--rm \
-v $(pwd)/ansible/ansible_volumes/host_interaction:/ansible_files \
ansible-controller \
ansible-playbook host-int-playbook.yml
# The ouput should look like that
TASK [Display the linux distribution] ******************************************
ok: [my-local-node] => {
    "msg": "ansible.cfg\nhost-int-playbook.yml\ninventory"
}
ok: [my-docker-host] => {
    "msg": "docker-for-projects\n<...>"
}
```

## Docker with Jenkins

### Pull the jenkins container
To see more details about this image check [here](https://hub.docker.com/r/jenkins/jenkins)
```sh
# Download the docker image from DockerHub
docker pull jenkins/jenkins:lts
```
