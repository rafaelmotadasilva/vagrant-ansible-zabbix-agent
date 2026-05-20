# Zabbix Agent com Vagrant e Ansible

Provisionamento automatizado de uma VM com Vagrant e configuração do Zabbix Agent via role Ansible — do zero ao agente monitorando em minutos.

## Stack

- **Vagrant** — criação e gerenciamento da VM (Ubuntu 20.04)
- **Ansible** — provisionamento e configuração via role
- **Zabbix Agent** — agente de monitoramento instalado e configurado

## Estrutura

```
.
├── Vagrantfile          # Define a VM e aciona o Ansible
├── playbook.yml         # Playbook principal
└── roles/
    └── zabbix-agent/
        ├── tasks/       # Tarefas de instalação e configuração
        └── templates/   # Template do arquivo de configuração do agente
```

## Pré-requisitos

- [Vagrant](https://www.vagrantup.com/)
- [VirtualBox](https://www.virtualbox.org/)
- [Ansible](https://docs.ansible.com/ansible/latest/installation_guide/)

## Como usar

```bash
git clone https://github.com/rafaelmotadasilva/vagrant-ansible-zabbix-agent.git
cd vagrant-ansible-zabbix-agent

vagrant up
```

O Vagrant cria a VM e aciona automaticamente o Ansible para:

1. Instalar o Zabbix Agent
2. Aplicar o template de configuração com as definições do servidor Zabbix

## Configuração

Antes de subir a VM, edite o template de configuração na role para apontar para o seu servidor Zabbix:

```
roles/zabbix-agent/templates/zabbix_agentd.conf.j2
```

Ajuste a variável `ZBX_SERVER_HOST` com o IP ou hostname do seu Zabbix Server.

## Parar e destruir a VM

```bash
vagrant halt    # Para a VM
vagrant destroy # Remove a VM completamente
```

## Referências

- [Documentação do Vagrant](https://developer.hashicorp.com/vagrant/docs)
- [Documentação do Ansible](https://docs.ansible.com/)
- [Zabbix Agent — instalação](https://www.zabbix.com/documentation/current/en/manual/installation/install_from_packages)
