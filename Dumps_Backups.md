Para criar um usuário no MySQL apenas para fazer backups, siga estes passos no terminal:

### 1️⃣ Acesse o MySQL como root:
```sh
mysql -u root -p
```

### 2️⃣ Crie o usuário com acesso de qualquer lugar (`%`) e local (`localhost`):
```sql
CREATE USER 'backup'@'%' IDENTIFIED BY 'senha_segura';
CREATE USER 'backup'@'localhost' IDENTIFIED BY 'senha_segura';
```
⚠️ **Substitua `'senha_segura'` por uma senha forte.**

### 3️⃣ Conceda permissões para backup:
```sql
GRANT SELECT, SHOW VIEW, LOCK TABLES, EVENT, TRIGGER, RELOAD, PROCESS, REPLICATION CLIENT ON *.* TO 'backup'@'%';
GRANT SELECT, SHOW VIEW, LOCK TABLES, EVENT, TRIGGER, RELOAD, PROCESS, REPLICATION CLIENT ON *.* TO 'backup'@'localhost';
```
🔹 **Explicação das permissões:**
- `SELECT, SHOW VIEW` → Para ler os dados e as views.
- `LOCK TABLES` → Necessário para `mysqldump` bloquear tabelas durante o backup.
- `EVENT, TRIGGER` → Para exportar eventos e gatilhos (triggers).
- `RELOAD` → Permite operações como `FLUSH`.
- `PROCESS` → Para ver processos em execução.
- `REPLICATION CLIENT` → Para acessar informações de binlogs (importante para backups incrementais).

### 4️⃣ Aplique as permissões:
```sql
FLUSH PRIVILEGES;
```

### 5️⃣ Teste o login e a permissão:
No terminal, tente conectar com o usuário recém-criado:
```sh
mysql -u backup -p -h <ip_do_servidor>
```
E tente rodar:
```sql
SHOW DATABASES;
```
Se conseguir ver os bancos, o usuário está configurado corretamente.

Agora é só usar `mysqldump` para fazer backups, por exemplo:
```sh
mysqldump -u backup_user -p --all-databases > backup.sql
```

Se precisar de algo mais refinado, me avisa! 🚀🔥
