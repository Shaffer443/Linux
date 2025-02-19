Para compartilhar uma pasta no Linux e permitir que outros usuários da rede acessem, você pode usar **Samba** (para compartilhamento com máquinas Windows/Linux) ou **NFS** (para compartilhamento entre sistemas Linux). Vou te mostrar como configurar o **Samba**, que é mais comum para redes mistas.

---

## 📌 **Passo a passo para compartilhar uma pasta via Samba**
### 🔹 1. Instalar o Samba (se ainda não estiver instalado)
No terminal, execute:
```bash
sudo apt update
sudo apt install samba -y
```

---

### 🔹 2. Criar a pasta a ser compartilhada
Escolha ou crie a pasta que será compartilhada. Exemplo:
```bash
sudo mkdir -p /srv/compartilhado
```
Dê as permissões necessárias para que todos os usuários tenham acesso:
```bash
sudo chmod 777 /srv/compartilhado
```
Se quiser que apenas um grupo específico tenha acesso, crie um grupo e altere as permissões:
```bash
sudo groupadd compartilhamento
sudo chown -R :compartilhamento /srv/compartilhado
sudo chmod -R 770 /srv/compartilhado
```
Adicione os usuários que devem ter acesso ao grupo:
```bash
sudo usermod -aG compartilhamento usuario1
sudo usermod -aG compartilhamento usuario2
```

---

### 🔹 3. Configurar o Samba
Edite o arquivo de configuração do Samba:
```bash
sudo nano /etc/samba/smb.conf
```
Adicione no final do arquivo:
```
[Compartilhado]
   path = /srv/compartilhado
   browseable = yes
   writable = yes
   guest ok = yes
   create mask = 0777
   directory mask = 0777
   force user = nobody
```
Se quiser restringir o acesso a usuários específicos, remova `guest ok = yes` e adicione:
```
   valid users = usuario1 usuario2
```

---

### 🔹 4. Criar um usuário Samba
Se estiver usando **usuários específicos** no Samba:
```bash
sudo smbpasswd -a usuario1
```
Ele pedirá uma senha para esse usuário.

---

### 🔹 5. Reiniciar o Samba
Depois de configurar, reinicie o serviço para aplicar as mudanças:
```bash
sudo systemctl restart smbd
sudo systemctl enable smbd
```

---

### 🔹 6. Acessar a pasta compartilhada na rede
Agora, de outro computador na rede:

- **Windows**: Abra o Explorador de Arquivos e digite na barra de endereços:
  ```
  \\IP_DO_SERVIDOR\Compartilhado
  ```
  Exemplo: `\\192.168.1.100\Compartilhado`

- **Linux**: No gerenciador de arquivos, vá para `Conectar a servidor` e digite:
  ```
  smb://IP_DO_SERVIDOR/Compartilhado
  ```

Se estiver acessando por terminal, use:
```bash
smbclient //IP_DO_SERVIDOR/Compartilhado -U usuario1
```

---

## 📌 **Outras opções**
Se todos os computadores forem **Linux**, você pode usar **NFS** para compartilhamento mais eficiente. Quer que eu te mostre como configurar isso também? 😊
