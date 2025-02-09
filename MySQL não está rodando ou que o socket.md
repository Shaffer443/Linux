Esse erro significa que o MySQL não está rodando ou que o socket `/tmp/mysql.sock` não está sendo criado corretamente. Vamos resolver isso passo a passo.

---

### 🔎 **Possíveis Causas e Soluções**

#### 1️⃣ **Verifique se o MySQL está rodando**
Primeiro, cheque se o MySQL está ativo:
```bash
sudo systemctl status mysql
```
Se ele não estiver rodando, inicie-o com:
```bash
sudo systemctl start mysql
```
Se quiser que o MySQL sempre inicie junto com o sistema:
```bash
sudo systemctl enable mysql
```

---

#### 2️⃣ **Tente conectar via TCP/IP**
Se o socket não existir, tente conectar via TCP/IP em vez do socket local:
```bash
mysql -u root -p --protocol=tcp
```

---

#### 3️⃣ **Reinicie o serviço do MySQL**
Se o MySQL estiver travado, reinicie:
```bash
sudo systemctl restart mysql
```
Ou, dependendo da sua distro:
```bash
sudo service mysql restart
```

---

#### 4️⃣ **Cheque se o socket do MySQL está no local correto**
O erro menciona que está tentando `/tmp/mysql.sock`, mas às vezes o MySQL usa outro caminho.

Veja onde está o arquivo do socket:
```bash
sudo find / -type s -name "mysql.sock" 2>/dev/null
```
Se encontrar em outro local, edite o arquivo de configuração do MySQL:

```bash
sudo nano /etc/mysql/mysql.conf.d/mysqld.cnf
```
E adicione/edite a linha:
```ini
socket=/caminho/correto/mysql.sock
```
Depois, reinicie o MySQL.

---

#### 5️⃣ **Tente reinstalar o MySQL**
Se nada funcionar, talvez seja um problema de instalação corrompida. Reinstale o MySQL:

```bash
sudo apt-get remove --purge mysql-server mysql-client mysql-common
sudo apt-get autoremove
sudo apt-get autoclean
sudo apt-get install mysql-server
```

Após reinstalar, tente iniciar novamente:
```bash
sudo systemctl start mysql
```

---
# Resolvido assim 09/02/2025

🔎 3️⃣ Verifique se o arquivo de socket existe

Se o erro persistir, pode ser que o arquivo de socket /tmp/mysql.sock não tenha sido criado. Para verificar, rode:
```bash
ls -l /tmp/mysql.sock
```
Se o arquivo não existir, tente recriá-lo reiniciando o MySQL:
```bash
sudo systemctl restart mysql
```
Se ainda não funcionar, tente criar um link simbólico para o socket:
```bash
sudo ln -s /var/run/mysqld/mysqld.sock /tmp/mysql.sock
```
Depois, tente se conectar novamente.

