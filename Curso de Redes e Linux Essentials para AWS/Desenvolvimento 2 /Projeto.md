# Projeto EC2 - Configuração e Gerenciamento de Instância

Este projeto demonstra a configuração e o gerenciamento de uma instância EC2 na AWS, incluindo a criação de um volume EBS adicional.

## Preparação

#### Configuração da instância EC2

1. Acesse o Console da AWS (https://console.aws.amazon.com/).
2. No menu de serviços, procure e clique em "EC2".
3. Na página do EC2, clique no botão "Launch Instance".
4. Escolha "Amazon Linux 2 AMI" como a imagem do sistema operacional.
5. Selecione o tipo de instância adequado às suas necessidades (por exemplo, t2.micro para testes).
6. Configure os detalhes da instância (mantenha as configurações padrão para começar).
7. Adicione armazenamento conforme necessário (o padrão é geralmente suficiente para começar).
8. Adicione tags se desejar (opcional).
9. Configure o grupo de segurança:
   - Crie um novo grupo de segurança.
   - Adicione uma regra para SSH (porta 22) com acesso apenas do seu IP.
10. Revise e lance a instância.
11. Crie um novo par de chaves ou use um existente. Se criar um novo, faça o download e guarde em local seguro.
12. Lance a instância.

### Encontrando o DNS público correto para sua instância:

Faça login no Console AWS
Vá para o serviço EC2
No painel de navegação à esquerda, clique em "Instâncias"
Selecione a instância que você criou
No painel de detalhes abaixo, procure por "DNS público IPv4"
Copie esse valor

O endereço DNS público geralmente terá um formato semelhante a este:
ec2-XX-XX-XX-XX.compute-1.amazonaws.com
Onde XX-XX-XX-XX é substituído por números que correspondem ao endereço IP público da sua instância.
Então, o comando SSH completo ficaria assim:

    ssh -i "caminho/para/sua-chave.pem" ec2-user@ec2-XX-XX-XX-XX.compute-1.amazonaws.com

## Passos Realizados

### 1. Conexão SSH à Instância EC2

Conexão via SSH

Para se conectar via SSH:

Abra um terminal no seu computador.
Navegue até o diretório onde você salvou a chave privada.
Execute o seguinte comando (substitua os valores conforme necessário):

```bash
chmod 400 teste.pem 
ssh -i "teste.pem" ec2-user@ec2-54-187-200-73.us-west-2.compute.amazonaws.com
```


### 2. Verificação do Armazenamento Disponível

Gerenciando o armazenamento
No Console da AWS, vá para EC2 > Volumes.
Clique em "Create Volume".
Escolha o tipo de volume (gp2 é uma boa opção para uso geral).
Defina o tamanho do volume (por exemplo, 10 GB).
Selecione a zona de disponibilidade da sua instância EC2.
Clique em "Create Volume".
Após a criação, selecione o volume e clique em "Actions" > "Attach Volume".
Escolha sua instância EC2 e defina o dispositivo (por exemplo, /dev/sdf).

Formatando e montando o volume

Após anexar o volume, conecte-se à sua instância EC2 via SSH e execute os seguintes comandos:

#### Verifique se o volume está disponível

```bash
lsblk
```

Resultado:
```
NAME    MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
xvda    202:0    0   8G  0 disk 
└─xvda1 202:1    0   8G  0 part /
xvdf    202:80   0  10G  0 disk 
```

### 3. Formatação do Novo Volume

```bash
sudo mkfs -t ext4 /dev/xvdf
```

### 4. Montagem do Novo Volume

```bash
sudo mkdir /mnt/meu-volume
sudo mount /dev/xvdf /mnt/meu-volume
```

### 5. Criação de Arquivo de Teste

```bash
echo "teste" | sudo tee /mnt/meu-volume/teste.txt
```

### 6. Verificação do Novo Volume

```bash
ls -l /mnt/meu-volume/
```

Resultado:
```
total 20
drwx------ 2 root root 16384 out  2 05:25 lost+found
-rw-r--r-- 1 root root     6 out  2 05:28 teste.txt
```

### 7. Verificação do Espaço em Disco

```bash
df -h
```

Resultado relevante:
```
/dev/xvdf       9,7G   28K  9,2G   1% /mnt/meu-volume
```

### 8. Verificação dos Pontos de Montagem

```bash
mount
```

Resultado relevante:
```
/dev/xvdf on /mnt/meu-volume type ext4 (rw,relatime)
```

### 9. Verificação do Conteúdo do Arquivo de Teste

```bash
cat /mnt/meu-volume/teste.txt 
```

Resultado:
```
teste
```

## Observações

- A instância EC2 está executando Amazon Linux 2.
- Um volume EBS adicional de 10GB foi anexado e montado com sucesso.
- O novo volume foi formatado com o sistema de arquivos ext4.
- Um arquivo de teste foi criado no novo volume para verificar a funcionalidade.

## Próximos Passos

1. Configurar a montagem automática do novo volume editando o arquivo `/etc/fstab`.
2. Implementar backups regulares dos dados no volume EBS.
3. Considerar a criptografia do volume para maior segurança.
4. Monitorar o uso do espaço em disco e configurar alertas se necessário.

Lembre-se de encerrar a instância EC2 e excluir os volumes EBS não utilizados para evitar custos desnecessários.
