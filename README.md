# Infrastructure-as-Code-IaC-
Infrastructure as Code (IaC)

## What is IaC
IaC stands for Infrastructure as Code, which is an approach to managing infrastructure using code rather than manual processes. With IaC, developers can write code to define and manage infrastructure resources such as servers, networks, and storage, just like they would write code for applications.

## Why do we use IaC

We use IaC to automate the process of infrastructure management and make it easier to deploy, scale, and manage infrastructure resources. By using code to define infrastructure, we can achieve greater consistency, repeatability, and reliability compared to manual processes.

## Where do we use IaC

We use IaC in a variety of contexts, including cloud computing, data centers, and containerized environments. It is particularly useful in cloud computing because of the dynamic nature of cloud resources and the need to rapidly provision and de-provision infrastructure.

## When do we use IaC

We use IaC whenever we need to create, update, or manage infrastructure resources. This includes tasks such as deploying new applications, scaling up or down resources, configuring network settings, and setting up security policies.

## How do we use IaC

To use IaC, we typically write code using a programming language or a specialized tool such as Terraform, Ansible, or CloudFormation. The code defines the desired state of infrastructure resources, and we use tools to apply the code to the target environment, creating, updating, or deleting resources as necessary. The IaC code can also be version-controlled, tested, and audited, just like application code, to ensure the desired state of the infrastructure is maintained over time.

# Using Ansible with VMs

- Create a project locally called `Iacansible` Ansible and add a vagrantfile to the directory.
- Open the folder in Visual Studio Code and make sure the vagrant file is in ruby.
- Copy the following configuration within your Vagrantfile:

``` 
# ansible-tech201


# -*- mode: ruby -*-
 # vi: set ft=ruby :
 
 # All Vagrant configuration is done below. The "2" in Vagrant.configure
 # configures the configuration version (we support older styles for
 # backwards compatibility). Please don't change it unless you know what
 
 # MULTI SERVER/VMs environment 
 #
 Vagrant.configure("2") do |config|
 # creating are Ansible controller
   config.vm.define "controller" do |controller|
     
    controller.vm.box = "bento/ubuntu-18.04"
    
    controller.vm.hostname = 'controller'
    
    controller.vm.network :private_network, ip: "192.168.33.12"
    
    # config.hostsupdater.aliases = ["development.controller"] 
    
   end 
 # creating first VM called web  
   config.vm.define "web" do |web|
     
     web.vm.box = "bento/ubuntu-18.04"
    # downloading ubuntu 18.04 image
 
     web.vm.hostname = 'web'
     # assigning host name to the VM
     
     web.vm.network :private_network, ip: "192.168.33.10"
     #   assigning private IP
     
     #config.hostsupdater.aliases = ["development.web"]
     # creating a link called development.web so we can access web page with this link instread of an IP   
         
   end
   
 # creating second VM called db
   config.vm.define "db" do |db|
     
     db.vm.box = "bento/ubuntu-18.04"
     
     db.vm.hostname = 'db'
     
     db.vm.network :private_network, ip: "192.168.33.11"
     
     #config.hostsupdater.aliases = ["development.db"]     
   end
 
 
 end
```

- In VSC you can now use a Git terminal to use `vagrant up` to create your VMs (make sure you have enough space on your local machine for this ,and you don't run out of space whilst doing this)
- We need to now SSH into each machine and establish the connection to the internet from our local machine.
- To ssh we use `vagrant ssh <VM name>`
- Once in our VMs:
```
sudo apt-get update -y 

sudo spt-get upgrade -y
```

- Next we need to get ansible on our controller VM:

```
sudo apt-get install software-properties-common

sudo apt-add-repository ppa:ansible/ansible

sudo apt-get update -y 

sudo apt-get install ansible -y

ansible --version

cd etc/ansible
```
- These are the steps needed to install and update ansible

- Now that we have ansible we want to test that we have connection between our Vms. we are going to test if we can ssh into our db and web VM and also if we can ping them
- To test if we ssh into our web/ db Vms from our Controller we need to run the command:
```
ssh vagrant@<ip> # the ip adressess are prset in the vagrantfile
```
- Now in order to ping the other VMs we use:

```
sudo ansible -m ping <???> # ??? = all/web db . whereever you want to ping

#or

sudo ansible -m ping --ask-vault-pass
```

- You will most likely recsize an error:
```
192.168.33.10 | FAILED! => {
 "msg": "Using a SSH password instead of a key is not possible because Host Key checking is enabled and sshpass does not support this. Please add this host's fingerprint to your known_hosts file to manage this host."
}
```
- We need to no change the host files `sudo nano hosts # should be in ansible folder` in this file we want to add our web and db username and password

![img.png](img.png)

- we also need to edit out `ansible.cfg` file and add this code under the defaults section:
```
host_key_checking = false
```
