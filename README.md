# docker-swarm-env

Esse é um Vagrantfile para criação de um ambiente de experimentação com Docker Swarm e Portainer.

## Pré-requisitos
* VirtualBox - https://www.virtualbox.org/wiki/Downloads
* Vagrant - https://www.vagrantup.com/downloads

## Utilização
Faça o clone do repositório:

     git clone https://github.com/felipe8086/docker-swarm-env.git

Execute o comando abaixo para iniciar o processo de criação e provisionamento das máquinas virtuais.

    cd docker-swarm-env
    vagrant up

Ao término do processo, verifique se todas as máquinas estão rodando.

    vagrant status

Se todos os passos anteriores executarem com sucesso, será possível acessar o portal de administração do Portainer através do endereço abaixo:

    https://192.168.33.11:9443/

## Configuração
Serão criados por padrão, dois nós masters, três nós workers e um docker-registry (standalone). Porém essas configurações podem ser alteradas através dos parâmetros abaixo:

    NUMBER_OF_MASTERS=2
    NUMBER_OF_WORKERS=3
    ENABLE_REGISTRY=true