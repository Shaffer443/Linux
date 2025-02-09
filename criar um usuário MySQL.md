Para criar um usuário MySQL que **só possa executar SELECT** no banco de dados `entradas` (para ser usado na web), siga estes passos:  

---

### **1️⃣ Acessar o MySQL**  
Entre no MySQL com um usuário que tenha privilégios de administrador, como `root` ou seu usuário principal:  

```sh
mysql -u root -p
```

Digite a senha quando solicitado.

---

### **2️⃣ Criar o Usuário**  
Crie um usuário chamado `webuser` (ou outro nome que preferir) e defina uma senha segura:  

```sql
CREATE USER 'webuser'@'%' IDENTIFIED BY 'SenhaSegura123';
```
> **Troque `'SenhaSegura123'` por uma senha forte.**

O **`'%'`** permite conexões de qualquer IP. Se quiser restringir para que só funcione no servidor web (por exemplo, no próprio Ubuntu `192.168.1.100`), use:  
```sql
CREATE USER 'webuser'@'192.168.1.100' IDENTIFIED BY 'SenhaSegura123';
```

---

### **3️⃣ Conceder Apenas Permissão de SELECT**  
Agora, conceda permissão **somente para SELECT** no banco `entradas`:

```sql
GRANT SELECT ON entradas.* TO 'webuser'@'%';
```
Ou, se restringiu o acesso ao IP do servidor:  
```sql
GRANT SELECT ON entradas.* TO 'webuser'@'192.168.1.100';
```

---

### **4️⃣ Aplicar as Permissões**  
Execute:

```sql
FLUSH PRIVILEGES;
```

Isso garante que as permissões sejam aplicadas imediatamente.

---

### **5️⃣ Testar o Novo Usuário**  
Saia do MySQL:  

```sql
EXIT;
```

Agora, tente conectar com o novo usuário e verificar se ele só consegue fazer SELECT:

```sh
mysql -u webuser -p -h 192.168.1.100 entradas
```

Dentro do MySQL, tente rodar um SELECT:

```sql
SHOW TABLES;
```

E um comando que **não deveria funcionar**, como `DELETE`:

```sql
DELETE FROM alguma_tabela WHERE id=1;
```

Se tudo estiver certo, o `DELETE` deve ser bloqueado.

---

### **🛠 Dica Extra: Melhor Segurança**  
- **Use um firewall (UFW ou IPTables)** para impedir acessos externos desnecessários.  
- **Se o MySQL estiver rodando apenas localmente**, edite o arquivo de configuração (`/etc/mysql/mysql.conf.d/mysqld.cnf`) e garanta que a linha `bind-address` esteja assim para aceitar apenas conexões locais:  
  ```ini
  bind-address = 127.0.0.1
  ```

Agora, seu usuário para acesso web está pronto e seguro! 🚀

---

### ** Se precisar ver as permissões de todos os usuários, pode rodar: **
```sql
SELECT user, host FROM mysql.user;
```
E depois verificar cada um com SHOW GRANTS.
