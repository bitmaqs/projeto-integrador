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


4. Cenário em Kubernetes

Assim como no swarm-mode, o ambiente está em um cluster na amazon web services. O diferencial é que desta vez foi configurado com kubernetes, deixando-o mais robusto, complexo e autônomo.
O cenário consiste em um servidor master e dois nodes. Cada um possui alguns PODs com o container do Wordpress e um POD com o container mysql. O nginx não é mais utilizado pois foi configurado um serviço de load balancing do próprio kubernetes. Todos os arquivos são salvos em volumes persistentes, assim evitando que o conteúdo seja perdido com a exclusão de algum POD.


Um ponto interessante, é que uma vez configurado utilizando o kops (Kubernetes Operations, serve para gerenciar o cluster) e possuindo as permissões do AWS CLI, todo o ambiente fica automatizado. As especificações ficam salvas em um bucket S3, é capaz de criar e apagar as instâncias EC2, são automaticamente configurados os serviços de load balancing, grupos de auto-scaling, políticas de segurança, etc. Em outras palavras, se um servidor for desativado ou se um POD for deletado, os mesmos serão repostos automaticamente. 

4.1 Subindo o Ambiente 

- Configurar o AWS CLI com as devidas credenciais;
- Configurar o Kops ( 1 master, 2 nodes, modo gossip) ;
- Aplicar os PVCs (mysql-volumeclaim.yaml e wordpress-volumeclaim.yaml);
- Criar o Deployment do banco de dados (mysql-deploy.yaml);
- Criar o Serviço do banco de dados (mysql-service.yaml) ;
- Criar o Deployment do wordpress (wordpress-deploy.yaml);
- Criar o Serviço do wordpress (wordpress-service.yaml).


