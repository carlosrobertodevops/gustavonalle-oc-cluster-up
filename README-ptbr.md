# oc-cluster-up


Contêiner do Docker para iniciar um cluster docker-in-docker [OKD](https://okd.io) de vários nós. Para hosts Linux, ele funciona sem virtualização, por isso é extremamente rápido e leve.


:rotating_light::rotating_light: Este repositório usa software alfa e deve ser usado apenas para fins de desenvolvimento e teste. :rotating_light::rotating_light:

O código é retirado do ramo ```master``` de [origin](github.com/openshift/origin), mais o [console](github.com/openshift/console) e o [operatore-lifecycle-manager]( https://github.com/operator-framework/operator-lifecycle-manager).

Para iniciar em primeiro plano no Linux, certifique-se de que o SELinux esteja desabilitado e execute:
```
docker run -it -p 9000:9000 -v /tmp/:/tmp/ -v /etc/docker/certs.d/:/certs/ -v /var/run/docker.sock:/var/run/docker.sock gustavonalle/oc-cluster-up
```

Abra outro terminal e você verá um mestre, um nó e o contêiner principal:

```
$ docker ps
CONTAINER ID        IMAGE                         CREATED             PORTS         
d74781e6a89b        openshift/dind-node           20 minutes ago     openshift-node-2
7bd51a24a055        openshift/dind-node           20 minutes ago     openshift-node-1
8d995fcdbe4f        openshift/dind-master         20 minutes ago     openshift-master
e1c4886f6162        gustavonalle/oc-cluster-up    23 minutes ago     cranky_clarke

```

Alternativamente, digite ```Ctrl+p``` e depois ```Ctrl+q``` para reutilizar o mesmo terminal.

### Demo

![asciiart](https://github.com/carlosrobertodevops/oc-cluster-up/raw/master/demo.gif)


### MacOS

[MacOS hosts](README-macos.md) precisam do docker-machine para funcionar.

### Cluster size

Por padrão, o tamanho do cluster é 2 (mestre + 1 nó), mas o tamanho pode ser alterado com a variável de ambiente NODES.

Por exemplo, para executar um cluster de nó único (somente mestre):

```
docker run -e "NODES=0" ...
```

### Console

O console estará acessível em https://localhost:9090

### Registry

O registro pode ser acessado de fora do OKD no contêiner mestre na URL https://registry.router.MASTER_IP.nip.io.

Exemplo de uso:

```
MASTER=$(oc describe nodes/openshift-master-node  | grep InternalIP | awk '{print $2}')

# Puxe uma imagem pública existente
docker pull jboss/infinispan-server

# Marque-o para usar no namespace 'myproject'
docker tag jboss/infinispan-server
docker tag jboss/infinispan-server registry.router.$MASTER.nip.io/myproject/infinispan

# Entrar como desenvolvedo
oc login -u developer -p developer

# Criar um novo projeto
oc new-project myproject

# Acesse o registro
docker login -u $(oc whoami) -p $(oc whoami -t) https://registry.router.$MASTER.nip.io/

# Empurre a imagem
docker push registry.router.$MASTER.nip.io/myproject/infinispan

# Crie um novo aplicativo usando a imagem interna
oc new-app myproject/infinispan
```

### Pausando e retomando

É possível parar os containers sem destruir, preservando assim todas as imagens e configurações baixadas. Use o comando para parar:

```
docker stop openshift-node-1 openshift-node-2 openshift-master
```

e para começar:
```
docker start openshift-master openshift-node-1 openshift-node-2
```

### Limpar

Para destruir todos os containers e arquivos criados, preservando as imagens, use este script:

```
docker kill $(docker ps -q) && docker system prune -f && sudo rm -Rf /tmp/openshift*
```

### Aplicação de exemplo

Caso o ```oc``` não esteja instalado localmente, é possível reutilizá-lo do mestre:

```
alias okd="docker exec -it openshift-master oc"
```

Os comandos abaixo irão criar um novo aplicativo e expô-lo fora do OKD:

```
okd new-app openshift/hello-openshift --name app
okd expose svc/app
```

Acesse http://app-default.router.172.17.0.3.nip.io/ para receber uma mensagem de boas-vindas do Openshift!

Nota: O master pode não ser 172.17.0.3 em sua instalação, para descobrir, execute ```oc describe nodes/openshift-master-node | grep InternalIP | awk '{print $2}'```


### TODO

* Prometheus
* S2i support


### #oc-cluster-up


Contêiner do Docker para iniciar um cluster docker-in-docker [OKD](https://okd.io) de vários nós. Para hosts Linux, ele funciona sem virtualização, por isso é extremamente rápido e leve.


:rotating_light::rotating_light: Este repositório usa software alfa e deve ser usado apenas para fins de desenvolvimento e teste. :rotating_light::rotating_light:

O código é retirado do ramo ```master``` de [origin](github.com/openshift/origin), mais o [console](github.com/openshift/console) e o [operatore-lifecycle-manager]( https://github.com/operator-framework/operator-lifecycle-manager).

Para iniciar em primeiro plano no Linux, certifique-se de que o SELinux esteja desabilitado e execute:

```
docker run -it -p 9000:9000 -v /tmp/:/tmp/ -v /etc/docker/certs.d/:/certs/ -v /var/run/docker.sock:/var/run/docker .sock gustavonalle/oc-cluster-up
```

Abra outro terminal e você verá um mestre, um nó e o contêiner principal:

```
$ docker ps
PORTAS CRIADAS DE IMAGEM DE ID DE CONTENTOR
d74781e6a89b openshift/dind-node 20 minutos atrás openshift-node-2
7bd51a24a055 openshift/dind-node 20 minutos atrás openshift-node-1
8d995fcdbe4f openshift/dind-master 20 minutos atrás openshift-master
e1c4886f6162 gustavonalle/oc-cluster-up 23 minutos atrás cranky_clarke

```

Alternativamente, digite ```Ctrl+p``` e depois ```Ctrl+q``` para reutilizar o mesmo terminal.

### Demonstração

![asciiart](https://github.com/gustavonalle/oc-cluster-up/raw/master/demo.gif)


### Mac OS

[hosts MacOS](README-macos.md) precisam do docker-machine para funcionar.

### Tamanho do cluster

Por padrão, o tamanho do cluster é 2 (mestre + 1 nó), mas o tamanho pode ser alterado com a variável de ambiente NODES.


Por exemplo, para executar um cluster de nó único (somente mestre):

```
docker run -e "NODES=0" ...
```

### Consola

O console estará acessível em https://localhost:9090

### Registro

O registro pode ser acessado de fora do OKD no contêiner mestre na URL https://registry.router.MASTER_IP.nip.io.

Exemplo de uso:

```
MASTER=$(oc describe nodes/openshift-master-node | grep InternalIP | awk '{print $2}')

# Puxe uma imagem pública existente
docker pull jboss/infinispan-server

# Marque-o para usar no namespace 'myproject'
tag docker jboss/infinispan-server registry.router.$MASTER.nip.io/myproject/infinispan

# Entrar como desenvolvedor
oc login -u desenvolvedor -p desenvolvedor

#Criar projeto
oc novo-projeto meuprojeto

# Acesse o registro
docker login -u $(oc whoami) -p $(oc whoami -t) https://registry.router.$MASTER.nip.io/

# Empurre a imagem
docker push registry.router.$MASTER.nip.io/myproject/infinispan

# Crie um novo aplicativo usando a imagem interna
oc novo aplicativo meuprojeto/infinispan
```

### Pausando e retomando

É possível parar os containers sem destruir, preservando assim todas as imagens e configurações baixadas. Use o comando para parar:

```
docker stop openshift-node-1 openshift-node-2 openshift-master
```

e para começar:
```
docker start openshift-master openshift-node-1 openshift-node-2
```

### Limpar

Para destruir todos os containers e arquivos criados, preservando as imagens, use este script:

```
docker kill $(docker ps -q) && docker system prune -f && sudo rm -Rf /tmp/openshift*
```

### Aplicação de exemplo

Caso o ```oc``` não esteja instalado localmente, é possível reutilizá-lo do mestre:

```
alias okd="docker exec -it openshift-master oc"
```

Os comandos abaixo irão criar um novo aplicativo e expô-lo fora do OKD:

```
okd new-app openshift/hello-openshift --name app
okd expor svc/app
```

Acesse http://app-default.router.172.17.0.3.nip.io/ para receber uma mensagem de boas-vindas do Openshift!

Nota: O master pode não ser 172.17.0.3 em sua instalação, para descobrir, execute ```oc describe nodes/openshift-master-node | grep InternalIP | awk '{print $2}'```


### FAÇAM

* Prometeu
* Suporte S2i


### Erros comuns

#### Permissão negada

No Docker 1.13.1, a imagem do docker pode falhar ao iniciar mostrando erros de permissão como este (via `docker logs ...`):

```bash
go: criando diretório de trabalho: mkdir /tmp/go-build717279851: permissão negada
go: criando diretório de trabalho: mkdir /tmp/go-build462782577: permissão negada
mkdir: não é possível criar o diretório '/tmp/openshift': Permissão negada
```

Você pode contornar o problema adicionando `--privileged` à chamada `docker run`, por exemplo

```bash
docker run --privileged --name oc-cluster-up ...
```

#### Permissão negada

No Docker 1.13.1, a imagem do docker pode falhar ao iniciar mostrando erros de permissão como este (via `docker logs ...`):

```bash
go: creating work dir: mkdir /tmp/go-build717279851: permission denied
go: creating work dir: mkdir /tmp/go-build462782577: permission denied
mkdir: cannot create directory '/tmp/openshift': Permission denied
```

Você pode contornar o problema adicionando `--privileged` à chamada `docker run`, por exemplo

```bash
docker run --privileged --name oc-cluster-up ...
```
