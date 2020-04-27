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
web1 html port 80 will be forwarded to your host on port 8080, web2 html port 80 will be 8081, once nginx up you can validate it is up with http://localhost:8080 

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

To return to first snap
```bash
vagrant snapshot pop
```

## Ad hoc mode

> :warning: Snap your Entire Lab From this point.

First command will check Ansible connectivity with nodes

```bash
ansible -m ping all -i inventory -u vagrant

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

Create an Ansible User note that password is @testlab encypted in sha512, we'll see a method to protect your passwords in second step (playbooks + Vault)

```bash
ansible all -m user -i inventory -u vagrant -a "name=ansible password={{ '@testlab' | password_hash('sha512')}} shell=/bin/bash" --become 
```
Uncomment a lineinfile (sshd_config)
```bash
ansible all -m lineinfile -i inventory -u vagrant -a "dest=/etc/ssh/sshd_config regexp='^#PasswordAuthentication.*' line='PasswordAuthentication yes'" --become
```

Restart a service
```bash
ansible all -m service -i inventory -u vagrant -a "name=sshd state=restarted" --become
```
Install a package
```bash
ansible webservers -m apt -i inventory -u vagrant -a "name=nginx state=latest update_cache=yes" --become
```
Copy index.html to nginx Server
```bash
ansible webservers -m copy -i inventory -u vagrant -a "src=./files/index.html dest=/var/www/html/index.html owner=root group=root backup=yes" --become
```
> :warning: **From Here you can revert back to your snap**
## First Playbook All-in-one
## Ansible Roles usage