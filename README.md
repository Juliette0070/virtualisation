(Configurer le folder .varant.d pour stocker les box. Il est mieux de mettre dans le fichier .bashrc
et faire un source .bashrc
export VAGRANT_HOME=/home/username/.vagrant.d)

fichier vagrant :

# -*- mode: ruby -*-
# vi: set ft=ruby :

DEPLOY_USER = "deploy"
DEPLOY_USER_PASSWORD = "mdp_for_deploy"

Vagrant.configure("2") do |config|

    config.vm.define "manager1" do |manager1|
        manager1.vm.box = "generic/debian10"
        manager1.vm.network "private_network", ip: "192.168.56.10"
        manager1.vm.hostname = "manager1"
        manager1.vm.provider "virtualbox" do |vb|
        # Not display the VirtualBox GUI when booting the machine.
            vb.gui = false
            vb.memory = "512"
        end
        manager1.vm.disk :disk, size: "5GB", name: "main_disk"
    end
    
    config.vm.define "manager2" do |manager2|
        manager2.vm.box = "generic/debian10"
        manager2.vm.network "private_network", ip: "192.168.56.11"
        manager2.vm.hostname = "manager2"
        manager2.vm.provider "virtualbox" do |vb|
            vb.gui = false
            vb.memory = "512"
        end
        manager2.vm.disk :disk, size: "5GB", name: "main_disk"
    end
    
    config.vm.define "worker1" do |worker1|
        worker1.vm.box = "generic/debian10"
        worker1.vm.network "private_network", ip: "192.168.56.20"
        worker1.vm.hostname = "worker1"
        worker1.vm.provider "virtualbox" do |vb|
            vb.gui = false
            vb.memory = "512"
        end
        worker1.vm.disk :disk, size: "5GB", name: "main_disk"
    end
        
     config.vm.define "worker2" do |worker1|
         worker1.vm.box = "generic/debian10"
         worker1.vm.network "private_network", ip: "192.168.56.21"
         worker1.vm.hostname = "worker2"
         worker1.vm.provider "virtualbox" do |vb|
             vb.gui = false
             vb.memory = "512"
         end
     end
        

    config.vm.provision "shell", inline: <<-SHELL
        echo "nameserver 192.168.122.1" > /etc/resolv.conf
        apt-get update
        apt-get -y install apt-transport-https ca-certificates curl gnupg-agent software-properties-common
        curl -fsSL https://download.docker.com/linux/ubuntu/gpg | apt-key add -
        add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"

        apt-get -y install docker-compose
        apt-get install python3-pip

        echo "adduser #{DEPLOY_USER} --gecos"
        adduser "#{DEPLOY_USER}" --gecos "" --disabled-password
        echo "#{DEPLOY_USER}:#{DEPLOY_USER_PASSWORD} | sudo chpasswd"
        echo "#{DEPLOY_USER}:#{DEPLOY_USER_PASSWORD}" | chpasswd

        echo "usermod -aG docker #{DEPLOY_USER}"
        usermod -aG docker "#{DEPLOY_USER}"
        sed -i 's/PasswordAuthentication no/PasswordAuthentication yes/' /etc/ssh/sshd_config
        service ssh restart

        # Create a backup of the original daemon.json
        cp /etc/docker/daemon.json /etc/docker/daemon.json.bak
        # Add or modify the insecure-registries entry in daemon.json
        echo '{ "insecure-registries": ["192.168.56.11:5000"] }' | tee -a /etc/docker/daemon.json
        # Restart Docker to apply changes
        systemctl restart docker

    SHELL
    
end



vagrant up

ssh 'deploy@192.168.56.10' ??
ssh-copy-id -i ~/.ssh/id_rsa.pub deploy@192.168.56.10
password: mdp_for_deploy

ssh 'deploy@192.168.56.10'

pour la 11:
ssh-copy-id -i ~/.ssh/id_rsa.pub deploy@192.168.56.11
password: mdp_for_deploy

...

ssh 'deploy@192.168.56.10'
mkdir src
nano src/file.py (fin tp3 -> Mon_server_redis)
(requirement.txt, dockerfile, docker-compose.yml, etc.)

docker container ls

