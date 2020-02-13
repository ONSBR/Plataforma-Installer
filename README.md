# Plataforma-Installer
Módulo de instalação da nova versão de domínio da plataforma ("new_domain). O ambiente foi testado nas versões citadas abaixo no momento da escrita desse documento. Versões mais atualizadas não devem afetar a instalação.

## Pré-Requisitos
- Windows 10 64-bit v1903 ou superior
- Hyper-V habilitado com Recursos de Containers
- WSL (Windows Subsystem for Linux) v1.0 ou superior habilitado
- Docker Desktop on Windows v2.1 ou superior instalado
  - usar imagens Linux

## :ship: Obter e Executar o Plataforma-Installer

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
Na qual:
* **docker-compose** irá buscar um arquivo *docker-compose.yaml* na pasta atual.
* **up** inicia os containers descritos do arquivo de composição.
* **-d**  *Detached mode*: inicia os containers em segundo plano e imprime os nomes. Incompatível com *--abort-on-container-exit*.

A saída esperada é uma lista com os nomes dos containers:
* Creating proxy          ... done
* Creating logspout       ... done
* ...
* Creating maestro        ... done

Referência: https://docs.docker.com/compose/reference/up/

### :exclamation: Configurar para AWS DocumentDB (Amazon)

O Plataforma-Installer está configurado para acessar por padrão repositórios locais, e portanto, a variável de ambiente MONGO_OPTIONS precisa estar presente para que o DocumentDB seja usado.

1. Inserir a variável de ambiente MONGO_OPTIONS antes da sua inicialização, no arquivo docker_compose.yaml:
```
  process_memory:
    build:
      context: ./Dockerfiles
      dockerfile: DProcessMemory
    container_name: "process_memory"
    ports:
      - 9091:9091
    environment:
      - MONGO_HOST=mongo
      - MONGO_OPTIONS=/?ssl=true&ssl_ca_certs=rds-combined-ca-bundle.pem&replicaSet=rs0
```
2. Caso o container já esteja em execução, use o comando abaixo:
```
docker exec -e "MYVAR=MONGO_OPTIONS=/?ssl=true&ssl_ca_certs=rds-combined-ca-bundle.pem&replicaSet=rs0" process_memory env
```

### (Opcional) Instalar o Portainer - Ambiente de Desenvolvimento
Para facilitar a gestão e visualização de containers no ambiente local, pode-se instalar o Portainer:
```
docker run -d -p 9097:9000 -v /var/run/docker.sock:/var/run/docker.sock portainer/portainer --no-auth
```
Acessar http://localhost:9097 e selecionar conjunto de containers em localhost. 

## :warning: Issues Conhecidas

### Erro no Docker Compose
Algumas vezes, pode ocorrer com algum repositório, um erro parecido como o abaixo:
```
Cloning into 'Some-Repository'...
fatal: unable to access 'https://github.com/some_name/Some-Repository.git/': Failed to connect to github.com port 443: Connection refused
```
Para contorná-lo, execute novamente o comando *docker compose up -d* como descrito no passo 3 acima.

### Erro de Login no Portainer
Pode ocorrer um erro ao tentar acessar o container do Portainer. Caso isso ocorra, acesse o mesmo endereço em uma janela anônima.

### Erro no Container Postgres
Caso ocorra um erro na qual o Postgres não inicie, verifique os logs.
```
The data directory was initialized by PostgreSQL version xx, which is not compatible with this version xx.x (Debian xx.x-x.etc)
```
Se os logs possuírem um erro como o acima, apague os volumes de dados da base de dados. Eles provavelmente estão numa versão diferente da base de dados do container.
