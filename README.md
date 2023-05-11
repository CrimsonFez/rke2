RKE2
=========

A basic installer for RKE2 Kubernetes

Role Variables
--------------

## these dont need to be changed
> rke2_version: v1.24.4+rke2r1 \
> rke2_install_script_dir: /var/tmp \
> rke2_install_bash_url: https://get.rke2.io \
> rke2_data_path: /var/lib/rancher/rke2

## These should be chaged
### when set to true this configures the servers in a cluster. You must also set the rke2_first_server variable on a server
> rke2_server_ha: False

### set the token here 
> rke2_token: SuperSecretPassword!

### choosing the CNI used
> rke2_cni: 'canal'

### supported options from rke2 are:

> canal \
> calico \
> flannel \
> cilium

### the domain and ip that points to your server nodes
> rke2_api_domain: cluster.local \
> rke2_api_ip: 192.168.100.100

### disabling the kube proxy for when running eBPF based CNI like cillium or calico eBPF

> rke2_disable_kube_proxy: true

# The following variables are applied to nodes
### choose the node type
> rke2_node_type: 'server' \
or
> rke2_node_type: 'agent'


### When running HA server nodes set this on your first server. This will preform first time setup on that node.
> rke2_first_server: True

### set this on nodes you don't want to run pods
> rke2_node_taint: True

### disable integrated charts. 
> rke2_disable: 'rke2-ingress-nginx' 

Example Inventory
-----------------
```
[cluster]
master rke2_node_type=server rke2_node_taint=True
worker01 rke2_node_type=agent
worker02 rke2_node_type=agent

[ha_cluster:children]
ha_servers
agents

[ha_servers]
master01 rke2_first_server=True
master02
master03

[ha_servers:vars]
rke2_node_type=server
rke2_node_taint=True

[agents]
worker01
worker02
worker03

[agents:vars]
rke2_node_type=agent
```
Example Playbook
----------------

    - hosts: "cluster"
      roles:
        - rke2_ansible
      vars:
        rke2_server_ha: False
        rke2_token: SuperSecretPassword!
        rke2_cni: canal
        rke2_api_domain: cluster.local
        rke2_api_ip: 192.168.100.100
        rke2_disable: rke2-ingress-nginx

License
-------

MIT