Se o SSH está ativo em ambos os lados, mas você recebe um *Connection timed out*, significa que algo está impedindo a conexão. Aqui estão algumas verificações e soluções para resolver o problema:

---

### 1️⃣ **Verifique se o SSH está rodando no servidor**
No servidor (192.168.1.100), execute:
```bash
sudo systemctl status ssh
```
Se não estiver rodando, inicie com:
```bash
sudo systemctl start ssh
sudo systemctl enable ssh
```

---

### 2️⃣ **Teste a conectividade entre os dispositivos**
No cliente (seu desktop), tente pingar o servidor:
```bash
ping 192.168.1.100
```
- **Se não houver resposta**, pode ser um problema de rede ou firewall.
- **Se houver resposta**, o SSH pode estar bloqueado.

---

### 3️⃣ **Verifique o firewall (UFW)**
No servidor, execute:
```bash
sudo ufw status
```
Se estiver ativado e bloqueando a porta 22, libere o SSH:
```bash
sudo ufw allow 22
sudo ufw reload
```

---

### 4️⃣ **Verifique se a porta 22 está aberta**
No servidor, execute:
```bash
sudo netstat -tlnp | grep ":22"
```
Se não aparecer nada, tente reinstalar o OpenSSH:
```bash
sudo apt update && sudo apt install --reinstall openssh-server
```

---

### 5️⃣ **Teste a conexão localmente no servidor**
Se o SSH estiver rodando, tente conectar nele mesmo:
```bash
ssh localhost
```
Se não conectar, o problema pode estar na configuração do SSH.

---

### 6️⃣ **Verifique o arquivo de configuração do SSH**
No servidor, abra:
```bash
sudo nano /etc/ssh/sshd_config
```
Confirme que **as seguintes linhas não estão comentadas (`#` na frente)** e têm esses valores:
```
Port 22
PermitRootLogin yes
PasswordAuthentication yes
```
Depois, reinicie o SSH:
```bash
sudo systemctl restart ssh
```

---

### 7️⃣ **Verifique se há algum bloqueio no roteador**
Se o servidor estiver em outra rede ou atrás de um roteador, o tráfego na porta 22 pode estar bloqueado. Tente conectar via outra porta (por exemplo, 2222):
1. No servidor, edite `/etc/ssh/sshd_config` e altere:
   ```
   Port 2222
   ```
2. Reinicie o SSH:
   ```bash
   sudo systemctl restart ssh
   ```
3. No cliente, conecte com:
   ```bash
   ssh -p 2222 shaffer443@192.168.1.100
   ```

---

Se nada funcionar, me avise onde parou e o que tentou! 🚀
