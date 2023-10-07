# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.

BOX_NAME = "generic/centos7"
BOX_VERSION = "4.2.16"
SUBNET_MASK = "192.168.56."
CONTROLLER_ID = 9


Vagrant.configure("2") do |config|
  config.vm.box = BOX_NAME
  config.vm.box_version = BOX_VERSION
  config.vm.provider "virtualbox" do |vb|
    vb.cpus = 2
    vb.memory = 2048
  end

  config.vm.synced_folder ".", "/vagrant", disabled: true

  config.vm.define "ansible-controller" do |controller|
    controller.vm.network "private_network", ip:  "#{SUBNET_MASK}#{CONTROLLER_ID}"
    controller.vm.provision "shell", path: "install_ansible.sh", privileged: true
    controller.vm.synced_folder "playbooks", "/playbooks", mount_options: ["dmode=770,fmode=770"]
    controller.vm.provision 'copy_private_key', type: 'file', 
      source: '~/.ssh/id_rsa', 
      destination: '~/.ssh/id_rsa' 
    controller.vm.provision "change_private_key_mod", type: "shell",
      inline: 'chmod 600 ~/.ssh/id_rsa',
      privileged: false
  end

  (1..2).each do |i|
    config.vm.define "ansible-target-#{i}" do |target|
      target.vm.network "private_network", ip: "#{SUBNET_MASK}#{CONTROLLER_ID + i}"
    end
  end
  config.ssh.insert_key = false
  config.ssh.private_key_path = [
    '~/.ssh/id_rsa',
    '~/.vagrant.d/insecure_private_key'
  ]
  config.vm.provision 'copy_public_key', type: 'file', 
    source: '~/.ssh/id_rsa.pub', 
    destination: '~/.ssh/authorized_keys'
  
  config.vm.provision 'copy_ansible_cfg', type: 'file',
    source: 'ansible.cfg',
    destination: '~/.ansible.cfg'
end