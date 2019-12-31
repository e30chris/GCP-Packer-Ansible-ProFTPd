# GCP-Packer-Ansible-ProFTPd

Baked GCE VM with ProFTPd Server 

## Building the Bakery GCE VM

There are two methods to run the GCE Bakery on GCP.

### Using GCP Cloud Build

  pros:
    * as-a-service convience
    * most of the wiring is done for you
  cons:
    * runs from a GCP Project owned by GCP requiring access to your GCP Project from a public IP
      * _traffic originating within the GCP network, addressed to the GCP network, does not leave Google fiber and therefore never traverses the public internet_
    * pushes the complexity into building the many steps needed to pull Packer and Ansible down into Cloud Build environment, and then feed the files into each

### Using a GCE VM running in a GCP "Bakery" Project

  pros:
    * uses the default GCE Compute Service Account so no needed `.json` files containing secrets to manage
    * traffic never leaves your GCP Project
    * outbound internet traffic can be segmented to this isolated GCP Project containing the GCE Bakery
    * files for Packer and Ansible can be pulled locally from Git
  cons:
    * requires Packer and Ansible to be installed on the GCE VM
    * requires the GCE Bakery VM to be managed
    * chicken and egg requires the GCE Bakery VM to be built manually

This repo assumes method #2.

To build the GCE Bakery VM, run the following API calls to create a GCE VM with API scopes to then build any GCE VM in the bakery.  Change any flags to match your infrastructure requirements.

```
gcloud compute instances create gce-bakery \
--image-family ubuntu-1804-lts \
--image-project gce-uefi-images \
--network projects/smc-bakery/global/networks/default \
--zone us-west1-c \
--scopes "https://www.googleapis.com/auth/compute,https://www.googleapis.com/auth/devstorage.full_control"
```

## notes:

  * Ubuntu 18.04 LTS defaults to Ansible 2.2 which does not have `force_apt_get` which then defaults to `Aptitude` which Ubuntu 18.04 also does not have
  * install latest Ansible 2.5 in order to use `force_apt_get: yes`
  * Aptitude is not available on Ubuntu 18.04 LTS GCE image family and therefore the `force_apt_get: yes` Ansible call must be used for all `apt` runs
  * ProFTPd requires `allow_unauthenticated: yes`
