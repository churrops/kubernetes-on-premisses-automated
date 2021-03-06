![Kubernetes](img/images.png)

# Create Cluster Kubernetes - On Premises
**Can be deployed on KVM, VirtualBox, VMware or OpenStack**

### Supported Linux Distributions
    
   * CentOS/RHEL 7
   * Ubuntu 16
    
### Installation

To deploy the cluster you can use:

   * Ansible 2.6.1
   * Python version 2.7.12 
    
### Supported Components

   * etcd v3.3.8
   * docker 18.06.0-ce 
   * keepalived v1.3.5
   * kubernetes v1.11.1
   * weave v2.4.0
    
### Step 1 - Configure file vars/main.yml

- Set the environment variables

file: inventory/sample/group_vars/all 

```bash
---
# certificate cfssl
expiry: 24000h
corp: kubernetes
cfssl: /etc/cfssl

# ips (do not change)
ips-etcd: "{{groups['etcd']|join(',')}}"
ips-master: "{{groups['master']|join(',')}}"
ips-worker: "{{groups['worker']|join(',')}}"

# kubernetes
k8sversionUbuntu: 1.11.1-00
k8sversionRedhat: 1.11.1-0*
podSubnet: 10.0.0.0/16
# create TOKEN - run command
# python -c 'import random; print "%0x.%0x" % (random.SystemRandom().getrandbits(3*8), random.SystemRandom().getrandbits(
  #8*8))'
admission_token: 3e6035.191009b3012b14db

# etcd
etcdversion: v3.3.8
etcd: /etc/etcd
etcdstorage: /var/lib/etcd-cluster

# keepalived
interfacename: ens32 # to change
priority: 51
virtual_router_id: "{{ 100 | random }}"
keeppass: p7S5gkT719R
state: MASTER
virtualip: 192.168.15.230 # to change
```

### Step 2 - Inventory 

- open file hosts 
- add ips or fqdn to etcd, master and worker.

    * number minimum: 3 nodes for etcd
    * number minimum: 2 nodes for master
    * number minimum: 1 node for worker
 
Example:

file: inventory/sample/hosts

```bash
[etcd]
192.168.56.111
192.168.56.112
192.168.56.113

[master]
192.168.56.111
192.168.56.112
192.168.56.113

[worker]
192.168.56.114
192.168.56.115

[all:vars]
ansible_ssh_user=root
ansible_ssh_private_key_file=~/kubernetes.pem
```

### Step 3 - Executing playbook Ansible

```bash
ANSIBLE_HOST_KEY_CHECKING=False ansible-playbook -i inventory/sample/hosts pb_IntallCluster.yml
```

### Display Dashboard Kubernetes

http://IP-VIP:30000
![Dashboard Kubernetes](img/dashboard1.png)

### Display Weave Scope

http://IP-VIP:30001
![Weave Scope](img/weave.png)

<hr>

## NOTE:

### Destroy Cluster Kubernetes 

```bash
ANSIBLE_HOST_KEY_CHECKING=False ansible-playbook -i inventory/sample/hosts pb_IntallCluster.yml
```
