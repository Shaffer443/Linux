Sim, é possível configurar sua aplicação para iniciar automaticamente ao ligar o servidor Ubuntu e rodar sem precisar do comando `sudo python3`. Aqui estão os passos para configurar isso:

---

### **1. Configurar o Sistema para Não Precisar do `sudo`**
Se você está usando a porta 5000, que é maior que 1024, não é necessário usar `sudo` para iniciar o aplicativo. Certifique-se de que o usuário que executará a aplicação tem permissão para acessar os arquivos e diretórios da aplicação. Se necessário, altere as permissões:

```bash
sudo chown -R $(whoami):$(whoami) /caminho/para/Avisorede
```

---

### **2. Criar um Serviço no Systemd**
Crie um arquivo de serviço para sua aplicação no `systemd`. 

1. Crie o arquivo do serviço:
   ```bash
   sudo nano /etc/systemd/system/avisorede.service
   ```

2. Adicione o seguinte conteúdo ao arquivo, ajustando o caminho e o nome do usuário:
   ```ini
   [Unit]
   Description=Avisorede Application
   After=network.target

   [Service]
   User=seu_usuario
   WorkingDirectory=/caminho/para/Avisorede
   ExecStart=/usr/bin/python3 main.py
   Restart=always

   [Install]
   WantedBy=multi-user.target
   ```

   - **`User`**: Substitua `seu_usuario` pelo nome do usuário que executará o aplicativo.
   - **`WorkingDirectory`**: Substitua pelo caminho completo até a pasta da aplicação.
   - **`ExecStart`**: Confirme o caminho correto para o Python 3 (use `which python3` para encontrar o caminho exato).

3. Salve e saia do arquivo (Ctrl+O, Enter, Ctrl+X no Nano).

---

### **3. Ativar e Iniciar o Serviço**
Ative o serviço para que ele seja iniciado automaticamente com o servidor:

```bash
sudo systemctl daemon-reload
sudo systemctl enable avisorede.service
sudo systemctl start avisorede.service
```

Verifique se o serviço está rodando corretamente:

```bash
sudo systemctl status avisorede.service
```

---

### **4. Verificar Logs em Caso de Problemas**
Se houver algum problema, você pode verificar os logs do serviço:

```bash
journalctl -u avisorede.service -f
```

---
O erro **`PermissionError: [Errno 13] Permission denied`** indica que o usuário configurado no serviço não tem permissão para executar algo necessário, como acessar a porta ou arquivos. Aqui está como corrigir isso:

---

### **1. Verificar Porta Reservada**
Se sua aplicação está tentando usar uma porta abaixo de 1024 (como 80 ou 443), o sistema exige permissões elevadas (`sudo`). Caso esteja utilizando uma porta acima de 1024 (como 5000), não deveria ser necessário. 

#### Confirme a porta que está sendo usada no seu script:
Abra o `main.py` e veja a linha onde o servidor Flask (ou similar) é iniciado, algo como:
```python
app.run(host='0.0.0.0', port=5000)
```
Se a porta for menor que 1024, altere para uma acima, como 5000.

---

### **2. Permissões de Arquivos e Pastas**
Garanta que o usuário configurado no arquivo de serviço (`User=seu_usuario`) tenha acesso à pasta e arquivos da aplicação.

Execute:
```bash
sudo chown -R seu_usuario:seu_usuario /caminho/para/Avisorede
```

---

### **3. Testar o Script Manualmente**
Para confirmar que o script funciona com o mesmo usuário configurado no `avisorede.service`, execute o comando abaixo sem `sudo`:

```bash
python3 /caminho/para/Avisorede/main.py
```

- Se funcionar, o problema está na configuração do serviço.
- Se der o mesmo erro, ajuste as permissões ou debug o código para identificar o que está causando o problema.

---

### **4. Atualizar o Serviço Systemd**
Verifique novamente o arquivo `/etc/systemd/system/avisorede.service` e ajuste se necessário:

- Confirme que o usuário configurado tem permissão para rodar a aplicação.
- Altere a linha `ExecStart` para capturar erros:
  ```ini
  ExecStart=/usr/bin/python3 /caminho/para/Avisorede/main.py
  ```
- Adicione a diretiva `StandardOutput` e `StandardError` para salvar os logs:
  ```ini
  [Service]
  User=seu_usuario
  WorkingDirectory=/caminho/para/Avisorede
  ExecStart=/usr/bin/python3 main.py
  Restart=always
  StandardOutput=journal
  StandardError=journal
  ```

---

### **5. Aplicar Alterações**
Depois de corrigir as configurações, recarregue o `systemd` e reinicie o serviço:

```bash
sudo systemctl daemon-reload
sudo systemctl restart avisorede.service
sudo systemctl status avisorede.service
```

---

### **6. Verificar Logs**
Se o problema persistir, colete os logs completos com:
```bash
journalctl -u avisorede.service -e
```
O erro **`PermissionError: [Errno 1] Operation not permitted`** ocorre porque a criação de sockets ICMP (usados para enviar pacotes de ping) requer permissões elevadas no sistema. No Linux, isso é uma restrição de segurança.

---

### **Opções para Resolver o Problema**

#### **1. Executar o Script com Capacidades Elevadas**
Ao invés de usar `sudo`, você pode permitir que o script use o socket ICMP sem precisar rodar como superusuário. Use o comando abaixo para conceder a permissão necessária ao interpretador Python:

```bash
sudo setcap cap_net_raw+ep $(which python3)
```

Isso permite que o Python crie sockets ICMP sem precisar de privilégios de administrador. Depois, teste novamente:

```bash
python3 /home/gti/AvisoRede/main.py
```

Se funcionar, o problema estará resolvido.

---

#### **2. Usar Sockets UDP como Alternativa**
Algumas bibliotecas de ping, como o `ping3`, tentam automaticamente usar sockets UDP quando os sockets ICMP falham. No entanto, isso pode não funcionar para todos os casos ou sistemas. Confirme se o `ping3` está configurado corretamente para fallback ao UDP.

---

#### **3. Substituir o `ping3` por Comandos Shell**
Se você não conseguir usar sockets ICMP diretamente, pode substituir o uso de `ping3` por uma chamada de sistema ao comando `ping`. Por exemplo:

```python
import subprocess

def ping_via_shell(ip):
    try:
        subprocess.run(["ping", "-c", "1", ip], stdout=subprocess.PIPE, stderr=subprocess.PIPE, check=True)
        print(f"{ip} está acessível.")
    except subprocess.CalledProcessError:
        print(f"{ip} não está acessível.")
```

---

#### **4. Ajustar o Serviço Systemd para Rodar com Privilégios**
Se nenhuma das opções acima for viável, você pode configurar o arquivo de serviço do Systemd para rodar com permissões elevadas. Edite o arquivo `/etc/systemd/system/avisorede.service` e adicione:

```ini
[Service]
User=root
```

Embora isso funcione, não é recomendado rodar serviços como `root`, devido a riscos de segurança.

---

### **Próximos Passos**
- **Recomenda-se fortemente usar a solução do `setcap`**, pois ela limita as permissões apenas ao necessário, sem expor todo o serviço.
- Teste novamente após aplicar a solução e, caso tenha problemas, compartilhe os novos erros ou dúvidas!

---

Esse erro ocorre porque `/usr/bin/python3` provavelmente é um **symlink** (atalho) para uma versão específica do Python, como `/usr/bin/python3.6`. O comando `setcap` precisa ser aplicado no arquivo real, não no symlink. 

### **Solução**

#### 1. Verifique o Alvo do Symlink
Para descobrir para onde o symlink `/usr/bin/python3` aponta, execute:

```bash
ls -l /usr/bin/python3
```

Isso retornará algo como:

```plaintext
lrwxrwxrwx 1 root root 9 Jan 16 10:00 /usr/bin/python3 -> python3.6
```

Aqui, podemos ver que `/usr/bin/python3` aponta para `/usr/bin/python3.6`.

#### 2. Aplicar `setcap` no Alvo Real
Agora aplique o comando `setcap` diretamente no arquivo real (por exemplo, `/usr/bin/python3.6`):

```bash
sudo setcap cap_net_raw+ep /usr/bin/python3.6
```

#### 3. Testar o Script
Execute seu script novamente para verificar se o problema foi resolvido:

```bash
python3 /home/gti/AvisoRede/main.py
```

---

### **Nota**
Caso seu sistema use múltiplas versões do Python, e você altere o Python padrão frequentemente, será necessário repetir esse comando para o binário correto. 
---
Ótimo! O status **`active (running)`** indica que sua aplicação foi iniciada corretamente como um serviço do `systemd` e está em execução.

Agora, sempre que o servidor Ubuntu for iniciado, sua aplicação será carregada automaticamente na porta configurada. Você pode verificar a funcionalidade da aplicação acessando-a pela rede ou usando a porta configurada (por exemplo, `http://<ip_do_servidor>:5000`).

### **Próximos Passos (Opcional)**
1. **Verificar Logs**  
   Monitore os logs para garantir que a aplicação continua rodando sem erros:
   ```bash
   journalctl -u avisorede.service -f
   ```

2. **Testar Reinício Automático**  
   Reinicie o servidor para garantir que o serviço inicie corretamente:
   ```bash
   sudo reboot
   ```

3. **Configurar Firewall (se necessário)**  
   Se a aplicação precisar ser acessada externamente, certifique-se de que a porta está aberta:
   ```bash
   sudo ufw allow 5000
   ```

4. **Manutenção do Serviço**
   - Para reiniciar a aplicação manualmente:
     ```bash
     sudo systemctl restart avisorede.service
     ```
   - Para desativar o início automático:
     ```bash
     sudo systemctl disable avisorede.service
     ```


