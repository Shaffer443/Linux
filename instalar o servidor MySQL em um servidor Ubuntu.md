Aqui está um guia passo a passo para instalar o servidor MySQL em um servidor Ubuntu 18.04:

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
