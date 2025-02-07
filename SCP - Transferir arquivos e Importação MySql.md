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
