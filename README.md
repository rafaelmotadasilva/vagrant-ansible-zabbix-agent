<h1>
    <a href="https://www.dio.me/">
     <img align="center" width="40px" src="https://www.ansible.com/images/project-logos/ansible-core.svg"></a>
    <span> Criando uma role do Zabbix Agent no Ansible</span>
</h1>

Repositório desenvolvido para fins educativos.

## Objetivo

Criar uma máquina virtual através de um arquivo do Vagrantfile. Configurar o provisionamento com Ansible e criar uma role do Zabbix Agent para realizar as seguintes tarefas:

- Instalar o serviço do Zabbix Agent.
- Configurar um template para o arquivo de configuração do Zabbix Agent.

## Vagrantfile

Este é um exemplo simples de `Vagrantfile`, que cria uma máquina virtual e configura o provisionamento com Ansible.

```
# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vm.box = "bento/ubuntu-20.04"
  config.vm.network "public_network"
  config.vm.provision "ansible" do |ansible|
    ansible.playbook = "playbook.yml"
  end
end
```

Se mais de uma interface de rede estiver disponível na máquina host, o Vagrant solicitará que você escolha qual interface a máquina virtual deve fazer a ponte.

## Executando o script

Após criar o Vagrantfile, você pode iniciar a máquina virtual com o comando:

```
vagrant up
```

Esse comando é fundamental no Vagrant, pois ele cria e configura as máquinas virtuais (VMs) conforme definido no seu Vagrantfile. 

## Estrutura do Projeto

Certifique-se de que seu projeto tenha a seguinte estrutura:

```
.
├── playbook.yml
├── README.md
├── roles
│   └── zabbix-agent
│       ├── files
│       │   └── zabbix-release_6.4-1+ubuntu20.04_all.deb
│       ├── tasks
│       │   └── main.yml
│       └── templates
│           └── zabbix_agent2.conf.j2
└── Vagrantfile
```

## playbook.yml

Um exemplo básico de um playbook Ansible para usar a role `zabbix-agent`:

```
---
- name: Provision Zabbix Agent
  hosts: all
  become: yes
  roles:
    - zabbix-agent
```

## roles/zabbix-agent/tasks/main.yml

Crie a role `zabbix-agent` para realizar as seguintes tarefas:

```
---
- name: Update apt cache
  apt:
    update_cache: yes

- name: Copiar o arquivo do repositório Zabbix
  copy:
    src: files/zabbix-release_6.4-1+ubuntu20.04_all.deb
    dest: /tmp/zabbix-release_6.4-1+ubuntu20.04_all.deb

- name: Instalar o pacote do repositório Zabbix
  apt:
    deb: /tmp/zabbix-release_6.4-1+ubuntu20.04_all.deb

- name: Verificar se o repositório Zabbix foi adicionado
  shell: cat /etc/apt/sources.list.d/zabbix.list
  register: zabbix_repo_check

- debug:
    var: zabbix_repo_check.stdout

- name: Update apt cache novamente
  apt:
    update_cache: yes
    cache_valid_time: 3600

- name: Instalar o Zabbix agent2
  apt:
    name: zabbix-agent2
    state: present

- name: Copiar o arquivo de configuração do Zabbix agent
  template:
    src: zabbix_agent2.conf.j2
    dest: /etc/zabbix/zabbix_agent2.conf

- name: Start e enable o Zabbix agent2
  systemd:
    name: zabbix-agent2
    state: started
    enabled: yes
```