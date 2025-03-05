# DESAFIO DA VEXPENSES
## Sobre
Repositório contendo a solução para o desafio de estágio em DevOps da VExpenses. O objetivo é demonstrar habilidades em IaC (Infraestrutura como Código) com Terraform, segurança e automação de configurações na AWS.
## **Descrição Técnica**
![image](https://github.com/user-attachments/assets/e4411a6a-bb7b-4e84-b74a-233eba782ff5)

Nessa primeira parte do código é inicializado um ambiente Terraform que defina a AWS como provedor de nuvem e especifica a região na qual esses recursos serão criados, além disso ocorre as definições de variáveis que armazenam respectivamente os valores de projeto e candidato, caso não sejam preenchidos os valores padrão são "VExpenses" e "SeuNome".

![image](https://github.com/user-attachments/assets/a0aa04fd-5d61-4a32-8c45-bf7bcf7ab80b)

Nessa segunda parte o código gera uma chave privada RSA de 2048 bits localmente, além disso é importada a chave pública para a AWS, a qual permite o acesso SSH à instância EC2.

![image](https://github.com/user-attachments/assets/70be59cd-3023-4b20-83dc-cf8ec4d9d73e)

Nessa parte o código cria uma VPC com CIDR 10.0.0.0/16 e habilita suporte a DNS para resolução interna de nomes.

![image](https://github.com/user-attachments/assets/8fb70a5f-009f-443e-aad1-c435d5113d0a)

Nessa parte é criada uma subnet pública (10.0.1.0/24) na zona de disponibilidade us-east-1a.

![image](https://github.com/user-attachments/assets/d9e20ec1-307c-4be3-84b7-92e7d4862bc2)

Referente a essa parte do código o primeiro recurso criado (aws_internet_gateway) permite comunicação entre a VPC e a internet. Já o segundo recuros (aws_route_table) define uma rota padrão (0.0.0.0/0) direcionando tráfego para o gateway. Por fim o terceiro recurso (aws_route_table_association) associa a subnet à tabela de rotas.

![image](https://github.com/user-attachments/assets/3cdfe114-776a-4b8e-88a7-8b51c8b2760a)

Essa parte do código é referente ao Grupo de segurança nela é realizada a liberação de entrada peela porta 22 (SSH) de qualquer IP (0.0.0.0/0 e IPv6 ::/0) e também permite todo tráfego de saída.

![image](https://github.com/user-attachments/assets/abefc1ca-7779-4f46-a292-e72da6057577)

Nessa parte do código é responsável por provisionar uma instância EC2 na AWS com Debian 12, associando-a a uma subnet, security group e chave SSH. A instância é t2.micro, tem 20 GB de armazenamento e recebe um IP público. Durante a inicialização, executa um script de atualização do sistema. Por fim, a instância é nomeada dinamicamente com base nas variáveis projeto e candidato.

![image](https://github.com/user-attachments/assets/5f76dba9-cb62-4ca6-945e-9f98df6d107c)

Por fim essa parde do código define outputs para exibir informações importantes após a criação da instância EC2. O primeiro output, private_key, armazena a chave privada usada para acessar a instância via SSH, sendo marcada como sensível para maior segurança. O segundo output, ec2_public_ip, exibe o endereço IP público da instância, facilitando o acesso remoto.

## **Implementação de Melhorias**

![image](https://github.com/user-attachments/assets/275328f8-2056-4334-9aa2-a4de7d60aab3)

A primeira melhoria é em relação a região que agora pode ser definida dinamicamente.

![image](https://github.com/user-attachments/assets/a88d930b-7bca-45d8-8d16-71672df6bf9f)

Essa variável allowed_ssh_cidr permite restringir o acesso SSH para um intervalo específico de IPs.

![image](https://github.com/user-attachments/assets/d54d9a80-98b9-4c69-9efa-94725544ecc4)

Com o map_public_ip_on_launch = true, é garantindo que as instâncias tenham IPs públicos automaticamente.

![image](https://github.com/user-attachments/assets/d4fa7ae6-c7cc-4058-bf4d-71ee78a01eed)

O recurso aws_security_group agora inclui uma regra para permitir tráfego HTTP na porta 80, possibilitando o acesso ao servidor web, além disso passou a referenciar var.allowed_ssh_cidr para maior flexibilidade no controle de acesso.

![image](https://github.com/user-attachments/assets/0ec1e741-7215-457b-a2ef-b966def4eb9f)

Nessa parte o script de inicialização (user_data) agora instala e inicia o Nginx automaticamente, tornando a instância um servidor web funcional logo após a criação.

![image](https://github.com/user-attachments/assets/cc7ab4a3-9f17-46a3-b1d0-198b185c2e2a)

Nessa parte o codigo através do instance_ip mantém o retorno do IP público da instância, já o ssh_private_key mantém a chave privada, garantindo que ela seja marcada como sensitive.

# **Instruções de Uso**

### 1. Clone o Repositório
```bash
git clone https://github.com/marlonmelo12/TesteDevOps.git
cd [DIRETÓRIO_DO_PROJETO]
```
### 2. Inicialize o Terraform
```bash
terraform init
```
### 3. Revise o Plano de Infraestrutura
```bash
terraform plan
```
### 4. Aplique a Infraestrutura
```bash
terraform apply
```
## **Acesse a Instância**
### 1. Obtenha o IP Público
```bash
terraform output instance_ip
```
### 2. Conecte-se via SSH
```bash
ssh -i chave_privada.pem admin@[IP_PUBLICO]
```
Importante: A chave privada é gerada automaticamente e exibida no output ssh_private_key. Salve-a em um arquivo (ex: chave_privada.pem) com permissão 400:

```bash
terraform output -raw ssh_private_key > chave_privada.pem
chmod 400 chave_privada.pem
```
### 3. Acesse o Nginx
Abra no navegador: http://[IP_PUBLICO]

# **Personalização**
Edite as váriaveis no main.tf.

# Destruir Infraestrutura
Para remover todos os recursos e evitar custos.
```bash
terraform destroy
```
