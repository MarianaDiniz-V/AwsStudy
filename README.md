###### AWS

# Infraestrutura e Rede

## Load Balancer

É um serviço responsável por manter a estabilidade de um servidor quando o tráfego ou o volume de dados é muito grande. A idéia é distribuir as milhares de solicitações entre os diferentes servidores. 


## Api Gateway

Funciona como uma porta de entrada para a nossa aplicação. Ao receber uma requisição, ela é responsável por acionar o microsserviço correto. Ela pode, inclusive, chamar uma função lambda.

    <img src = "https://www.tibco.com/sites/tibco/files/media_entity/2020-05/api-gateway-diagram.svg">
###### [img: https://www.tibco.com/pt-br/reference-center/what-is-an-api-gateway]


# Dados e Armazenamento:


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
