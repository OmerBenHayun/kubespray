# instruction to install kubespray from original repo (master of this repo)

instruction to do that from the repository
# on the master VPC do:
## make sure ssh between nodes works
<!---  * make sure you can sudo from master to itself and to the worker via ssh from roo user without a password-->
* at the end of this step the `master` node shoudld be able to `ssh` itself and to worker(s) (from and to `root` user) without a password.
* in case you want to use the same `ssh` keys you use form connecting to the VPC's you can do that with copy the public and private ssh key from `.ssh` to the master node.and use `chmod 400 id_rsa` on it and make sure it's works. (internal IP)

## Install dependencies (on master node)
clone repo (or clone my repo and branch to the spesific file)


* install python
```
sudo apt install python3-pip
```

* Install dependencies from requirements.txt
```
sudo pip3 install -r requirements.txt
````
* clone on of the repo `cd` into it and checkout to wanted branch
* Copy ``inventory/sample`` as ``inventory/mycluster``
```
cp -rfp inventory/sample inventory/mycluster
```
* Update ansible
Update Ansible inventory file with inventory builder (with local IP addresses)
```
declare -a IPS=(10.10.1.3 10.10.1.4 10.10.1.5) #internal ip's change it to you internal ip address
CONFIG_FILE=inventory/mycluster/hosts.yaml python3 contrib/inventory_builder/inventory.py ${IPS[@]}
```
* change settings if neccery 
Review and change parameters under `inventory/mycluster/group_vars`
```
cat inventory/mycluster/group_vars/all/all.yml
cat inventory/mycluster/group_vars/k8s-cluster/k8s-cluster.yml
```

* make sure the hostnames at inventory/mycluster/hosts.yaml are good (and edit as needed)
```
cat inventory/mycluster/hosts.yaml
```

Deploy Kubespray with Ansible Playbook - run the playbook as root
The option `--become` is required, as for example writing SSL keys in /etc/,
installing packages and interacting with various systemd daemons.
Without --become the playbook will fail to run!
```
ansible-playbook -i inventory/mycluster/hosts.yaml  --become --become-user=root cluster.yml
```



# delete the cluster
delete with command from the master node:
ansible-playbook -i inventory/mycluster/hosts.yaml  --become --become-user=root reset.yml