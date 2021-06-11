# -*- mode: ruby -*-
# vi: set ft=ruby :
#

$script = <<-SCRIPT
sudo apt install docker.io -y
sudo docker pull fastide/ubuntu:19.04
sudo docker create -ti --name fastide fastide/ubuntu:19.04 bash
sudo docker cp fastide:/home/developer /home/
sudo useradd developer
sudo usermod -aG sudo developer
echo "developer:developer" | sudo chpasswd
sudo chown -R developer /home/developer
SCRIPT

Vagrant.configure("2") do |config|

  config.vm.box = "bento/ubuntu-19.10"
  config.vagrant.plugins = ["vagrant-vbguest"]

  config.vm.box = "bento/ubuntu-19.10"
  config.vm.network "public_network"
  config.vm.synced_folder('shared', '/vagrant', type: 'rsync')

#   config.vm.provider "virtualbox" do |vb|
#     vb.gui = false
#     vb.memory = "2048"
#   end

  config.vm.provider :vmware_esxi do |esxi|

    esxi.esxi_hostname = '<exsi_hostname>'
    esxi.esxi_username = 'root'
    esxi.esxi_password = 'prompt:'

    esxi.esxi_hostport = 22

    esxi.guest_name = '${GITHUB_USERNAME}'

    esxi.guest_username = 'vagrant'
    esxi.guest_memsize = '2048'
    esxi.guest_numvcpus = '2'
    esxi.guest_disk_type = 'thin'
  end

  config.vm.provision "shell", inline: $script, privileged: true
  config.ssh.extra_args = "-tt"

end

