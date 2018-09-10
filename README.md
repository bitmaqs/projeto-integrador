Projeto integrador - Documentação 

1. Objetivo
 
O projeto tem como objetivo principal prover uma página wordpress utilizando containers. O cenário inicial consiste em 1 container para o banco de dados, 1 para o nginx e 3 para o wordpress. 

2. Cenário Inicial 

A primeira versão deste ambiente funciona localmente. O nginx além de servidor web, age como um load balancer simples. Existem três containers para o wordpress que compartilham o mesmo diretório do host. As configurações do banco de dados são feitas diretamente pelo docker compose. 

2.1. Subindo o ambiente
- Arquivos necessários: Dockerfile-nginx, docker-compose.yaml, nginx.conf.
- Os arquivos devem estar no mesmo diretório.
- Utilizar o “docker-compose up” para subir o ambiente
- No browser, acessar localhost:80
- A página de instalação do wordpress deverá aparecer. 

3. Cenário com Swarm na AWS

A ideia original continua a mesma, porém os testes foram realizados diretamente na nuvem (amazon) e utilizando clusters com o swarm-mode. O container de banco de dados roda no exclusivamente no manager, o nginx em modo global (um em cada instância). O wordpress não é mais configurado em três containers individuais, mas utiliza a função de replicar o container. O docker-compose foi configurado para criar 3 réplicas, mas este numero pode ser facilmente alterado conforme necessário.

3.1. Subindo o ambiente
- Arquivos: docker-compose-swarm.yaml, aws-swarm-boot.sh
- Criar instâncias na amazon (um manager e alguns workers)
- O script aws-swarm-boot.sh pode ser utilizado durante a criação das instâncias, permitindo que as máquinas já iniciem com o docker instalado e até mesmo com os workers incluídos no cluster. 
- Caso os diretórios “/var/www/html” e “/var/lib/mysql” não existam no host, os mesmos devem ser criados (pode ser feito manualmente ou incluir no script) 
- No manager, utilizar o “stack deploy” com o arquivo do compose para subir o ambiente.
- No browser, entrar com o endereço provido pelo dns da amazon. Tanto o endereço do manager quanto dos workers devem iniciar o wordpress. 
Obs: É necessário abrir algumas portas na política de segurança, como a porta para http e portas do swarm.

Próximas etapas do projeto: 
- Subir o ambiente com kubernetes (em andamento) 
- Adicionar containers de monitoramento.

