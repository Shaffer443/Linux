Para criar um servidor web usando PHP em um servidor Ubuntu, você pode seguir as etapas abaixo. Vou explicar como liberar uma porta específica no firewall, configurar o servidor PHP embutido, e garantir que ele inicie automaticamente ao reiniciar o sistema.

---
Configurar Firewall com UFW no Ubuntu

Como mencionado antes, o UFW está instalado por padrão no Ubuntu, mas não está habilitado. Então a primeira coisa que precisamos fazer é habilitá-lo.

Depois de conectar com o SSH, vamos executar o seguinte comando para habilitar o UFW:
```bash
sudo ufw enable
```
Se você recebeu o erro de “comando não encontrado”, instale o UFW executando:
```bash
sudo apt-get install ufw
```
Em seguida, iremos verificar o status do UFW:
```bash
sudo ufw status
```
Neste momento o UFW deverá estar ativado.

Por padrão, o UFW nega qualquer conexão recebida e permite todas as conexões realizadas. Para muitos usuários, isto já é suficiente. Mas para quem possui serviços online ou aplicações, é preciso criar algumas regra
---
### Passo 1: Liberar a Porta no Firewall
Por padrão, servidores PHP embutidos rodam na porta `8000`, mas você pode alterar conforme necessário.

1. Verifique se o `ufw` (firewall) está ativo:
   ```bash
   sudo ufw status
   ```

2. Libere a porta desejada (exemplo: `8000`):
   ```bash
   sudo ufw allow 8000/tcp
   ```

3. Verifique se a regra foi aplicada:
   ```bash
   sudo ufw status
   ```

---

### Passo 2: Configurar o Servidor PHP Embutido
O PHP já vem com um servidor embutido que pode ser utilizado para propósitos simples de desenvolvimento. Siga as etapas:

1. Navegue até o diretório onde seus arquivos PHP estão localizados:
   ```bash
   cd /caminho/do/seu/projeto
   ```

2. Inicie o servidor PHP na porta desejada (exemplo: porta `8000`):
   ```bash
   php -S 0.0.0.0:8000
   ```

   Isso iniciará o servidor PHP e o deixará acessível para todas as máquinas na rede (0.0.0.0).

---

### Passo 3: Configurar o Servidor para Iniciar Automaticamente
Você pode criar um **arquivo de serviço do systemd** para que o servidor PHP inicie automaticamente.

1. Crie um arquivo de serviço para o systemd:
   ```bash
   sudo nano /etc/systemd/system/php-server.service
   ```

2. Adicione o seguinte conteúdo ao arquivo (ajuste o caminho do seu projeto e a porta):
   ```ini
   [Unit]
   Description=Servidor PHP embutido
   After=network.target

   [Service]
   ExecStart=/usr/bin/php -S 0.0.0.0:8000 -t /caminho/do/seu/projeto
   Restart=always
   User=www-data
   Group=www-data
   WorkingDirectory=/caminho/do/seu/projeto

   [Install]
   WantedBy=multi-user.target
   ```

   **Explicação dos campos:**
   - `ExecStart`: Comando para iniciar o servidor.
   - `User` e `Group`: Usuário e grupo que executam o servidor (use `www-data` ou o usuário apropriado).
   - `WorkingDirectory`: O diretório onde seus arquivos PHP estão.

3. Salve o arquivo e feche o editor.

4. Atualize os serviços do systemd:
   ```bash
   sudo systemctl daemon-reload
   ```

5. Inicie o serviço manualmente para testar:
   ```bash
   sudo systemctl start php-server
   ```

6. Verifique o status do serviço:
   ```bash
   sudo systemctl status php-server
   ```

7. Habilite o serviço para iniciar automaticamente no boot:
   ```bash
   sudo systemctl enable php-server
   ```

---

### Passo 4: Testar o Servidor Web
1. Abra um navegador e acesse:
   ```
   http://<endereço_do_servidor>:8000
   ```
   Substitua `<endereço_do_servidor>` pelo IP ou hostname do servidor Ubuntu.

2. Certifique-se de que os arquivos PHP sejam carregados corretamente.

---

Se precisar de mais ajuda, avise! 🚀
