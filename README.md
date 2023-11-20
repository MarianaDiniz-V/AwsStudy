###### AWS

# Infraestrutura e Rede

## Load Balancer

É um serviço responsável por manter a estabilidade de um servidor quando o tráfego ou o volume de dados é muito grande. A idéia é distribuir as milhares de solicitações entre os diferentes servidores. 

Aqui consideramos uma instância ec2 rodando um serviço web.

* Duplicamos a instância em questão, alterando apenas nome e zona de disponibilidade.
* Com as instâncias criadas, podemos criar um load balancer, lembrando de se atenar a vpc, caso haja mais de uma, zonas de disponibilidade e subnet. 
* Mesmo com a opção de acesso pelo mundo externo habilitada, devemos atribuir a ele  grupo com essas permissões. Devemos criar também um target group.
* Com o load balancer criados, podemos testar através do nome de DNS.

### Auto Scaling

Com ele podemos provisionar um escalonamento automático de recursos. O primeiro passo é criar um modelo(hardware). Com o modelo criado, criamos um grupo de auto scaling e o selecionamos. Na opçao de adicionar um balaneador de carga, nós, na verdade, selecionamos um target group, que é o grupo de destino do load balancer.


### Ponteiro

Aqui estamos trabalhando com microsserviços, onde cada um aponta para um container. Dentro do load balancer, em listeners, vamos editar as roles:

* Criamos uma nova role com a seguinte condição: IF path-pattern THEN foward. Basicamente, definimos um caminho e o container para o qual ele deve ser redirecionado.

## Api Gateway

Funciona como uma porta de entrada para a nossa aplicação. Ao receber uma requisição, ela é responsável por acionar o microsserviço correto. Ela pode, inclusive, chamar uma função lambda.

<img src = "https://www.tibco.com/sites/tibco/files/media_entity/2020-05/api-gateway-diagram.svg">

###### [https://www.tibco.com/pt-br/reference-center/what-is-an-api-gateway]

* Devemos criar uma policy sobre o serviço de aws que será "amarrado" ao Gateway, como por exemplo o S3. Essa policy descreve todas as ações que poderão ser executadas.
* Criamos também uma role, que linka a essa policy e direciona a um serviço, no nosso caso, ao Api Gateway.


### Importando a API

* Com um Bucket(S3) já criado, amarramos ele ao Gateway configurando o yaml da aplicação.
* Na aws, basta criar uma api e importar o arquivo de configuração.
* Com  api importada, podemos criar novos métodos(verbos). Configuramos a parte de "integration request" e mapeamos os parâmetros. Por exemplo: se o nome do parâmetro é "teste", o mapeamento ficaria: method.request.path.teste.
* Em "integration response", podemos configurar códigos e mensagem para determinados cenários. A configuração da mensagem é feita em: Mapping Templates -> Content-Type


# Computação


## EC2

Ao criar uma nova instância, é recomendado criar e salvar um par de chaves(interna e externa) para que possamos ter acesso ao servidor. Essa chave vem com um número alto de permissões, mas o ideal é que seja permitido apenas a leitura dessa chave. Para isso, rodamos o seguinte comando: **chmod 400 + nome**

Para acessar a instância via ssh, entramos nela e vamos na opção "conectar". Em "client ssh", copiamos o comando e rodamos na mesma pasta onde salvamos a chave.

### Alocação de IP

Toda instância possui um IP, que é alterado toda vez que interrompemos essa instância ou até mesmo em caso de instabilidade. Por isso, o ideal é que aloquemos um ip.

* Em "IP's elásticos" criamos um novo IP.
* Com o IP criado, selecionamos "Alocar endereço IP elástico" e "Associar endereço IP elástico"


## Lambda

É um serviço de computação que permite executar código sem provisionar ou gerenciar servidores. Após criado, o Lambda trás um trigger: o CloudWatch, onde podemos ver os logs da função. Nós podemos criar outros triggers, que são basicamente outros serviços da aws.


# Containers

## Elastic Container Service (ECS)

É um serviço de orquestração de contêineres.


<img src = "https://miro.medium.com/max/720/1*f85DYkcx6eJneaVrKaX8ow.png">

###### [https://medium.com/huia/orquestrando-e-configurando-containers-docker-com-o-ecs-na-aws-2bbd6a6a4781]

* No núcleo temos o container docker. 
* A task definition possui os parêmetros de como a tarefa será executada.
* A service é a responsáel por administrar as tarefas.
* Cluster é basicamente o agrupamento das instâncias.

Na criação do Cluster podemos utilizar dois serviços: o EC2 e o **Fargate**. Diferente do EC2, no Fargate toda a infraestrutura é provisionada e gerenciada pela AWS.

## Elastic Container Registry (ECR)

É um serviço de registro de imagem de contêiner, onde usuários ou instâncias do Amazon EC2 especificados podem acessar seus repositórios e imagens.

* Dentro do serviço de ECS, temos no menu a opção de repositório. Trata-se do ECR.
* Sempre que criamos um repositório, a AWS disponibiliza um passo a passo para o push.

Aqui consideramos que o Cluster já tenha sido criado. Para linkar esse repositório ao ECS, criamos uma nova task definition:

* Ao selecionar a opção de "Add Container", colamos a URI do repository no campo "image".
* Agora podemos criar uma service, dentro do Cluster, e selecionar a task definition criada.
* Nas regras de segurança precisamos permitir o acesso externo ao load balancer e o acesso interno(do load balancer) ao container. Para o acesso interno, permitimos apenas a rede da VPC ou das subnets(mais específico).


# Dados e Armazenamento


## Amazon S3

É um serviço que fornece armazenamento de objetos dentro da aws. O repositório onde esse objeto é armazenado, é chamado de Bucket. 

* Dentro da S3, criamos um bucket, que deve possuir um nome únio dentro da AWS.
* Com o Bucker criado, basta fazer o upload dos arquivos.

Por padrão, um bucket não vem público. Para ter acesso a ele, precisamos criar uma policy e desativar a opção "Block all public access".

* Dentro de permissões, selecionamos a opção "edit bucket policy".
* Digitamos então a policy desejada. Existem alguns exemplos em "policy examples".

### Versionamento do Bucket

A cada alteração feita, a aws na verdade criará um novo objeto, com seu próprio id. O último objeto criado será a versão default. Dessa forma, como não há exclusão de versões, podemos, caso necessário, voltar para a versão X. Para utilizar essa ferramenta, precisamos alterar a permissão de versionamento no S3. 

* Dentro do objeto, ao ativar a opção "show versions", podemos visualizar todas as versões do mesmo.
* Podemos configurar um lyfecicle para transicionar ou excluir as versões de acordo com uma regra.

### Site estático com o S3

Com o S3 podemos também hospedar um site estático, sem preocupações com o servidor. 

* Após a criação do bucker, basta deixá-lo público e ativar a propriedade de "estatic website hosting".
* Nesta página, vamos informar nossa página principal, que é "index.html", na seção "Index document".
* Por fim, adicionamos ao bucket o objeto desejado.

### Cloudfront

É um serviço que acelera a distribuição de conteúdo e arquivos de imagem para os usuários. Quando o usuário faz uma solcitação através desse, a solicitação é roteada para o ponto de presença que fornece a menor latência.

O primeiro passo é criar um bucket e anexar nele os objetos desejados. Esse bucket deve ser privado, pois a nossa intenção é "amarra-lo" ao cloudfront, sendo assim, o único meio de acesso. Após a criação e configuração do bucket, criamos um cloudfront:

* Selecionamos a origem (Bucket) e em "S3 Bucket access", "Yes use OAI". Pois definimos que o bucket seria restrito.
* Podemos deixar que a aws faça o updtade do Bucket Policy 
* Para consultar o cloudfront, passamos juntamente ao endereço o nome do objeto anexado

