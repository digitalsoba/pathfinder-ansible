# Learning Ansible
Vagrant enables users to create and configure lightweight, reproducible, and portable development environments. Virtualbox is the default hypervisor for Vagrant as it is free an open source. We will use Vagrant to create a VM that Ansible will run against

Ansible is a configuration management tool that allows us to automate infrastructure tasks. 

## Requirements
  - Virtualbox
    - VBox guest addons
  - Vagrant
  - Ansible
  - Or Docker

## Installing Virtualbox
To install Virtualbox [here](https://www.virtualbox.org/wiki/Downloads) their download page and install it on your respective OS. Also, install the `VirtualBox 6.0.4 Oracle VM VirtualBox Extension Pack` after installing Virtualbox (This will help Vagrant)

## Installing Vagrant
Download the Vagrant binary [here](https://www.vagrantup.com/downloads.html)

## How it works
Vagrant reads a Vagrantfile that will bootstrap the creation of a VM. Vagrant uses a base image they call boxes to create these VM. Inside the Vagrantfile contains syntax we declare for the machine such a private IP address and provisioners. For our case, we will provision with Ansible.

```shell
# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  # Using Ubuntu 18.04 bionic
  config.vm.box = "ubuntu/bionic64"

  # Use IP private IP address 192.168.33.10
  config.vm.network "private_network", ip: "192.168.33.10"

  # Use ansible as VM provisioner
  config.vm.provision "ansible" do |ansible|
    # Use playbook.yml file to provision
    ansible.playbook = "playbook.yml"
    # Use Python3 
    ansible.extra_vars = { ansible_python_interpreter:"/usr/bin/python3" }
  end # End provisioner
end # End Vagrantfile
```

## Getting Started
In your terminal run the command `vagrant init ubuntu/bionic64`. This will generate a default Vagrantfile. Run `vagrant up` to start the machine. This will download the Ubuntu 18.04 Bionic box and create a VM in VirtualBox. To verify the box is up, run `vagrant status` or view Virtualbox to see the VM.

From here we can run `vagrant ssh` to login into the machine and run commands. Update the machine and install apache2. `sudo apt update && sudo apt install -y apache2`. Curl your localhost, what do you get? Exit the VM by typing `exit`. You can not destroy the machine by using the `vagrant destroy` 

## Ansible labs
Vagrant abstracts the process for creating a VM so we won't have access to directly ssh into the machine, this is why we use `vagrant ssh`. Because of this, we can't directly run an ansible playbook with `ansible-playbook playbook.yml` against the machine, instead, we'll use `vagrant provision`. 


You can also use Docker to run these labs in a standalone container, this requires ansible to run against itself. You can run the `ubuntu:latest` image. For the lab you can run the command `docker run -it ubuntu: latest bash` to run a ubuntu container. If you using docker you must update your container and install ansible. Change hosts to `hosts: localhost` and add `connection: local` to the `.yml` file. Sample files for docker can be viewed in the ansible-lab-docker folder. Once finished with a lab you can exit the container or continue with the same container. If you're asked to visit a page on your browser, use [localhost](http://localhost)

To get started, clone this repo with `git clone https://github.com/digitalsoba/pathfinder-ansible.git`. In the terminal, navigate to each lab and complete the challenges. 

### Lab 1 - Creating a user
Create a playbook that will update and upgrade apt packages. After creating a user name pathfinder. Create a home directory for pathfinder, add them to the sudo group, and set bash as their default shell. Create a file name hello.txt inside pathfinders directory

Tasks:
  - Update packages
  - Upgrade packges
  - Create a pathfinder user
    - Create a home directory
    - Set /bin/bash as default shell
    - Add them to sudo group
    - Create a hello.txt file inside pathfinders home directory

In the machine, change users to pathfinder and verify you are logged in as them pathfinder with `whoami` and change directory into your home. Do you see the hello.txt file?

### Lab 2
Install a LAMP stack with Ansible. This should include Apache2, Mysql-client, and PHP. 
Tasks: 
  - Update apt cache
  - Install 
    - apache2
    - mysql-client
    - php
  - Start apache2 service

To verify installation run 
  - `php --version`
  - `curl localhost`

### Lab 3
Create a index.php page like this on your local filesystem.
```html
<?php
phpinfo();
?>
```
Tasks: 
- Install LAMP Stack
- Start Apache2 service
- Create index.php page locally
- Remove index.html from ansible node
  - Note the default web directory for apache
- Copy index.php into ansible node

Visit your web browser and verify the php info page is shown correctly

### Lab 4
Create roles to add a pathfinder user and install a lamp stack. Create another role to install [Laravel](https://laravel.com/docs/5.8#installation) dependencies such as the required php modules, composer, and apache modifications. Create a final role to deploy a laravel project and serve it on localhost. 