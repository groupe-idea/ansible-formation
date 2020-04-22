# Ansible-formation
public repo for ansible formation 

# Requirements 
* Ansible up and running https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html#
* Vagrant up and running for testing purpose https://www.vagrantup.com/downloads.html
* copy your ssh pub key on the vagrant box

# Quick Demos

First you need to start your Vangrant lab 

```bash
vagrant up
```

Will start your boxes, web1 & web2 production & staging webservers (Debian 9) respectively and db1 & db2 production & staging dbservers (CentOS). You can see if everything is up and running

```bash
vagrant status

Current machine states:

web1                      running (virtualbox)
db1                       running (virtualbox)
web2                      running (virtualbox)
db2                       running (virtualbox)

This environment represents multiple VMs. The VMs are all listed
above with their current state. For more information about a specific
VM, run `vagrant status NAME`.
```

Once your boxes are started with vagrant up 
```
ssh-copy-id 127.0.0.1 -p 2222
```
if it doesn't work you can do it manually by ssh in your box (vagrant ssh) and copy your key in /home/vagrant/.ssh/authorized_keys, this enable the fact that you can lauch playbooks on your vagrant box.


> :warning: **Before going further** : Once Everything is up you need to snapshot your four boxes, with this you can restore a box state to the Original without destroy it and re-run the original config. 


To snap the entire lab 

```bash
vagrant snapshot push
```

To reverse to original state
```bash
vagrant snapshot pop
```

## Ad hoc mode

> :warning: Snap your Entire Lab From this point

First command will check Ansible connectivity with nodes

```bash
ansible -m ping all -i staging -u vagrant

db2 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python"
    }, 
    "changed": false, 
    "ping": "pong"
}
web2 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    }, 
    "changed": false, 
    "ping": "pong"
}
```

Second command create an Ansible User and add it to sudoers group

```bash
ansible all -m user -i staging -u vagrant -a "name=ansible password={{ '@testlab' | password_hash('sha512') }}" --become --ask-become-pass 
```
Third command uncomment a lineinfile (sshd_config)

```bash
ansible all -m lineinfile -i staging -u vagrant -a "dest: /etc/ssh/sshd_config regexp: '^#PasswordAuthentication.*' line: 'PasswordAuthentication yes'" --become

## First Playbook All-in-one

> :warning: Snap your Entire Lab From this point

## Ansible Roles usage

> :warning: Snap your Entire Lab From this point

