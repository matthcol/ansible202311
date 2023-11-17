# Ansible
https://codeshare.io/eVDPW9
https://github.com/matthcol/ansible202311.git

## Parc
4 machines Debian 11 avec python2.7 et python 3.9
- 192.168.56.101 pilot (ansible)
- 192.168.56.102 host1
- 192.168.56.103 host2
- 192.168.56.132 host2
																											
## Utilisateurs
- srvadmin/password , sudoer (4 machines)
- installer/password + clé SSH
- intallerauto (nopassword) + clé SSH (passphrase: il fait beau aujourd'hui), sudoer sans password

## commandes SSH
```
ssh-agent
ssh-add chemin-vers-clé-privé
ssh-add -L  (lister)
ssh-add -D  (tout supprimer)
```

## fichier /etc/hosts de la machine pilote
192.168.56.132 host1 host1.example.org
192.168.56.150 host2 host2.example.org
192.168.56.151 host3 host3.example.org

## Inventory Ansible
file hosts_python (in current directory)
```
[myservers]
host1.example.org
host2.example.org
host3.example.org

[myservers:vars]
ansible_python_interpreter=/usr/bin/python3
```
## Ansible CLI

```
ansible -i hosts -k -m ping all
ansible -i hosts -u installerauto -m ping all
ansible -i hosts -u installerauto -m ping myservers 
```


## Modules
### module file:
https://docs.ansible.com/ansible/latest/collections/ansible/builtin/file_module.html

## Privilege Escalation
Note: -b (become) : become root with method sudo
```
ansible -i hosts -u installerauto -b -m file -a "path=/opt/dummy state=directory" all
ansible -i hosts -u installerauto -b -m file -a "path=/opt/dummy state=directory owner=installer group=installer" all
ansible -i hosts -u installerauto -b -m file -a "path=/opt/dummy state=absent" all 
```

## ANSIBLE PLAYBOOK
### become dans la ligne de commande
```
ansible-playbook -i hosts -u installerauto -b  install.yml
```
### become dans le plybook
```
ansible-playbook -i hosts -u installerauto  install.yml
```

## Module copy
https://docs.ansible.com/ansible/latest/collections/ansible/builtin/copy_module.html#ansible-collections-ansible-builtin-copy-module

## Variables
https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_variables.html

```
ansible-playbook -i hosts -u installerauto -e "dest_dir=/opt/nasty" install_extvars.yml 
ansible-playbook -i hosts -u installerauto -e "dest_dir=/opt/nasty" desinstall_extvars.yml 
```

### se limiter à une machine:
```
ansible-playbook -i hosts -u installerauto --limit host2.example.org install_extvars.yml 
```
### lister les plays/tasks
```
ansible-playbook -i hosts --list-tasks install_extvars.yml
```
### démarrer à une certaine tâche
```
ansible-playbook -i hosts -u installerauto --start-at-task "Update Apt Cache" install_extvars.yml
```
### lister les tags
```
ansible-playbook --list-tags install_extvars.yml
```

### executer une partie taguée (-t ou --tags)
```
ansible-playbook -i hosts -u installerauto -t TOOL install_extvars.yml
```

## Atelier:
- changer le nom d'hote de chacune des machines de l'inventaire
Ex: host2.example.org
Dans /etc/hostname => host2
Dans /etc/hosts => 127.0.1.1   host2 host2.example.org
Hints: 
  - utiliser les modules: debug, lineinfile
  - utiliser variable: ansible_hostname ou inventory_hostname
  - filter regexp_replace: https://docs.ansible.com/ansible/latest/collections/ansible/builtin/regex_replace_filter.html
  - reboot

## conditional
https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_conditionals.html

## modules pour commandes, scripts (en dernier recours)
https://docs.ansible.com/ansible/latest/collections/ansible/builtin/command_module.html#ansible-collections-ansible-builtin-command-module

https://docs.ansible.com/ansible/latest/collections/ansible/builtin/shell_module.html#ansible-collections-ansible-builtin-shell-module

https://docs.ansible.com/ansible/latest/collections/ansible/builtin/script_module.html#ansible-collections-ansible-builtin-script-module

## modules: user/systemd + bdd
```
ansible-playbook -i hosts -u installerauto install.yml 
ansible-playbook -i hosts -u installerauto -t APP install.yml 
ansible-playbook -i hosts -u installerauto -t DB install.yml 

ssh installerauto@host1 "id myapp"
ssh installerauto@host1 "id johndoe"

scp 10.174.31.180:artifacts.tgz .
```

### Modules PostgreSQL
https://docs.ansible.com/ansible/latest/collections/community/postgresql/index.html#plugins-in-community-postgresql

```
ansible-galaxy collection list
ansible-galaxy collection install community.postgresql
```

### repo postgresql:
```
	deb https://apt.postgresql.org/pub/repos/apt bullseye-pgdg main
```  
### pg_hba.conf: 
```
host dbmovie movie 192.168.56.0/24            scram-sha-256
```

## playbook with role

https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_reuse_roles.html

```
ansible-galaxy init roles/api
ansible-galaxy init roles/db
```

## start api
```
java -jar movieapi-17.jar
```

### swagger de l'api rest (adapter ip api)
http://192.168.56.102:8080/swagger-ui/

## variables and facts
```
ansible-playbook -i hosts -u installerauto explore-variables.yml > facts.json
```
 
NB: les custom facts s'utilisent directement comme des variables





