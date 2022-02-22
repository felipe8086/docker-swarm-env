# -*- mode: ruby -*-
# vi: set ft=ruby :
# Copyright (c) 2022 Felipe Pinto
# Licensed under the MIT License

BOX_NAME="debian/bullseye64"

NUMBER_OF_MASTERS=2
NUMBER_OF_WORKERS=3
ENABLE_REGISTRY=true

CERT_ISSUE=false

$bootstrap_script = <<SCRIPT
echo "Atualizando APT-GET"
apt-get update
echo "Instalando Curl"
apt-get install curl -y
echo "Instalando Docker"
curl -sSL https://get.docker.com/ | sh
usermod -aG docker vagrant
SCRIPT

$first_master_script = <<SCRIPT
echo "Inicializando Docker Swarm"
docker swarm init --listen-addr 192.168.33.11 --advertise-addr 192.168.33.11
echo "Gerando Tokens de Acesso ao Swarm"
docker swarm join-token worker --quiet > /vagrant/token_worker
docker swarm join-token manager --quiet > /vagrant/token_manager

echo "Instalando Portainer"
curl -L https://downloads.portainer.io/portainer-agent-stack.yml -o portainer-agent-stack.yml
docker stack deploy -c portainer-agent-stack.yml portainer
SCRIPT

Vagrant.configure("2") do |config|
  config.vm.box = BOX_NAME
  config.vm.box_download_insecure = CERT_ISSUE

  config.vm.provider "virtualbox" do |cfg|
    cfg.memory = 1024
    cfg.cpus = 1
  end

  config.vm.provision "shell", inline: $bootstrap_script, privileged: true

  (1..NUMBER_OF_MASTERS).each do |i|
    config.vm.define "master0#{i}" do |master|
      master.vm.hostname = "master0#{i}"
      master.vm.network "private_network", ip: "192.168.33.1#{i}"
      if i == 1
        master.vm.provision "shell", inline: $first_master_script
      else
        master.vm.provision "shell", inline: "docker swarm join --token `cat /vagrant/token_manager` 192.168.33.11"
      end
    end    
  end

  (1..NUMBER_OF_WORKERS).each do |i|
    config.vm.define "worker0#{i}" do |worker|
      worker.vm.hostname = "worker0#{i}"
      worker.vm.network "private_network", ip: "192.168.33.2#{i}"

      worker.vm.provision "shell", inline: "docker swarm join --token `cat /vagrant/token_worker` 192.168.33.11"
    end
  end

  if ENABLE_REGISTRY
    config.vm.define "docker-registry" do |registry|
      registry.vm.hostname = "docker-registry"
      registry.vm.network "private_network", ip: "192.168.33.31"

      registry.vm.provision "shell", inline: "docker run -d -p 5000:5000 --restart=always --name registry registry:2"
    end
  end
end
