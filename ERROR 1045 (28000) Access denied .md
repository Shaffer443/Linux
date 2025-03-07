Esse erro **"ERROR 1045 (28000): Access denied for user 'web_info'@'localhost'"** pode ter várias causas. Vamos resolver isso passo a passo.

---

### **1. Verifique se o usuário realmente existe**
Logue no MySQL como root:

```bash
sudo mysql -u root -p
```
Depois, rode:

```sql
SELECT user, host FROM mysql.user;
```
Se `web_info@localhost` **não aparecer**, significa que o usuário ainda não foi criado corretamente.

Se precisar criar, faça:

```sql
CREATE USER 'web_info'@'localhost' IDENTIFIED BY 'sua_senha';
GRANT ALL PRIVILEGES ON *.* TO 'web_info'@'localhost' WITH GRANT OPTION;
FLUSH PRIVILEGES;
```

---

### **2. Tente acessar sem especificar `localhost`**
O comando que você usou:

```bash
mysql -u web_info -p localhost
```
está tentando conectar via **socket local**. Se o MySQL estiver configurado para aceitar apenas conexões TCP/IP, tente:

```bash
mysql -u web_info -p -h 127.0.0.1
```

---

### **3. Verifique a senha do usuário**
Pode ser que a senha salva não esteja correta. Para redefinir:

1. Logue no MySQL como root:

   ```bash
   sudo mysql -u root -p
   ```

2. Atualize a senha do usuário:

   ```sql
   ALTER USER 'web_info'@'localhost' IDENTIFIED BY 'nova_senha_segura';
   FLUSH PRIVILEGES;
   ```

Tente logar de novo:

```bash
mysql -u web_info -p
```

---

### **4. Verifique o método de autenticação**
No MySQL 8+, o método de autenticação padrão pode ser `caching_sha2_password`, enquanto algumas aplicações só aceitam `mysql_native_password`. Para verificar:

```sql
SELECT user, host, plugin FROM mysql.user WHERE user = 'web_info';
```

Se o método for `caching_sha2_password`, altere para `mysql_native_password`:

```sql
ALTER USER 'web_info'@'localhost' IDENTIFIED WITH mysql_native_password BY 'sua_senha';
FLUSH PRIVILEGES;
```

---

### **5. Veja se o MySQL está rodando corretamente**
Verifique se o serviço do MySQL está ativo:

```bash
sudo systemctl status mysql
```
Se estiver parado, inicie:

```bash
sudo systemctl start mysql
```

---

Se nada disso resolver, me conta quais mensagens aparecem em cada etapa, que a gente acha o problema! 🚀
