
# Tomcat, Sonarqube and Nexus Deployment using role based ansible playbook


Expects Ubuntu 18.04

We need to create the role for tomcat, sonarqube and Nexus server using ansible galaxy command, to create the role, run the below command

```
ansible-galaxy init tomcat
```
It will create the below directory structure for the tomcat.

```
└── tomcat
    ├── defaults
    │   └── main.yml
    ├── handlers
    │   └── main.yml
    ├── meta
    │   └── main.yml
    ├── README.md
    ├── tasks
    │   ├── main.yml
    ├── tests
    │   ├── inventory
    │   └── test.yml
    └── vars
        └── main.yml
```
Same steps can be follow to create the role for the Sonarqube and Nexus application using ansible galaxy command.
Then final directory structure will be like the below:

```
├── hosts
├── main.yml
├── nexus-server
│   ├── defaults
│   │   └── main.yml
│   ├── handlers
│   │   └── main.yml
│   ├── meta
│   │   └── main.yml
│   ├── tasks
│   │   ├── main.yml
│   ├── tests
│   │   ├── inventory
│   │   └── test.yml
│   └── vars
│       └── main.yml
├── sonarqube
│   ├── defaults
│   │   └── main.yml
│   ├── handlers
│   │   └── main.yml
│   ├── meta
│   │   └── main.yml
│   ├── tasks
│   │   ├── main.yml
│   ├── tests
│   │   ├── inventory
│   │   └── test.yml
│   └── vars
│       └── main.yml
└── tomcat
    ├── defaults
    │   └── main.yml
    ├── handlers
    │   └── main.yml
    ├── meta
    │   └── main.yml
    ├── README.md
    ├── tasks
    │   ├── main.yml
    ├── tests
    │   ├── inventory
    │   └── test.yml
    └── vars
        └── main.yml
```
Lets create the centralized main.yml file which will contain the information of hosts, username and roles of respective servers.

## vi main.yml

```
---
- hosts: nexus
  gather_facts: false
  become: yes
  become_method: sudo
  remote_user: ubuntu
  vars_files:
    - nexus-server/vars/main.yml
  handlers:
    - include: nexus-server/handlers/main.yml
  roles:
    - role: nexus-server

- hosts: sonarqube
  gather_facts: false
  become: yes
  become_method: sudo
  remote_user: ubuntu
  vars_files:
    - sonarqube/vars/main.yml
  handlers:
    - include: sonarqube/handlers/main.yml
  roles:
    - role: sonarqube
  
- hosts: tomcat
  gather_facts: false
  become: yes
  become_method: sudo
  remote_user: ubuntu
  vars_files:
    - tomcat/vars/main.yml
  handlers:
    - include: tomcat/handlers/main.yml
  roles:
    - role: tomcat

```
These playbooks deploy implementation of Tomcat Application Server, Sonarqube and Nexus server on respective host server. To use them, first edit the hosts inventory file to contain the hostnames of the servers on which you want deployed applications, add the ansible username(client server user name) and private key path of master server user(In this example, we are using jenkins user to run the anisble playbook) and also edit the group_vars/tomcat-servers file to set any Tomcat configuration parameters you need same you can do for the Sonarqube and Nexus Server.

## vi hosts

```
[tomcat]

192.168.62.131 ansible_connection=ssh ansible_user=ubuntu ansible_private_key_file=/var/lib/jenkins/.ssh/id_rsa ansible_python_interpreter=/usr/bin/python3

[sonarqube]

192.168.62.132 ansible_connection=ssh ansible_user=ubuntu ansible_private_key_file=/var/lib/jenkins/.ssh/id_rsa ansible_sudo_pass=admin ansible_python_interpreter=/usr/bin/python3

[nexus]

192.168.62.133 ansible_connection=ssh ansible_user=ubuntu ansible_private_key_file=/var/lib/jenkins/.ssh/id_rsa ansible_python_interpreter=/usr/bin/python3
```

Then run the playbook, like this:

```
ansible-playbook -i hosts main.yml
```

When the playbook run completes, you should be able to see the Tomcat Application Server running on the ports you chose, on the target machines also sonarqube and Nexus Server on respective hosts.

