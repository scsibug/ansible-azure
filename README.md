# ansible-azure
Ansible playbooks for automating Azure infrastructure.

## Running on Azure CloudShell

These scripts are designed to be runnable without any need for local
Ansible installation.  Just use the built-in copy of Ansible that
Azure provides.  The following quick tutorial walks through doing this
for the ```simple-vm``` example.

First, login to portal.azure.com and open a Cloud Shell (bash).

### Checkout the Code

Clone this repository using the built-in ```git``` client:

```
scsibug@Azure:~/clouddrive$ git clone https://github.com/scsibug/ansible-azure.git
Cloning into 'ansible-azure'...
remote: Enumerating objects: 10, done.
remote: Counting objects: 100% (10/10), done.
remote: Compressing objects: 100% (9/9), done.
remote: Total 10 (delta 0), reused 7 (delta 0), pack-reused 0
Unpacking objects: 100% (10/10), done.
Checking connectivity... done.
```

### Selecting a Playbook

Now, all the playbooks are available to run with the
```ansible-playbook``` command.  As an example, try running the
```simple-vm``` example.

Go into the project directory, and copy the sample environment file
into one that we will customize.

```
scsibug@Azure:~/clouddrive$ cd ansible-azure/simple-vm
scsibug@Azure:~/clouddrive$ cp env.yml.sample env.yml
```

### Generating SSH Keypair

For this example, we need to generate a public/private keypair so that
we can SSH into the server from the Cloud Shell.  Use hte default
suggested location, and leave the passphrase blank since this is just
a quick demonstration.

```
scsibug@Azure:~/clouddrive/ansible-azure/simple-vm$ ssh-keygen -q
Enter file in which to save the key (/home/scsibug/.ssh/id_rsa):
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Generating public/private rsa key pair.
```

### Setup Environment

Then, display and copy the SSH public key.  This value will go into
the environment YAML file our Ansible playbook will use, eventually
being placed on the new VM and allowing us to login without having to
use a password.

```
scsibug@Azure:~/clouddrive/ansible-azure/simple-vm$ cat /home/scsibug/.ssh/id_rsa.pub
ssh-rsa AAAAB <..snip..> scsibug@cc-e70e8169-79dbcd95f5-fxwmb
```

Edit the ```env.yml``` file to change the ```admin_user``` and
```ssh_pubkey``` variables to use our own local username and the key
we generated.  Either use a linux editor like ```vi```/```emacs```, or
type ```code env.yml``` to open up the graphical web editor.  Be sure
to use the exact same value for ```admin_user``` as the CloudShell
displays for your user, otherwise the ```ssh``` command will not work
without that being specified as well.

```
  admin_user: scsibug
  ssh_pubkey: "ssh-rsa AAAAB <..snip..> scsibug@cc-e70e8169-79dbcd95f5-fxwmb"
```

### Running the Playbook

Now, we are ready to run Ansible and have it create our new resource
group, virtual network, public IP, network security group, and VM and
associated disk.  These will be created within the default
subscription.

Ansible is pre-installed, and the playbook is configured to read from
the ```env.yml`` file we edited.

```
scsibug@Azure:~/clouddrive$ ansible-playbook provision.yml
PLAY [Create Small Azure Dev VM] ***********************************************
<..snip..>
TASK [Show public IP for VM which will be created] *****************************
ok: [localhost] => {
    "msg": "The public IP is 40.124.26.110."
}
PLAY RECAP *********************************************************************
localhost                  : ok=9    changed=7    unreachable=0    failed=0
skipped=0    rescued=0    ignored=0   
```

Note the IP address that was reported, since that is now connected to our server.

If it was successful, you should be able to remote into the VM, and run commands.

```
% ssh 40.124.26.110
The authenticity of host '40.124.26.110 (40.124.26.110)' can't be established.
ECDSA key fingerprint is SHA256:eSPuviKC/JyCpwvVTwL8e3oO/YslYp2gJfNgeZ+sUb4.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '40.124.26.110' (ECDSA) to the list of known hosts.
Activate the web console with: systemctl enable --now cockpit.socket

[scsibug@centos-vm ~]$ hostname
centos-vm
```

### Tearing Down

Once finished, run the teardown playbook to clean up the resources we created.

```
% ansible-playbook teardown.yml 
PLAY [Destroy Small Azure Dev VM] **********************************************
<..snip..>
PLAY RECAP *********************************************************************
localhost                  : ok=2    changed=1    unreachable=0    failed=0
skipped=0    rescued=0    ignored=0   
```

