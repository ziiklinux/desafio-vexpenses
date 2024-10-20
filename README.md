# Desafio Vexpenses
Demonstrar conhecimentos em Infraestrutura como Código (IaC) utilizando Terraform, bem como habilidades em segurança e automação de configuração de servidores.

# Tarefa 1

O arquivo main.tf cria uma infraestrutura na AWS, constituida de: VPC, subnet, Internet Gateway, tabela de rotas, grupo de segurança e uma instância EC2 rodando o Debian 12. 

A instância EC2 é configurada para receber um IP público e pode ser acessada via SSH usando a chave privada gerada.

O arquio main.tf segue a seguinte ordem para criar os recursos:

1.Define o provedor AWS e especifica a região onde os recursos serão criados.

2.Define duas variáveis que serão usadas para nomear os recursos. 

3.Gera chave privada e cria um par de chaves AWS.

4.Cria uma VPC com o bloco CIDR 10.0.0.0/16. Habilita suporte a DNS e nomes de host DNS.

5.Cria uma subnet dentro da VPC criada anteriormente, com o bloco CIDR 10.0.1.0/24.

6.Cria um Internet Gateway e o associa à VPC criada anteriormente.
 
7.Cria uma tabela de rotas associada à VPC. A rota padrão (0.0.0.0/0) direciona todo o tráfego para o Internet Gateway criado anteriormente.

8.Associa a tabela de rotas criada anteriormente à subnet criada anteriormente.

9.Cria um grupo de segurança associado à VPC.

-Regras de Entrada: Permite conexões SSH (porta 22) de qualquer lugar.

-Regras de Saída: Permite o tráfego de saída para qualquer destino.

10.Busca a AMI mais recente do Debian 12 para uso em instâncias EC2.

11.Cria uma instância EC2 usando a AMI do Debian 12 encontrada anteriormente:

-A instância é do tipo t2.micro.

-A instância é associada à subnet criada anteriormente.

-O par de chaves criado anteriormente é usado para acessar a instância.

-O grupo de segurança criado anteriormente é aplicado à instância.

-A instância recebe um IP público.

-O volume raiz da instância tem 20 GB e é do tipo gp2. O volume é excluído quando a instância é terminada.

-O user_data é um script que é executado quando a instância é inicializada pela primeira vez. Esse script pode ser usado para configurar a instância, instalar pacotes, baixar arquivos, configurar serviços, entre outras tarefas.

12.Exibe duas saídas:

-private_key: Exibe a chave privada gerada para acessar a instância EC2. O valor é marcado como sensível para evitar que seja exibido em logs.

-ec2_public_ip: Exibe o endereço IP público da instância EC2.

# Tarefa 2

Somente a porta 22 estava aberta para entrada, e como foi pedido o uso do nginx tive que abrir a porta 80 e 443 tanto pra saída quanto para entrada.

Limitei o acesso SSH a IPs especificos, pois é um risco bindar a porta SSH com toda a Internet.

Todas as portas estavam abertas para saída. A fim de corrigir esta vunerabilidade eu apenas permiti saída nas portas 443,80,22 e 53.

Adicionei a variavél "ips_ssh" a fim de limitar o acesso SSH a IPs especificos.

Adicionei um ingress com range de IPs de acesso SSH definido na variável "ips_ssh".

Adicionei um ingress para permitir acesso HTTPS # Presumi que o nginx será usado como um web server e não como load balancer.

Adicionei um ingress para permitir acesso HTTP para o nginx.

Adicionei um ingress para permitir acesso DNS, pois na VPC está setado o uso de DNS.

Adicionei um egress com range de IPs de acesso SSH definido na variável "ips_ssh".

Adicionei um egress para permitir acesso HTTPS.

Adicionei um egress para permitir acesso HTTP para o nginx.

Adicionei um egress para permitir acesso DNS, pois na VPC está setado o uso de DNS. 

Utilizei a funcionalidade dynamic do Terraform para criar rotas IPV6 dinamicamente. 

Adicionei um script que configura uma instância EC2 para servir um site estático usando Nginx, com suporte a HTTP e HTTPS. Ele gera um certificado SSL autoassinado para permitir conexões seguras via HTTPS e configura o Nginx para servir conteúdo a partir do diretório /var/www/html.

# Como executar o arquivo main.tf

Instale o Terraform na sua máquina, você pode baixá-lo por aqui https://www.terraform.io/downloads.html

Na sua máquina linux, crie um diretório para o projeto, para isto utilize o comando mkdir nome_do_projeto no bash.

Entre no diretório utilizando o comando cd nome_do_projeto.

Baixe o arquivo main.tf do repositório.

Crie um arquivo chamado main.tf utilizando o comando sudo nano main.tf.

Isso abrirá uma caixa de texto, copiei o texto do arquivo main.tf baixado do repositório e cole na caixa de texto que abriu.

Salve o arquivo com o comando crtl + o e depois crtl + x para sair.

Execute o comando para inicializar o Terraform:

terraform init

Execute o comando parar validar a infraestrutura:

terraform validate

Execute o comando para planejar a infraestrutura:

terraform plan

Execute o comando para aplicar a infraestrutura:

terraform apply

Após a aplicação da infraestrutura verifique a chave privada com o comando:

terraform output private_key 

Certifique-se de salvá-la em um local seguro!

Após verifique o ip com o comando:

terraform output ec2_public_ip

Aṕos acesse a instância EC2 via SSH com o comando:

ssh -i path/to/private_key.pem aws_user@<ec2_public_ip>

Verifique o nginx acessando a seguinte url:

http://<ec2_public_ip>

















 
 









