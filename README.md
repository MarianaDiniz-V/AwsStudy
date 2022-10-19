###### AWS

# Infraestrutura e Rede

## Load Balancer

É um serviço responsável por manter a estabilidade de um servidor quando o tráfego ou o volume de dados é muito grande. A idéia é distribuir as milhares de solicitações entre os diferentes servidores. 


## Api Gateway

Funciona como uma porta de entrada para a nossa aplicação. Ao receber uma requisição, ela é responsável por acionar o microsserviço correto. Ela pode, inclusive, chamar uma função lambda.

<img src = "https://www.tibco.com/sites/tibco/files/media_entity/2020-05/api-gateway-diagram.svg">

###### [img: https://www.tibco.com/pt-br/reference-center/what-is-an-api-gateway]

* Devemos criar uma policy sobre o serviço de aws que será "amarrado" ao Gateway, como por exemplo o S3. Essa policy descreve todas as ações que poderão ser executadas.
* Criamos também uma role, que linka a essa policy e direciona a um serviço, no nosso caso, ao Api Gateway.

### Importando a API

###### ** Aqui consideramos que um Bucket já tenha sido criado e que o yml da aplicação esteja configurado. **





# Computação


## Lambda

É um serviço de computação que permite executar código sem provisionar ou gerenciar servidores. Após criado, o Lambda trás um trigger: o CloudWatch, onde podemos ver os logs da função. Nós podemos criar outros triggers, que são basicamente outros serviços da aws.


# Dados e Armazenamento


## Amazon S3

É um serviço que fornece armazenamento de objetos dentro da aws. O repositório onde esse objeto é armazenado, é chamado de Bucket. 

* Dentro da S3, criamos um bucket, que deve possuir um nome únio dentro da AWS.
* Com o Bucker criado, basta fazer o upload dos arquivos.

Por padrão, um bucket vem público. Porém, para ter acesso a ele, precisamos criar uma policy e desativar a opção "Block all public access".

* Dentro de permissões, selecionamos a opção "edit bucket policy".
* Digitamos então a policy desejada. Existem alguns exemplos em "policy examples".

### Versionamento do Bucket

A cada alteração feita, a aws na verdade criará um novo objeto, com seu próprio id. O último objeto criado será a versão default. Dessa forma, como não há exclusão de versões, podemos, caso necessário, voltar para a versão X. Para utilizar essa ferramenta, precisamos alterar a permissão de versionamento no S3. 

* Dentro do objeto, ao ativar a opção "show versions", podemos visualizar todas as versões do mesmo.
* Podemos configurar um lyfecicle para transicionar ou excluir as versões de acordo com uma regra.

### Site estático com o S3

Com o S3 podemos também hospedar um site estático, sem preocupações com o servidor. 

* Após a criação do bucker, basta deixá-lo público e ativar a propriedade de "tatic website hosting".
* Nesta página, vamos informar nossa página principal, que é "index.html", na seção "Index document".
* Por mim, adicionamos ao bucker o objeto desejado.

### Cloudfront

É um serviço que acelera a distribuição de conteúdo e arquivos de imagem para os usuários. Quando o usuário faz uma solcitação através desse, a solicitação é roteada para o ponto de presença que fornece a menor latência.

O primeiro passo é criar um bucket e anexar nele os objetos desejados. Esse bucker deve ser privado, pois a nossa intenção é "amarra-lo" ao cloudfront, sendo assim, o único meio de acesso. Após a criação e configuração do bucket, criamos um cloudfront:

* Selecionamos a origem (Bucket) e em "S3 Bucket access", "Yes use OAI". Pois definimos que o bucket seria restrito.
* Podemos deixar que a aws faça o updtade do Bucket Policy 
* Para consultar o cloudfront, passamos juntamente ao endereço o nome do objeto anexado
