# Ansible-formation
public repo for ansible formation 

# Requirements 
* Ansible up and running https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html#
* Vagrant up and running for testing purpose https://www.vagrantup.com/downloads.html
* copy your ssh pub key on the vagrant box

Once your box is started with vagrant up 
```
ssh-copy-id 127.0.0.1 -p 2222
```
if it doesn't work you can do it manually by ssh in your box (vagrant ssh) and copy your key in /home/vagrant/.ssh/authorized_keys, this enable the fact that you can lauch playbooks on your vagrant box without specifing vagrant user.

#Quick Demos

First you need to start your Vangrant lab 
```bash
vagrant up
```
Will start your boxes, web1 & web2 production & staging webservers (Debian 9) respectively and db1 & db2 production & staging dbservers (CentOS). You can see if everything is up and running whit 
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

##ad hoc mode

