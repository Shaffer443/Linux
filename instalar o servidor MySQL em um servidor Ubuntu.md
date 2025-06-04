Aqui está um guia passo a passo para instalar o servidor MySQL em um servidor Ubuntu 18.04:
Para verificar se o MySQL está instalado no seu servidor Ubuntu, você pode usar os seguintes comandos:

### 1. Verificar se o MySQL está instalado
```bash
mysql --version
```
ou
```bash
mysqld --version
```

Se o MySQL estiver instalado, esses comandos retornarão a versão instalada. Se não estiver instalado, você verá uma mensagem como "comando não encontrado".

### 2. Verificar o status do serviço MySQL (se instalado)
```bash
sudo systemctl status mysql
```

Se o MySQL estiver instalado e em execução, você verá informações sobre o serviço com status "active (running)".

### 3. Verificar pacotes instalados relacionados ao MySQL
```bash
dpkg -l | grep mysql
```
ou
```bash
apt list --installed | grep mysql
```

Estes comandos listarão todos os pacotes relacionados ao MySQL que estão instalados no sistema.

### 4. Verificar se o servidor MySQL está ouvindo conexões
```bash
sudo netstat -tulnp | grep mysql
```

Se o MySQL estiver em execução, você verá uma linha mostrando que o serviço está ouvindo em uma porta (normalmente 3306).

Se nenhum desses comandos mostrar resultados, provavelmente o MySQL não está instalado no seu servidor Ubuntu. Nesse caso, você pode instalá-lo com:
```bash
sudo apt update
sudo apt install mysql-server
```

---

### **1. Atualize os pacotes do sistema**
Antes de começar, atualize o índice de pacotes do sistema para garantir que você tenha as versões mais recentes disponíveis:

```bash
sudo apt update
sudo apt upgrade -y
```

---

### **2. Instale o servidor MySQL**
Instale o pacote do servidor MySQL com o comando:

```bash
sudo apt install mysql-server -y
```

Durante a instalação, o MySQL será instalado sem pedir configurações adicionais.

---

### **3. Configure a segurança do MySQL**
Após a instalação, é recomendável executar o script de segurança do MySQL para configurar senhas e remover acessos padrão:

```bash
sudo mysql_secure_installation
```

Durante o processo:
- Será perguntado se deseja configurar o **VALIDATE PASSWORD PLUGIN**. Escolha o nível de validação de senha desejado (0 = Baixo, 1 = Médio, 2 = Alto).
- Escolha uma senha forte para o usuário root.
- Responda "Y" para as outras perguntas para remover usuários anônimos, desabilitar o login remoto do root e limpar bancos de dados de teste.

---

### **4. Verifique o status do serviço MySQL**
Certifique-se de que o MySQL está em execução:

```bash
sudo systemctl status mysql
```

Se não estiver ativo, inicie o serviço com:

```bash
sudo systemctl start mysql
```

E habilite-o para iniciar automaticamente com o sistema:

```bash
sudo systemctl enable mysql
```

---

### **5. (Opcional) Acesse o MySQL**
Para acessar o MySQL como administrador:

```bash
sudo mysql
```

Se preferir autenticação com senha (em vez de autenticação por socket), altere o método de autenticação do usuário root:

1. Acesse o MySQL como root:
   ```bash
   sudo mysql
   ```
2. Execute os seguintes comandos para mudar o método de autenticação:
   ```sql
   ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'SuaSenhaForte';
   FLUSH PRIVILEGES;
   EXIT;
   ```
Agora você pode acessar com:
```bash
mysql -u root -p
```
---
Para alterar a senha do usuário root no MySQL, siga estes passos:  

### 1️⃣ Acesse o MySQL como root  
Se você ainda tem acesso ao MySQL com a senha antiga, entre no terminal e execute:  

```sh
mysql -u root -p
```
Digite sua senha atual quando solicitado.  

### 2️⃣ Altere a senha  
Depois de acessar o MySQL, rode o seguinte comando:  

```sql
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'NovaSenhaForte';
FLUSH PRIVILEGES;
```
Substitua `'NovaSenhaForte'` pela nova senha desejada.  

### 3️⃣ Saia do MySQL  
```sql
EXIT;
```

Agora, ao acessar o MySQL novamente, use a nova senha. 🚀  

Se esqueceu a senha e não consegue logar? Me avise, que te ajudo a resetar! 🔥
---

### **6. (Opcional) Permitir conexões remotas**
Se você precisar acessar o MySQL remotamente, edite o arquivo de configuração:

```bash
sudo nano /etc/mysql/mysql.conf.d/mysqld.cnf
```

Encontre a linha:
```text
bind-address = 127.0.0.1
```

Substitua por:
```text
bind-address = 0.0.0.0
```

Reinicie o MySQL para aplicar as alterações:

```bash
sudo systemctl restart mysql
```

Além disso, crie um usuário com permissão para acessar remotamente:

```sql
CREATE USER 'usuario'@'%' IDENTIFIED BY 'senha';
GRANT ALL PRIVILEGES ON *.* TO 'usuario'@'%' WITH GRANT OPTION;
FLUSH PRIVILEGES;
```

---

### **7. Teste a instalação**
Use o comando abaixo para verificar a versão instalada do MySQL:

```bash
mysql --version
```

---

Com isso, o servidor MySQL estará instalado e configurado no seu Ubuntu 18.04! 🚀

---

Para importar um banco de dados MySQL no seu servidor **Ubuntu (192.168.1.100)**, siga estes passos:

---

## **1️⃣ Copiar o Arquivo de Backup para o Servidor**
Se o arquivo `.sql` está na sua máquina local, você pode copiá-lo para o servidor via **SCP**:

```sh
scp /caminho/do/seu/backup.sql usuario@192.168.1.100:/home/usuario/
```
> **Substitua** `/caminho/do/seu/backup.sql` pelo caminho do arquivo na sua máquina e `usuario` pelo usuário do servidor.

---

## **2️⃣ Acessar o Servidor**
Entre no servidor via SSH:

```sh
ssh usuario@192.168.1.100
```

Se estiver rodando o comando diretamente no servidor, pule essa etapa.

---

## **3️⃣ Acessar o MySQL**
Entre no MySQL como **root** ou outro usuário com permissões adequadas:

```sh
mysql -u root -p
```

Digite a senha quando solicitado.

---

## **4️⃣ Criar o Banco de Dados (Se Necessário)**
Se o banco de dados ainda não existe, crie-o:

```sql
CREATE DATABASE nome_do_banco;
```
> Substitua `nome_do_banco` pelo nome real do banco.

Saia do MySQL:

```sql
EXIT;
```

---

## **5️⃣ Importar o Banco de Dados**
Agora, rode o comando para importar o arquivo `.sql` dentro do MySQL:

```sh
mysql -u root -p nome_do_banco < /home/usuario/backup.sql
```
> **Substitua** `nome_do_banco` pelo nome do seu banco e `backup.sql` pelo nome do arquivo.

---

## **6️⃣ Confirmar a Importação**
Entre novamente no MySQL e confira se as tabelas e dados foram importados corretamente:

```sh
mysql -u root -p
```
Depois, selecione o banco e veja as tabelas:

```sql
USE nome_do_banco;
SHOW TABLES;
```
Se precisar verificar os dados de uma tabela específica:

```sql
SELECT * FROM nome_da_tabela LIMIT 10;
```

---

## **E se der erro?**
Caso tenha problemas como **"Unknown Database"**, verifique se o `.sql` contém a linha `CREATE DATABASE` no início.  
Se já existir, use `DROP DATABASE nome_do_banco;` antes de criar novamente.

Se o erro for **"Access Denied"**, pode ser que o usuário do MySQL não tenha permissões. Você pode conceder permissões assim:

```sql
GRANT ALL PRIVILEGES ON nome_do_banco.* TO 'root'@'localhost';
FLUSH PRIVILEGES;
```

---

Agora, seu banco está importado e pronto para uso! 🚀
