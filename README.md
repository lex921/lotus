# lotus

### lotus is a kubespray extend use to deploy k8s binary use kubepray config and kubernetes-defaults default values;

### copy lotus to the kubespray roles dir and copy lotus-k8s.yml and lotus.yml to the kubespray base dir.
```yaml
---
etcd_deployment_type: host  ## etcd deployment change to host
download_container: false
download_localhost: true
download_run_once: true
helm_enabled: true
kube_network_plugin: calico
calico_datastore: etcd
kube_apiserver_client_cert: "{{ kube_cert_dir }}/ca.pem"
kube_apiserver_client_key: "{{ kube_cert_dir }}/ca-key.pem"
#http_proxy: 'http://192.168.50.137:1081' ## define you  http_proxy to down images and binary to the **/tmp**
#https_proxy: "{{ http_proxy }}"
deploy_netchecker: false ## disable netchecher and you can enable
dns_min_replicas: 1

kube_apiserver_endpoint: |-
  {% if loadbalancer_apiserver is defined -%}
      https://{{ apiserver_loadbalancer_domain_name }}:{{ loadbalancer_apiserver.port|default(kube_apiserver_port) }}
  {%- elif not is_kube_master and loadbalancer_apiserver_localhost -%}
      https://localhost:{{ loadbalancer_apiserver_port|default(kube_apiserver_port) }}
  {%- else -%}
      https://{{ first_kube_master }}:{{ kube_apiserver_port }}
  {%- endif %}
 # if you define kube_loadbalancer_apiserver  will be use here
gcr_image_repo: "registry.cn-hangzhou.aliyuncs.com"
containerd_registries:
  "docker.io":
    - "https://registry-1.docker.io"
  "gcr.io":
    - "https://registry.cn-hangzhou.aliyuncs.com"  
kube_loadbalancer_apiserver:
  domain_name: ""  # use domain name not vip  this domian will be assign kube-apiserver cert so if you change kube-apiserver vip will not assign  apiserver agagin example **'lotus.io'**
  address: ""  #this is the apiserver vip will be use kube_lb role and binding  ${domain_name} ${address} in the hosts in all hosts in invertory
# TODO harbor ha
kube_loadbalancer_harbor: ## to doing
  domain_name: ""
  address: ""
```
### how to use
```bash
ansible-playbook -i inventory.ini lotus-k8s.yml -e@lotus.yml
```
### any other config you can see kubespray 

