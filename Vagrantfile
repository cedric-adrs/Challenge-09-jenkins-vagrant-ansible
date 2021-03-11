BOX_IMAGE = "ubuntu/bionic64" 

$provisionAnsible = <<-SCRIPT
#!/bin/bash -x
sudo apt update -y
   
#  Install ansible from Ubunto default repository
echo "Installing Ansible on the Ansible Host.... "
echo 
sudo apt install ansible -y    
		   
echo " ======  Initializing  etc/hosts the Ansible Host.... "
echo
echo
echo "
192.168.100.10 localhost 
192.168.100.11 server-staging  server-staging.businesstraining.be 
192.168.100.12 server-production  server-production .businesstraining.be"  >> /etc/hosts

echo " ======  Migrating SSH Keys and Inventory file to the user profile.... "
echo
echo "cp -R /vagrant/.vagrant/  /home/vagrant/" >> /home/vagrant/.profile
echo "chmod -R 700  /home/vagrant/.vagrant" >> /home/vagrant/.profile
echo "cp -p /vagrant/hosts  /etc/ansible/" >> /home/vagrant/.profile

SCRIPT



Vagrant.configure("2") do |config|
   config.vm.synced_folder ".", "/vagrant", type: "virtualbox"
 
   # ===========   Jenkins Master VM Config ==================
    config.vm.define "server-jenkins" do |subconfig|
    subconfig.vm.box = BOX_IMAGE
    subconfig.vm.hostname = "server-jenkins"
    subconfig.vm.network "forwarded_port", guest: 8080, host: 8080  # PORT for Jenkins
    subconfig.vm.network "forwarded_port", guest: 9000, host: 9000  # PORT for SonarQube
    subconfig.vm.network "forwarded_port", guest: 8081, host: 8081  # PORT for Nexus
    subconfig.vm.network :private_network, ip: "192.168.100.10"
	subconfig.vm.provider "virtualbox" do |vb|
         # Customize the amount of memory on the VM:
         vb.name = "server-jenkins"
         vb.cpus = 2
	     vb.memory = "4096"
    end
    subconfig.vm.provision "shell", inline: $provisionAnsible  
  end
  # ===========   Staging TomcatVM Config ==================
  config.vm.define "server-staging" do |subconfig|
    subconfig.vm.box = BOX_IMAGE
    subconfig.vm.hostname = "server-staging"
    subconfig.vm.network "forwarded_port", guest: 8080, host: 9090
    subconfig.vm.network :private_network, ip: "192.168.100.11"
	subconfig.vm.provider "virtualbox" do |vb|
         # Customize the amount of memory on the VM:
         vb.name = "server-staging"
         vb.cpus = 1
	     vb.memory = "1024"
    end
    subconfig.vm.provision "shell", inline:  "sudo apt update -y"
  end

# ===========   Production Tomcat VM Config ==================
config.vm.define "server-production" do |subconfig|
    subconfig.vm.box = BOX_IMAGE
    subconfig.vm.hostname = "server-production"
    subconfig.vm.network "forwarded_port", guest: 8080, host: 9080
    subconfig.vm.network :private_network, ip: "192.168.100.12"
	subconfig.vm.provider "virtualbox" do |vb|
         # Customize the amount of memory on the VM:
         vb.name = "server-production"
         vb.cpus = 1
	     vb.memory = "1024"
    end
    subconfig.vm.provision "shell", inline:  "sudo apt update -y" 
  end
 
end

