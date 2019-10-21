# Plataforma-Installer
instalador da plataforma

## Pré-Requisitos
- Windows 10 64-bit v1903 ou superior
- Hyper-V habilitado com Recursos de Containers
- WSL (Windows Subsystem for Linux) v1.0 ou superior habilitado
- Docker Desktop on Windows v2.1 ou superior instalado
  - usar imagens Linux

## Obter e Executar o Plataforma-Installer

1. Clonar instalador da plataforma da branch new-domain:
```
git clone --branch new_domain https://github.com/ONSBR/Plataforma-Installer.git
```

2. Criar a rede local para os containers docker:
```
docker network create plataforma_network
```

3. Acessar o repositório clonado no passo 1 e executar o comando para inicilizar o docker-compose:
```
docker-compose up -d
```
