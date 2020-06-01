# ansible-azure
Ansible playbooks for automating Azure infrastructure.

## Running on Azure CloudShell

These scripts are designed to be runnable without any need for local Ansible installation.  Just use the built-in copy of Ansible that Azure provides.  The following quick tutorial walks through doing this for the ```simple-vm``` example.

First, login to portal.azure.com and open a Cloud Shell (bash).

Checkout this repository using the built-in ```git``` client:

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

Then, just run playbooks using their own instructions.

As an example, try running the ```simple-vm``` example.

Go into the project directory, and copy the sample environment file into one that we will customize.

```
scsibug@Azure:~/clouddrive$ cd ansible-azure/simple-vm
scsibug@Azure:~/clouddrive$ cp env.yml.sample env.yml
```

Then, generate a public/private keypair so we can SSH into the server from the Cloud Shell.

```
scsibug@Azure:~/clouddrive/ansible-azure/simple-vm$ ssh-keygen
Generating public/private rsa key pair.
Enter file in which to save the key (/home/scsibug/.ssh/id_rsa):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/scsibug/.ssh/id_rsa.
Your public key has been saved in /home/scsibug/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:9EKQJCNO92uR1FClWvvamvtFAXfaJnvX1PLcQW/KPDU scsibug@cc-e70e8169-79dbcd95f5-fxwmb
The key's randomart image is:
+---[RSA 2048]----+
|  o +.==..o . .. |
| o o =.o.. o +. o|
|  .   + =   + +E*|
|       B o   B O=|
|      + S . o * *|
|     .   o . . o |
|          . .    |
|         + .     |
|        =+o      |
+----[SHA256]-----+
```

Then, output and copy the SSH public key.  This value will go into the environment YAML file our Ansible playbook will use.

```
scsibug@Azure:~/clouddrive/ansible-azure/simple-vm$ cat /home/scsibug/.ssh/id_rsa.pub
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDKovPQbir5L8Sr0CazbRUvq5YxnMTQa/B3RjwJuKYvM3C3gsny95W0S6frmq/gkxgDg838Z3W1K/czkTJ0XqdxJ9hVBqVqQqG82VX+6hxEVtFf/AMWZok25KkUn/btSLESK6VJdjOuJ13Js+piCcMdTbRIaoYnohRaMaqrzwov2GKY23Q+i95jbcSNj9DugF9taaNYgWV63s/kFQweV+RjF3hEGSuuaKp1RBJy+xO7V3amX3+DToV3EXVF1j5kz08A2xsER9vK5z9ZCE+0rzzimTncxyZoOA23Wpc0xSKhXXLpy8OP5bCHSGYzsGBKa8k930uUeDkMZCFzq8ccbcU9 scsibug@cc-e70e8169-79dbcd95f5-fxwmb
```

Edit the ```env.yml``` file to change the ```admin_user``` and ```ssh_pubkey``` variables to use our own local username and the key we generated.  Either use a linux editor like ```vi```/```emacs```, or type ```code env.yml``` to open up the graphical web editor.

```
  admin_user: scsibug
  ssh_pubkey: "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDKovPQbir5L8Sr0CazbRUvq5YxnMTQa/B3RjwJuKYvM3C3gsny95W0S6frmq/gkxgDg838Z3W1K/czkTJ0XqdxJ9hVBqVqQqG82VX+6hxEVtFf/AMWZok25KkUn/btSLESK6VJdjOuJ13Js+piCcMdTbRIaoYnohRaMaqrzwov2GKY23Q+i95jbcSNj9DugF9taaNYgWV63s/kFQweV+RjF3hEGSuuaKp1RBJy+xO7V3amX3+DToV3EXVF1j5kz08A2xsER9vK5z9ZCE+0rzzimTncxyZoOA23Wpc0xSKhXXLpy8OP5bCHSGYzsGBKa8k930uUeDkMZCFzq8ccbcU9 scsibug@cc-e70e8169-79dbcd95f5-fxwmb"
```

Now, we are ready to run Ansible and create our new resource group, VNET, public IP, and VM.

If it was successful, you should be able to remote into the VM, and check it's OS and hostname.
