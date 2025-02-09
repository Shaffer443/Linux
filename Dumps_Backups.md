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

---

### ** Script BASH **

```sh
#!/bin/bash

# Configurações
USER="backup"
PASSWORD="SUA_SENHA_AQUI"  # Substitua pela senha correta
BACKUP_DIR="/home/shaffer443/dumps"
DATE=$(date +"%Y-%m-%d")
FILENAME="backup_${DATE}.sql"

# Garantir que o diretório de backup existe
mkdir -p "$BACKUP_DIR"

# Criar o dump do MySQL
mysqldump -u "$USER" -p"$PASSWORD" --all-databases > "$BACKUP_DIR/$FILENAME"

# Verificar se o dump foi criado com sucesso
if [ $? -eq 0 ]; then
    echo "Backup concluído com sucesso: $BACKUP_DIR/$FILENAME"
else
    echo "Erro ao criar backup!"
    exit 1
fi
```

Aqui está um script shell para criar um dump do MySQL e salvar na pasta especificada com a data de hoje no nome do arquivo.

Salve esse script como `backup_mysql.sh`, dê permissão de execução com:

```bash
chmod +x backup_mysql.sh
```

E execute com:

```bash
./backup_mysql.sh
```

Se quiser agendar isso para rodar automaticamente, pode usar o cron:

```bash
crontab -e
```

E adicionar uma linha como esta para rodar todos os dias às 3h da manhã:

```bash
0 3 * * * /home/shaffer443/backup_mysql.sh
```

Isso garantirá que seu backup seja feito regularmente! 🚀
