# GCP-Packer-Ansible-ProFTPd
Baked GCE VM with ProFTPd Server 


## notes:
  * Ubuntu 18.04 LTS defaults to Ansible 2.2 which does not have `force_apt_get` which then defaults to `Aptitude` which Ubuntu 18.04 also does not have
  * install latest Ansible in order to use `force_apt_get: yes`
  * Aptitude is not available on Ubuntu 18.04 LTS GCE image family
  * ProFTPd requires `allow_unauthenticated: yes`
  * 