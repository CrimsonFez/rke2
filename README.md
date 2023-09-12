RKE2
=========

A basic installer for RKE2 Kubernetes

Role Variables
--------------
```yaml
# Required
# Version of RKE2 to deploy
rke2_version: v1.25.11+rke2r1

# Allows handler to restart rke2 on the node
rke2_restart: true

# Required
# Domain of the Kube API Server on the local network
rke2_api_domain: controlplane.example.net

# Required
# IP of the Kube API Server on the local network
rke2_api_ip: 192.168.0.10

# Override the default kubernetes in-cluster domain
# Default for RKE2 is cluster.local
rke2_cluster_domain: cluster.example.net

# True when you have more than one server node, False otherwise
rke2_server_ha: false

# Required
# CNI to use
# Options as defined in the rke2 docs (docs.rke2.io): 
# - none
# - calico
# - canal
# - cilium
# - multus
rke2_cni:
- calico

# RKE2 components to disable
rke2_diable:
- rke2-ingress-nginx

# IP of the node
rke2_node_ip: 192.168.0.11

# List of kube api server arguments
rke2_kube_api_server_args:
- "oidc-issuer-url=https://auth.example.net/realms/master"
- "oidc-username-claim=sub"
- "oidc-client-id=kubernetes-cluster"
- "oidc-groups-claim=roles"
- "oidc-groups-prefix=oidc:"
- "oidc-username-prefix=oidc:"

# Disable kube proxy for when using cilium or calico eBPF
rke2_disable_kube_proxy: true

# Required
# Type of node to deploy
# Options:
# - server
# - agent
rke2_node_type: server

# Containerd Snapshotter to use
# Currently supported options in this role:
# - overlayfs
# - zfs
rke2_containerd_snapshotter: overlayfs

# Volume or device to use for the snapshotter
# For use when using ZFS or when you want to use a seperate device for overlayfs
rke2_containerd_snapshotter_volume: /dev/mapper/containerd

# Modify containerd config
rke2_containerd_device_ownership_from_security_context: true

# Defaults that shouldn't be changed
rke2_install_script_dir: /var/tmp
rke2_install_bash_url: https://get.rke2.io
rke2_data_path: /var/lib/rancher/rke2
rke2_tls_san_security: "true"
```

Example Inventory
-----------------
```ini
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
```yaml
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
```
License
-------

MIT