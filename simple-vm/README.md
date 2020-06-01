# simple-vm
Simple playbooks to provision and teardown Azure virtual machines.

## About

Based off the guide for configuring VMs in Azure with Ansible:
https://docs.microsoft.com/en-us/azure/developer/ansible/vm-configure

With the following improvements:

 * Added a deprovisioning playbook
 * Externalize variables
 * Use managed disks
 * Default to CentOS 8, on a smaller VM

## Usage

Customize environment settings (preferred resource names, region, and
SSH public key) in the ```env.yml``` file (```env.yml.sample```
provided as a template).

Run the ```ansible-playbook provision.yml``` to create a resource
group containing the VNet, subnet, public IP, network security group,
VM, and managed disk.

Run ```ansible-playbook teardown.yml``` to delete the resource group,
and all its member resources.

## Provision New Server

Example of expected playbook output during server provisioning from
scratch.

```
$ ansible-playbook provision.yml
[WARNING]: No inventory was parsed, only implicit localhost is available
[WARNING]: provided hosts list is empty, only localhost is available. Note that
[WARNING]: the implicit localhost does not match 'all'

PLAY [Create Small Azure Dev VM] ***********************************************

TASK [Gathering Facts] *********************************************************
ok: [localhost]

TASK [Create resource group] ***************************************************
changed: [localhost]

TASK [Create virtual network] **************************************************
changed: [localhost]

TASK [Add subnet] **************************************************************
changed: [localhost]

TASK [Create public IP address] ************************************************
changed: [localhost]

TASK [Show public IP for VM which will be created] *****************************
ok: [localhost] => {
    "msg": "The public IP is 40.124.26.110."
}

TASK [Create Network Security Group that allows SSH] ***************************
changed: [localhost]

TASK [Create virtual network interface card] ***********************************
[DEPRECATION WARNING]: Setting ip_configuration flatten is deprecated and will
be removed. Using ip_configurations list to define the ip configuration. This
feature will be removed in version 2.9. Deprecation warnings can be disabled by
setting deprecation_warnings=False in ansible.cfg.
changed: [localhost]

TASK [Create VM] ***************************************************************
[WARNING]: Module did not set no_log for ssh_password_enabled
changed: [localhost]

PLAY RECAP *********************************************************************
localhost                  : ok=9    changed=7    unreachable=0    failed=0
skipped=0    rescued=0    ignored=0   
```

## Using the VM

Using a host with the corresponding SSH private key available, login
to the newly provisioned server, and check the hostname.

```
% ssh admin@40.124.26.110
The authenticity of host '40.124.26.110 (40.124.26.110)' can't be established.
ECDSA key fingerprint is SHA256:eSPuviKC/JyCpwvVTwL8e3oO/YslYp2gJfNgeZ+sUb4.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '40.124.26.110' (ECDSA) to the list of known hosts.
Activate the web console with: systemctl enable --now cockpit.socket

[admin@centos-vm ~]$ hostname
centos-vm
```

## Teardown

Example of expected playbook output during teardown of a running server.

```
% ansible-playbook teardown.yml 
[WARNING]: No inventory was parsed, only implicit localhost is available
[WARNING]: provided hosts list is empty, only localhost is available. Note that
the implicit localhost does not match 'all'

PLAY [Destroy Small Azure Dev VM] **********************************************

TASK [Gathering Facts] *********************************************************
ok: [localhost]

TASK [Remove resource group] ***************************************************
changed: [localhost]

PLAY RECAP *********************************************************************
localhost                  : ok=2    changed=1    unreachable=0    failed=0
skipped=0    rescued=0    ignored=0   
```
