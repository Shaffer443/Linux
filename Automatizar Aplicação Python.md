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

-----    

Aqui estão algumas verificações e etapas que você pode seguir para corrigir isso:
1. Recarregar o systemd

Após editar o arquivo de serviço, é essencial recarregar o systemd para que ele reconheça as mudanças. Execute os seguintes comandos:
 ```bash
sudo systemctl daemon-reload
```
Isso recarrega todas as unidades de serviço configuradas, garantindo que qualquer modificação seja aplicada.
2. Verificar se o arquivo foi editado corretamente

Certifique-se de que o caminho correto foi usado no arquivo de serviço:
 ```bash
ExecStart=/usr/bin/python3.6 /home/gti/AvisoRede/main.py
```
Se o arquivo de serviço não foi salvo corretamente, pode ser que ele ainda esteja utilizando o caminho errado. Reabra o arquivo e confirme:
 ```bash
sudo nano /etc/systemd/system/avisorede.service
```
3. Verifique o caminho do arquivo diretamente no sistema de arquivos

Abra o terminal e certifique-se de que o arquivo main.py realmente existe no diretório /home/gti/AvisoRede/:
 ```bash
ls /home/gti/AvisoRede/main.py
```
Se o arquivo não existir ou estiver com um nome diferente (por exemplo, AvisoREde com uma capitalização diferente), isso pode explicar a discrepância nos logs.
4. Reiniciar o serviço

Depois de garantir que o caminho está correto, reinicie o serviço:
 ```bash
sudo systemctl restart avisorede.service
```
5. Verificar os logs novamente

Agora, verifique os logs para ver se a mudança foi aplicada corretamente:
 ```bash
journalctl -u avisorede.service -f
```
6. Limpar possíveis caches de serviços antigos

Se o problema persistir, é possível que o systemd esteja tentando iniciar um serviço antigo, ou que tenha algum cache de configuração.

Para garantir que o systemd está rodando a versão mais recente do serviço, você pode parar o serviço, limpar qualquer cache de status e reiniciar:
 ```bash
sudo systemctl stop avisorede.service
sudo systemctl reset-failed avisorede.service
sudo systemctl start avisorede.service
```
Isso garantirá que o systemd não tente reiniciar o serviço em um estado incorreto.

---

Sim, isso pode ser um problema dependendo de como os `print()` estão sendo gerados e onde o processo está sendo executado. O `systemd` não tem uma interface de terminal, então se o `print()` está tentando imprimir no terminal ou console e o serviço não tem acesso a um terminal interativo, isso pode gerar erros ou falhas.

Uma forma de resolver isso seria direcionar a saída do seu script para um arquivo de log ou para o `journal` do `systemd` para garantir que você consiga acompanhar as saídas sem depender do terminal. Para isso, você pode ajustar a configuração do seu `service` e configurar o `stdout` e o `stderr` para registrar os logs em arquivos ou no `journal`:

### 1. Alterar a configuração do serviço para capturar a saída:

Você pode adicionar as seguintes linhas ao seu arquivo de unidade (`avisorede.service`) para redirecionar a saída para arquivos de log:

```ini
[Service]
User=gti
WorkingDirectory=/home/gti/AvisoRede
ExecStart=/usr/bin/python3.6 /home/gti/AvisoRede/main.py
Restart=always
StandardOutput=append:/var/log/avisorede.log
StandardError=append:/var/log/avisorede.log
```

Isso irá gravar as saídas do `print()` e qualquer erro no arquivo `/var/log/avisorede.log`. Você pode visualizar esse arquivo com o comando:

```bash
tail -f /var/log/avisorede.log
```

### 2. Usar `systemd` journal para capturar a saída:

Se você preferir não usar arquivos de log, pode configurar para que as saídas sejam capturadas pelo `journal` do `systemd` e você pode acessá-las diretamente com `journalctl`.

```ini
[Service]
User=gti
WorkingDirectory=/home/gti/AvisoRede
ExecStart=/usr/bin/python3.6 /home/gti/AvisoRede/main.py
Restart=always
StandardOutput=journal
StandardError=journal
```

Dessa forma, as saídas do seu script aparecerão diretamente no `journalctl` e você pode monitorá-las com:

```bash
journalctl -u avisorede.service -f
```

### 3. Garantir que o script esteja funcionando corretamente em segundo plano:

Verifique também se o seu script está funcionando corretamente sem interatividade, como seria no modo de serviço. Se ele depende de algum tipo de entrada do terminal ou interatividade, você pode ter problemas ao executá-lo como um serviço.

---
O status `code=exited, status=2/INVALIDARGUMENT` indica que o processo principal do serviço (`ExecStart`) terminou com um código de saída 2 e que o `systemd` interpretou esse código como um erro devido a um argumento inválido.

No contexto do seu caso, isso geralmente ocorre por um dos seguintes motivos:

1. **Caminho ou Arquivo Incorreto**:
   O Python não conseguiu localizar o arquivo especificado no comando `ExecStart`. Isso pode ser um erro de digitação no caminho ou no nome do arquivo (como `/home/gti/AvisoREde/main.py` em vez de `/home/gti/AvisoRede/main.py`).

2. **Erro no Script Python**:
   Se o script Python é executado, mas contém erros na lógica ou parâmetros fornecidos ao interpretador, ele pode sair com código de erro 2.

3. **Argumentos Incorretos no ExecStart**:
   Caso `ExecStart` contenha opções ou argumentos não reconhecidos pelo interpretador Python, ele também pode gerar esse erro.

4. **Permissões Insuficientes**:
   O usuário especificado (`User=gti`) pode não ter permissões para acessar o arquivo ou diretório.

---

### Soluções:
- **Verificar o Caminho**: Certifique-se de que o caminho em `ExecStart` está correto e o arquivo existe:
  ```bash
  ls -l /home/gti/AvisoRede/main.py
  ```

- **Testar o Script Manualmente**:
  Execute o comando manualmente como o mesmo usuário configurado no serviço para identificar erros:
  ```bash
  sudo -u gti /usr/bin/python3.6 /home/gti/AvisoRede/main.py
  ```

- **Verificar o Script Python**:
  Inspecione o script para erros de lógica ou problemas nos `print()` ou configurações iniciais.

- **Permissões**:
  Verifique se o usuário `gti` tem permissões no arquivo e diretórios:
  ```bash
  chmod -R 755 /home/gti/AvisoRede
  chown -R gti:gti /home/gti/AvisoRede
  ```

- **Logs para Depuração**:
  Habilite o redirecionamento de `StandardOutput` e `StandardError` no arquivo de configuração do serviço para capturar mensagens de erro.

Se o erro persistir após essas verificações, compartilhe os logs para análise mais detalhada.


