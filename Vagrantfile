# -*- mode: ruby -*-
# vi: ft=ruby

# file   : Vagrantfile
# purpose: deploy redhat do407 study environment
#
# author : harald van der laan
# date   : 2017/06/07
# version: v1.0

$ansible = <<SCRIPT
sudo yum -y upgrade
sudo yum install -y python-devel python-crypto
sudo yum install -y epel-release
sudo yum --enablerepo=epel -y install sshpass
curl https://bootstrap.pypa.io/get-pip.py > get-pip.py
sudo -H python get-pip.py
sudo -H pip install pip --upgrade
sudo -H pip install ansible --upgrade
sudo echo "172.16.20.151 proxy proxy.brakkedoos.nl" >> /etc/hosts
sudo echo "172.16.20.152 web web.brakkedoos.nl" >> /etc/hosts
sudo echo "172.16.20.153 database database.brakkedoos.nl" >> /etc/hosts
mkdir ansible
echo "[all:vars]" > ansible/inventory
echo "ansible_ssh_user=vagrant" >> ansible/inventory
echo "ansible_ssh_pass=vagrant" >> ansible/inventory
echo "" >> ansible/inventory
echo "[database]" >> ansible/inventory
echo "database.brakkedoos.nl" >> ansible/inventory
echo "" >> ansible/inventory
echo "[proxy]" >> ansible/inventory
echo "proxy.brakkedoos.nl" >> ansible/inventory
echo "" >> ansible/inventory
echo "[web]" >> ansible/inventory
echo "web.brakkedoos.nl" >> ansible/inventory
echo "" >> ansible/inventory
echo "[centos:children]" >> ansible/inventory
echo "database" >> ansible/inventory
echo "proxy" >> ansible/inventory
echo "web" >> ansible/inventory
sudo chown -R vagrant:vagrant ansible/
sudo ifup eth1
SCRIPT

$clients = <<SCRIPT
sudo sed -i 's/#PasswordAuthentication yes/PasswordAuthentication yes/g' /etc/ssh/sshd_config
sudo sed -i 's/PasswordAuthentication no/#PasswordAuthentication no/g' /etc/ssh/sshd_config
sudo systemctl restart sshd
sudo ifup eth1
SCRIPT


VAGRANTFILE_API_VERSION = "2"
Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
	config.vm.define "proxy" do |proxy|
		proxy.vm.box = "centos/7"
		proxy.vm.hostname = "proxy.brakkedoos.nl"
		proxy.vm.network "private_network", ip: "172.16.20.151"

		proxy.vm.provider "virtualbox" do |vbox|
			vbox.customize [
				"modifyvm", :id,
				"--memory", 512,
				"--cpus", 1,
				"--name", "proxy",
			]
		end
		proxy.vm.provision "shell", inline: $clients
	end

	config.vm.define "web" do |web|
		web.vm.box = "centos/7"
		web.vm.hostname = "web.brakkedoos.nl"
		web.vm.network "private_network", ip: "172.16.20.152"

		web.vm.provider "virtualbox" do |vbox|
			vbox.customize [
				"modifyvm", :id,
				"--memory", 512,
				"--cpus", 1,
				"--name", "web",
			]
		end
		web.vm.provision "shell", inline: $clients
	end

	config.vm.define "database" do |db|
		db.vm.box = "centos/7"
		db.vm.hostname = "database.brakkedoos.nl"
		db.vm.network "private_network", ip: "172.16.20.153"

		db.vm.provider "virtualbox" do |vbox|
			vbox.customize [
				"modifyvm", :id,
				"--memory", 512,
				"--cpus", 1,
				"--name", "database",
			]
		end
		db.vm.provision "shell", inline: $clients
	end

	config.vm.define "ansible" do |ansible|
		ansible.vm.box = "centos/7"
		ansible.vm.hostname = "ansible.brakkedoos.nl"
		ansible.vm.network "private_network", ip: "172.16.20.150"

		ansible.vm.provider "virtualbox" do |vbox|
			vbox.customize [
				"modifyvm", :id,
				"--memory", 512,
				"--cpus", 1,
				"--name", "ansible",
			]
		end
		ansible.vm.provision "shell", inline: $ansible
	end
end
