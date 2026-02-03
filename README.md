## **Ambiente de Desenvolvimento – Gitea + CI/CD com Docker e Vagrant**

Este projeto provisiona um **ambiente completo de Git + CI/CD self-hosted**, utilizando **Vagrant**, **Docker**, **Docker Compose**, **Gitea**, **PostgreSQL** e **Gitea Actions Runner**.

O objetivo é fornecer uma stack local que simula um ambiente real de CI/CD, permitindo versionamento de código, execução de pipelines e testes automatizados.

---

## **Tecnologias Utilizadas**

* **Vagrant** – Provisionamento de máquina virtual
* **Docker** – Conteinerização dos serviços
* **Docker Compose** – Orquestração dos containers
* **Gitea** – Servidor Git self-hosted
* **PostgreSQL** – Banco de dados do Gitea
* **Gitea Actions (act\_runner)** – Runner de CI/CD
* **Node.js (via containers)** – Execução dos pipelines

---

## **Arquitetura da Stack**

`[Vagrant VM]`  
   `|`  
   `├── Docker`  
   `│    ├── gitea (porta 3000)`  
   `│    ├── postgres`  
   `│    └── gitea-runner`  
   `|`  
   `└── Docker Network (bridge)`

### **Serviços**

| Serviço | Função |
| ----- | ----- |
| **Gitea** | Interface web, repositórios Git e Actions |
| **PostgreSQL** | Persistência dos dados do Gitea |
| **Gitea Runner** | Execução dos workflows CI/CD |
| **Volumes Docker** | Persistência de dados |

---

## **Estrutura do Projeto**

`gitea-exemple/`  
`├── docker-compose.yml`  
`├── Vagrantfile`  
`├── README.md`

---

## **sPré-requisitos**

Na máquina host:

* Vagrant
* VirtualBox ou libvirt
* Git

Dentro da VM (automático ou manual):

* Docker
* Docker Compose (`docker-compose` clássico)

---

## **Subindo o Ambiente com Vagrant**

Na raiz do projeto:

`vagrant up`

Acesse a VM:

`vagrant ssh`

Entre no diretório do projeto:

`cd ~/gitea`

---

## **Subindo os Serviços com Docker Compose**

Dentro da VM:

`sudo docker-compose up -d`

Isso irá subir:

* PostgreSQL
* Gitea ([http://localhost:3000](http://localhost:3000))
* Gitea Actions Runner

---

## **Acessando o Gitea**

No navegador da máquina host:

`http://localhost:3000`

Na primeira execução:

* Crie o usuário administrador
* Crie seus repositórios
* Gere o **token de registro do runner**

---

## **Gitea Actions Runner**

O runner utiliza:

* **Docker socket** (`/var/run/docker.sock`)
* Execução de jobs em containers
* Labels compatíveis com `ubuntu-latest`

### **Exemplo de labels configuradas:**

`GITEA_RUNNER_LABELS=ubuntu-latest:docker://node:18-bullseye`

Isso permite pipelines Node.js sem precisar instalar nada na VM.

---

## **Registro do Runner**

No `docker-compose.yml`, substitua:

`GITEA_RUNNER_REGISTRATION_TOKEN=SEU_TOKEN`

Pelo token gerado em:

`Gitea → Site Admin → Actions → Runners`

Após isso, reinicie o runner:

`sudo docker-compose restart runner`

---

## **Parar e Subir Novamente o Ambiente**

Parar os containers **sem perder dados**:

`sudo docker-compose down`

Subir novamente:

`sudo docker-compose up -d`

Os dados persistem graças aos volumes Docker:

* `postgres_data`
* `gitea_data`
* `runner_data`
---

## **Parar Tudo (incluindo a VM)**

`exit`  
`vagrant halt`

Remover a VM (opcional):

`vagrant destroy`

---

## **Funcionalidades Disponíveis**

* ✔ Repositórios Git via HTTP
* ✔ Interface web completa
* ✔ Banco de dados persistente
* ✔ CI/CD com Gitea Actions
* ✔ Execução de pipelines Node.js
* ✔ Ambiente isolado via Vagrant
* ✔ Infra reproduzível

---

## **Observações Importantes**

* O uso de `host.docker.internal` garante comunicação correta entre runner e host
* O ambiente simula uma infraestrutura real de CI/CD
* Ideal para estudos, POCs e portfólio DevOps