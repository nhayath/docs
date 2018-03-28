#### Requirements
- at least 2 remote machines
- Both machine must to be accessible from local machine over `ssh`
- `ssh` must be configured to communicate using public ssh-key

#### Install `ansible`
```
$ sudo apt-get update
$ sudo apt-get install software-properties-common
$ sudo apt-add-repository ppa:ansible/ansible
$ sudo apt-get update
$ sudo apt-get install ansible
```

#### Download Ansible-Tinc Playbook

```
cd ~
git clone https://github.com/thisismitch/ansible-tinc
cd ansible-tinc
```