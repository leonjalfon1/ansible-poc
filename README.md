# ANSIBLE
---

## Install Ansible on Docker

Dockerfile:
```
FROM ubuntu:18.04
RUN apt update \
    && apt install -y software-properties-common \
    && apt-add-repository --yes --update ppa:ansible/ansible \
    && apt install -y ansible
CMD ["ansible","--version"]
```

Run Command:
```
docker build -t ansible:2.9.2 .
```

---

## Test Ansible Container

Run:
```
docker run ansible:2.9.2
```

Expected Output:
```
ansible 2.9.2
  config file = /etc/ansible/ansible.cfg
  configured module search path = [u'/root/.ansible/plugins/modules', u'/usr/share/ansible/plugins/modules']
  ansible python module location = /usr/lib/python2.7/dist-packages/ansible
  executable location = /usr/bin/ansible
  python version = 2.7.15+ (default, Oct  7 2019, 17:39:04) [GCC 7.4.0]
```

---

## Run Ansible using Docker


```
docker run \
-v $(pwd)/ssh-keys:/root/.ssh \
-v $(pwd)/config:/etc/ansible \
-v $(pwd)/playbooks:/ansible/playbooks \
-v $(pwd)/roles:/root/.ansible/roles \
ansible:2.9.2 \
<command>
```

For example:
```
docker run \
-v $(pwd)/ssh-keys:/root/.ssh \
-v $(pwd)/config:/etc/ansible \
-v $(pwd)/playbooks:/ansible/playbooks \
-v $(pwd)/roles:/root/.ansible/roles \
ansible:2.9.2 \
ansible -m ping all
```

---

## Configure SSH keys

 - To reate ssh keys use:
```
ssh-keygen
```

 - Copy the keys to remote servers using:
```
ssh-copy-id -i (username)@(node machine ip)
```

---

## Install Managed Nodes Prerequisites

 - Install prerequisites
```
sudo apt -y upgrade
sudo apt install -y python
```

---

## Run with user and password instead of ssh keys

 - Use the arguments "--user" and "--ask-pass":
 
```
docker run \
-v $(pwd)/ssh-keys:/root/.ssh \
-v $(pwd)/config:/etc/ansible \
-v $(pwd)/playbooks:/ansible/playbooks \
-v $(pwd)/roles:/root/.ansible/roles \
ansible:2.9.2 \
ansible all -m ping --user sela --ask-pass
```
```
docker run \
-v $(pwd)/ssh-keys:/root/.ssh \
-v $(pwd)/config:/etc/ansible \
-v $(pwd)/playbooks:/ansible/playbooks \
-v $(pwd)/roles:/root/.ansible/roles \
ansible:2.9.2 \
ansible LinuxServers -a "touch ~/wololo" --user sela --ask-pass
```

## Using Playbooks

 - Playbooks should be stored in playbook folder:
 
```
$(pwd)/playbooks:/ansible/playbooks/
```
 
 - To check the syntax of a playbok use:
 
```
docker run \
-v $(pwd)/ssh-keys:/root/.ssh \
-v $(pwd)/config:/etc/ansible \
-v $(pwd)/playbooks:/ansible/playbooks \
-v $(pwd)/roles:/root/.ansible/roles \
ansible:2.9.2 \
ansible-playbook ansible/playbooks/webserver-test.yaml --syntax-check -v
```

 - To run a playbook use:
 
```
docker run \
-v $(pwd)/ssh-keys:/root/.ssh \
-v $(pwd)/config:/etc/ansible \
-v $(pwd)/playbooks:/ansible/playbooks \
-v $(pwd)/roles:/root/.ansible/roles \
ansible:2.9.2 \
ansible-playbook ansible/playbooks/webserver-test.yaml --user sela --ask-pass
```

## Install Roles

- To instal a role use the command below(require internet access):

```
docker run \
-v $(pwd)/ssh-keys:/root/.ssh \
-v $(pwd)/config:/etc/ansible \
-v $(pwd)/playbooks:/ansible/playbooks \
-v $(pwd)/roles:/root/.ansible/roles \
ansible:2.9.2 \
ansible-galaxy install nginxinc.nginx
```