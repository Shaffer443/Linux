Se você deseja iniciar um servidor PHP automaticamente no Ubuntu Server, você precisa criar um serviço `systemd` para gerenciar o processo do servidor. Aqui está um guia passo a passo para configurar isso:

### Passo 1: Crie um Arquivo de Unidade `systemd`

1. **Abra o editor de texto para criar um novo arquivo de unidade:**

   ```
   sudo nano /etc/systemd/system/php-server.service
   ```

2. **Adicione a configuração do serviço ao arquivo.** Supondo que você esteja usando o PHP embutido (por exemplo, `php -S`), o arquivo pode parecer assim:

   ```
   [Unit]
   Description=Servidor PHP
   After=network.target

   [Service]
   ExecStart=/usr/bin/php -S 0.0.0.0:9999 -t /caminho/para/seu/site
   WorkingDirectory=/caminho/para/seu/site
   Restart=always
   User=www-data
   Group=www-data

   [Install]
   WantedBy=multi-user.target
   ```

   - **`ExecStart`**: O comando para iniciar o servidor PHP. Aqui usamos `php -S 0.0.0.0:9999 -t /caminho/para/seu/site` para iniciar o servidor PHP na porta 9999 e servir arquivos do diretório especificado.
   - **`WorkingDirectory`**: O diretório onde o servidor PHP será executado. É recomendável que seja o diretório onde seus arquivos PHP estão localizados.
   - **`User` e `Group`**: Define o usuário e grupo sob o qual o serviço será executado. O usuário `www-data` é geralmente utilizado para serviços web, mas você pode ajustar conforme necessário.

### Passo 2: Recarregue o `systemd`

Depois de criar o arquivo, você precisa recarregar o `systemd` para reconhecer o novo serviço.

```
sudo systemctl daemon-reload
```

### Passo 3: Habilite o Serviço para Iniciar Automaticamente

Ative o serviço para garantir que ele inicie automaticamente na inicialização do sistema.

```
sudo systemctl enable php-server.service
```

### Passo 4: Inicie o Serviço Imediatamente

Se você deseja iniciar o servidor PHP imediatamente sem reiniciar o sistema, use:

```
sudo systemctl start php-server.service
```

### Passo 5: Verifique o Status do Serviço

Para verificar se o serviço está funcionando corretamente, use o comando `status`:

```
sudo systemctl status php-server.service
```

Isso mostrará se o serviço está ativo e se houve algum problema ao iniciar.

### Considerações Adicionais

- **Dependências**: Certifique-se de que o PHP esteja instalado no sistema e que o comando `php` esteja disponível no caminho especificado em `ExecStart`.
- **Firewall**: Verifique se o firewall permite conexões na porta 9999. Você pode ajustar as regras do firewall usando o `ufw` ou outro gerenciador de firewall que você estiver utilizando.
- **Segurança**: Para ambientes de produção, considere usar um servidor web mais robusto como Nginx ou Apache, e configure o PHP-FPM para melhor desempenho e segurança.

Seguindo esses passos, você deve ser capaz de configurar seu servidor PHP para iniciar automaticamente com o Ubuntu Server.

# Falhas Possiveis

O erro que você está encontrando indica um problema com o diretório de trabalho ou com o caminho especificado no comando `ExecStart`. O status `200/CHDIR` sugere que há um problema ao tentar mudar para o diretório de trabalho especificado. Vamos resolver isso passo a passo:

### Verificar e Corrigir o Caminho e Diretório

1. **Verifique o Caminho do Diretório**

   Certifique-se de que o diretório especificado em `WorkingDirectory` realmente exista e que o caminho esteja correto. No seu caso, parece que o diretório `/gti/contatostelefonicos/` deve ser verificado.

   ```
   ls -ld /gti/contatostelefonicos/
   ```

   Se o diretório não existir ou se você tiver digitado o caminho incorretamente, corrija isso no arquivo de unidade.

2. **Corrija o Arquivo de Unidade**

   Abra o arquivo de unidade e ajuste o caminho:

   ```
   sudo nano /etc/systemd/system/contatoseap.service
   ```

   Certifique-se de que o caminho no `ExecStart` e o `WorkingDirectory` estão corretos. Por exemplo:

   ```
   [Unit]
   Description=ContatoSeap
   After=network.target

   [Service]
   ExecStart=/usr/bin/php -S 10.4.0.11:9999 -t /gti/contatostelefonicos/
   WorkingDirectory=/gti/contatostelefonicos/
   Restart=always
   User=www-data
   Group=www-data

   [Install]
   WantedBy=multi-user.target
   ```

   No `ExecStart`, `-t /gti/contatostelefonicos/` deve apontar para o diretório onde seus arquivos PHP estão localizados.

3. **Recarregue e Reinicie o Serviço**

   Após corrigir o arquivo de unidade, recarregue o `systemd` e reinicie o serviço:

   ```
   sudo systemctl daemon-reload
   sudo systemctl restart contatoseap.service
   ```

4. **Verifique o Status do Serviço**

   Verifique novamente o status do serviço para garantir que ele está funcionando:

   ```
   sudo systemctl status contatoseap.service
   ```

### Verifique Permissões

Certifique-se de que o usuário e grupo especificados em `User` e `Group` têm permissões adequadas para acessar o diretório e os arquivos. Se necessário, ajuste as permissões:

```
sudo chown -R www-data:www-data /gti/contatostelefonicos/
```

### Verifique o Comando `ExecStart`

Confirme que o comando `php` está correto e disponível em `/usr/bin/php`. Você pode verificar isso executando o comando diretamente na linha de comando:

```
/usr/bin/php -S 10.4.0.11:9999 -t /gti/contatostelefonicos/
```

Se o comando falhar ou não funcionar como esperado, o problema pode estar no comando em si ou na configuração do PHP.

### Logs Detalhados

Para obter mais detalhes sobre o erro, você pode verificar os logs do `systemd` com:

```
journalctl -u contatoseap.service
```

Isso pode fornecer informações adicionais sobre o que está causando a falha no serviço.


---

Agora o serviço está tentando iniciar, mas ainda está falhando com um **status=200/CHDIR**, o que indica que há um problema ao acessar o diretório especificado (`WorkingDirectory`). Vamos resolver isso:

---

### **Problema Identificado**:
O erro **`status=200/CHDIR`** significa que:
1. O systemd não consegue acessar o diretório (`WorkingDirectory`), ou
2. O usuário `www-data` não tem permissão para ler/executar o PHP nesse diretório.

---

### **Solução Passo a Passo**:

#### 1. **Verifique se o diretório existe e tem permissões corretas**:
```bash
# Verifique se o diretório existe
ls -la /home/shaffer443/MeusProjetos/Financeiro/webfinancasPessoais/

# Dê permissão ao www-data (usuário do serviço)
sudo chown -R www-data:www-data /home/shaffer443/MeusProjetos/Financeiro/webfinancasPessoais/
sudo chmod -R 755 /home/shaffer443/MeusProjetos/Financeiro/webfinancasPessoais/
```

#### 2. **Teste manualmente (como www-data)**:
```bash
sudo -u www-data /usr/bin/php -S 0.0.0.0:9999 -t /home/shaffer443/MeusProjetos/Financeiro/webfinancasPessoais/
```
- Se funcionar, pressione **Ctrl+C** e reinicie o serviço.
- Se **não funcionar**, aparecerá um erro específico (ex: permissão negada, PHP não encontrado).

#### 3. **Se o PHP não estiver no caminho padrão**:
```bash
# Verifique onde está o PHP
which php
```
- Se retornar algo como `/usr/local/bin/php`, atualize o `ExecStart` no serviço:
  ```ini
  ExecStart=/usr/local/bin/php -S 0.0.0.0:9999 -t /home/shaffer443/MeusProjetos/...
  ```

#### 4. **Verifique se a porta 9999 está livre**:
```bash
sudo lsof -i :9999  # Se estiver em uso, mate o processo ou mude a porta
```

#### 5. **Ative logs detalhados (opcional)**:
Adicione ao arquivo de serviço (`[Service]`):
```ini
StandardOutput=journal
StandardError=journal
```
Depois recarregue e reinicie:
```bash
sudo systemctl daemon-reload
sudo systemctl restart php-server-webfinancas.service
```

#### 6. **Verifique os logs novamente**:
```bash
sudo journalctl -u php-server-webfinancas.service -b --no-pager
```

---

### **Possíveis Causas do Erro**:
- **Diretório não existe** → Verifique o caminho em `WorkingDirectory`.
- **www-data não tem permissão** → Corrija com `chown`/`chmod`.
- **PHP não encontrado** → Atualize o caminho em `ExecStart`.
- **Porta bloqueada** → Use outra porta (ex: `8080`).

---

### **Se ainda não funcionar**:
1. **Compartilhe a saída do teste manual (`sudo -u www-data php -S ...`)**.
2. **Verifique se há um `index.php` ou `index.html` no diretório** (o PHP pode estar recusando iniciar sem um arquivo padrão).

Se tudo estiver correto, o serviço deve iniciar e ficar **`Active: active (running)`**. 🚀

---

Ah, agora o problema ficou claro! O sistema diz que o diretório não existe, mas você confirmou que ele está lá. Isso acontece porque:

### 🕵️‍♂️ Causa do problema:
O usuário `www-data` **não tem permissão para navegar** até o diretório `/home/shaffer443/`. Por padrão, diretórios home são restritos ao seu usuário.

### ✅ Solução definitiva:
Temos 3 opções:

#### 1️⃣ **Mudar as permissões do diretório home (recomendado)**
```bash
# Permite que www-data acesse o diretório
sudo chmod 755 /home/shaffer443
sudo chmod -R 755 /home/shaffer443/MeusProjetos
```

#### 2️⃣ **Mover o projeto para um local acessível**
```bash
sudo mv /home/shaffer443/MeusProjetos/Financeiro /var/www/
sudo chown -R www-data:www-data /var/www/Financeiro
```
E atualize o serviço para:
```ini
WorkingDirectory=/var/www/Financeiro/webfinancasPessoais
ExecStart=/usr/bin/php -S 0.0.0.0:9999 -t /var/www/Financeiro/webfinancasPessoais
```

#### 3️⃣ **Mudar o usuário do serviço (alternativa)**
Edite o serviço trocando:
```ini
User=www-data  →  User=shaffer443
Group=www-data →  Group=shaffer443
```

### 🔍 Como testar:
```bash
# Teste como www-data
sudo -u www-data ls /home/shaffer443/MeusProjetos/Financeiro/webfinancasPessoais
```

### 💡 Dica importante:
Se optar pela solução 1, após mudar as permissões:
```bash
sudo systemctl restart php-server-webfinancas.service
sudo systemctl status php-server-webfinancas.service
```

Isso deve resolver definitivamente o problema! O serviço agora deverá iniciar corretamente.
