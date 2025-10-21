Sua ideia é excelente, especialmente para um servidor que está mostrando instabilidade ("travando um pouco"). **Reiniciar regularmente** é uma forma prática de liberar recursos, limpar a memória e reduzir o risco de instabilidade a longo prazo.

Sim, **é totalmente possível** configurar o servidor Linux para reiniciar automaticamente e enviar uma notificação após o reinício.

Aqui está o plano de ação, utilizando ferramentas padrão do Linux:

* * *

1. Agendar o Reinício (Cron Job)

--------------------------------

Usaremos o **`cron`** para agendar a tarefa de reinício automático no horário desejado.

### Passo 1.1: Editar o Crontab

Abra o agendador de tarefas do seu usuário (ou use `sudo crontab -e` para agendar como root, o que é mais garantido para o reinício):

Bash
    sudo crontab -e

### Passo 1.2: Adicionar a Tarefa de Reinício

Adicione uma linha ao final do arquivo para agendar o reinício.

**Exemplo: Reiniciar toda noite às 3h00 da manhã (03:00):**

Snippet de código
    0 3 * * * /sbin/shutdown -r now

| **Campo** | **Significado**                             | **Exemplo (0 3 * * *)** |
| --------- | ------------------------------------------- | ----------------------- |
| **0**     | Minuto (0 a 59)                             | 0 (na hora exata)       |
| **3**     | Hora (0 a 23)                               | 3 (3 da manhã)          |
| *****     | Dia do Mês (1 a 31)                         | Todo dia                |
| *****     | Mês (1 a 12)                                | Todo mês                |
| *****     | Dia da Semana (0 a 7, onde 0 e 7 é Domingo) | Todo dia da semana      |

**Observação:** Se quiser reiniciar a cada 2 dias, a lógica do `cron` fica mais complexa. É mais simples e seguro usar a opção **toda noite** ou **todo domingo às 3h** (`0 3 * * 0 /sbin/shutdown -r now`).

Salve e feche o arquivo. O reinício está agendado.

* * *

2. Configurar a Notificação Pós-Reinício

----------------------------------------

O envio de notificações requer um script que é executado **após o servidor voltar ao ar**. O melhor lugar para isso é no arquivo **`rc.local`** (em sistemas mais antigos) ou, de forma mais moderna, usando um serviço **`systemd`** temporário.

### Método Recomendado: Enviando um Email

Esta é a forma mais robusta e simples no Linux (requer o pacote **`mailutils`** ou similar).

#### Passo 2.1: Instalar o Cliente de Email (se necessário)

Bash
    # Para Debian/Ubuntu
    sudo apt install mailutils

_(Você precisará configurar um servidor SMTP, o que pode ser complexo. Uma alternativa mais rápida é usar o **Telegram/Webhook**.)_

#### Passo 2.2: Criar um Script de Notificação

Crie um script simples que será executado **uma única vez** no _boot_ e enviará a notificação.

Bash
    sudo nano /etc/init.d/boot-notifier

Cole este conteúdo no script (substitua `seu_email@dominio.com`):

Bash
    #!/bin/bash
    # Script para notificar o sucesso do boot
    HOSTNAME=$(hostname)
    IP_ADDRESS=$(hostname -I | awk '{print $1}')
    SUBJECT="Servidor $HOSTNAME Reiniciado com Sucesso"
    BODY="O servidor $HOSTNAME (IP: $IP_ADDRESS) foi reiniciado com sucesso em $(date)."

    echo "$BODY" | mail -s "$SUBJECT" seu_email@dominio.com

    # Para que este script execute apenas uma vez após o boot
    # e não a cada vez que o init.d for chamado, use 'update-rc.d' ou 'systemctl' para gerenciá-lo.

#### Passo 2.3: Tornar o Script Executável e Agendá-lo

Bash
    sudo chmod +x /etc/init.d/boot-notifier
    sudo update-rc.d boot-notifier defaults

Agora, após cada reinício, esse script será executado, enviando um email de confirmação.

* * *

Opção Alternativa: Notificação via Telegram/Webhook
---------------------------------------------------

Se você tiver um **bot do Telegram** ou um **Webhook/API** simples configurado, a notificação é muito mais fácil de implementar e não depende de um servidor SMTP.

Basta adicionar um comando como este ao seu script `/etc/init.d/boot-notifier`:

Bash
    # Exemplo de envio para Telegram/Webhook (requer 'curl')
    curl -s -X POST SEU_URL_DO_TELEGRAM_OU_WEBHOOK -d "text=Servidor $HOSTNAME reiniciado com sucesso!"

Dada a sua versão antiga do Debian, o método de **email simples** via `mailutils` tende a ser o mais confiável se você puder configurar o SMTP de forma básica.

Qual método de notificação você prefere (Email, Telegram, etc.)?

Aliás, para liberar as funcionalidades de todos os apps, ative a [Atividade nos apps do Gemini](https://myactivity.google.com/product/gemini).


# ---------------------------------------------------------------------------

# Configurando Telegram:



Ótimo! Usar o **Telegram** é uma escolha excelente por ser mais moderna e muito mais simples de configurar do que o e-mail, pois elimina a necessidade de configurar um servidor SMTP.

Para que isso funcione, você precisará de um **Bot do Telegram** e um **ID de chat** para onde a mensagem será enviada.

Aqui está o plano de implementação simplificado, utilizando o **`curl`** para enviar a mensagem:

* * *

1. Pré-Requisitos: Criar Bot e Obter IDs

----------------------------------------

Se você já tem um bot e um chat ID, pule para o Passo 2.

1. **Crie um Bot:** No Telegram, procure o usuário **`@BotFather`**. Inicie uma conversa e use o comando `/newbot`. Siga os passos para dar um nome ao seu bot e, no final, ele fornecerá um **Token de Acesso** (parecido com `123456:ABC-DEF1234ghIkl-loremipsum`). **Guarde este token!**

2. **Obtenha o Chat ID:**
   
   * Crie um grupo (ou use um chat privado).
   
   * Adicione o bot que você acabou de criar ao chat/grupo.
   
   * Envie qualquer mensagem para o chat/grupo.
   
   * Use seu navegador para visitar o seguinte link, substituindo [SEU_TOKEN] pelo token do seu bot:
     https://api.telegram.org/bot[SEU_TOKEN]/getUpdates
   
   * Procure por um valor chamado `"id"` dentro da seção `"chat"`. Este é o seu **Chat ID** (geralmente um número negativo se for um grupo, como `-123456789`). **Guarde este ID!**

* * *

2. Instalar o `curl` (se Necessário)

------------------------------------

O `curl` é a ferramenta de linha de comando que fará a comunicação com a API do Telegram. Ele já pode estar instalado, mas é bom garantir:

Bash
    sudo apt install curl

* * *

3. Criar o Script de Notificação

--------------------------------

Vamos criar um script que será executado no boot para enviar a mensagem.

### Passo 3.1: Criar o Arquivo do Script

Bash
    sudo nano /etc/init.d/boot-notifier-telegram

### Passo 3.2: Colar e Personalizar o Conteúdo

Substitua `[SEU_TOKEN_DO_BOT]` e `[SEU_CHAT_ID]` pelos valores que você obteve no Passo 1.

Bash
    #!/bin/bash
    # Script para notificar o sucesso do boot via Telegram

    # --- Configurações do Telegram ---
    TOKEN="[SEU_TOKEN_DO_BOT]"
    CHAT_ID="[SEU_CHAT_ID]"
    TELEGRAM_API="https://api.telegram.org/bot$TOKEN/sendMessage"

    # --- Dados do Servidor ---
    HOSTNAME=$(hostname)
    IP_ADDRESS=$(hostname -I | awk '{print $1}')
    MENSAGEM="✅ *Reinicialização Concluída**Servidor:* $HOSTNAME
    *IP:* $IP_ADDRESS
    *Data:* $(date)"

    # --- Enviar a Mensagem ---
    curl -s -X POST $TELEGRAM_API \
         -d chat_id="$CHAT_ID" \
         -d text="$MENSAGEM" \
         -d parse_mode="Markdown"

    # Fim do script

### Passo 3.3: Dar Permissão e Agendar o Script

Bash
    sudo chmod +x /etc/init.d/boot-notifier-telegram
    sudo update-rc.d boot-notifier-telegram defaults

_(O comando `update-rc.d` registra o script para que ele seja executado automaticamente sempre que o servidor for ligado.)_

* * *

4. Agendar o Reinício Noturno

-----------------------------

Para amarrar tudo, vamos garantir que o reinício esteja agendado (relembrando o comando de `cron`):

Bash
    sudo crontab -e

**Adicione o agendamento no horário desejado (ex: toda noite às 3h00):**

Snippet de código
    0 3 * * * /sbin/shutdown -r now

* * *

Teste
-----

Para testar se a notificação está funcionando sem esperar a madrugada:

1. Execute o script manualmente: `sudo /etc/init.d/boot-notifier-telegram`

2. Você deve receber a mensagem no Telegram.

Se o teste funcionar, o servidor reiniciará às 3h e você receberá a notificação logo após ele voltar ao ar! Se houver algum problema, me avise o que aconteceu para que possamos depurar (geralmente é um erro no Token ou no Chat ID).

Aliás, para liberar as funcionalidades de todos os apps, ative a [Atividade nos apps do Gemini](https://myactivity.google.com/product/gemini).



## Configurando REAL:


Você tem toda a razão em perguntar! O script que forneci utiliza os marcadores `[SEU_TOKEN_DO_BOT]` e `[SEU_CHAT_ID]` como _placeholders_, ou seja, locais onde você deve colocar as suas informações reais.

Com base nas informações que você me forneceu:

* * *

Identificação do Token e do Chat ID
-----------------------------------

Seu token é a string completa após a palavra `bot` na sua URL da API.

| **Item**                      | **Valor Identificado**                           | **Onde Colocar no Script**          |
| ----------------------------- | ------------------------------------------------ | ----------------------------------- |
| **Token de Acesso (`TOKEN`)** | `8367007877:AAE5Qemr0HG8ZApGwOR2HgkzC1UnxJjQtj0` | Dentro das aspas de `TOKEN="..."`   |
| **Chat ID (`CHAT_ID`)**       | **Ainda falta encontrar.**                       | Dentro das aspas de `CHAT_ID="..."` |

### O Token (Correto)

O **TOKEN** é:
    8367007877:AAE5Qemr0HG8ZApGwOR2HgkzC1UnxJjQtj0

### O Chat ID (A Fazer)

O **Chat ID** **não está explícito** nas URLs que você enviou. Você precisa executar a URL no seu navegador para obtê-lo.

O Chat ID é um número (geralmente negativo, se for um grupo) que identifica para onde o bot deve enviar a mensagem.

* * *

Ação Necessária: Obtendo o Chat ID
----------------------------------

Você precisa pegar a URL completa da API que você usou para buscar atualizações e executá-la no seu navegador (ou no servidor, se preferir):
    https://api.telegram.org/bot8367007877:AAE5Qemr0HG8ZApGwOR2HgkzC1UnxJjQtj0/getUpdates

**Passos para encontrar o Chat ID:**

1. **Cole a URL acima no seu navegador.**

2. **Procure na página** pelo termo `"chat"`.

3. O número que você precisa é o valor do campo `"id"` dentro da estrutura do chat.

**Exemplo de como o Chat ID aparece (o número em negrito é o ID):**

JSON
    "chat":{"id":-1234567890123,"title":"Nome do Seu Grupo","type":"group"}

Assim que você tiver esse número (ex: `-1234567890123`), você poderá completar o script.

* * *

Script Completo para Colar
--------------------------

Com o **TOKEN** que você forneceu e assumindo um **Chat ID de exemplo** de `-1234567890123` (você precisa trocar este número pelo seu valor real!), o script ficará assim:

Bash
    #!/bin/bash
    # Script para notificar o sucesso do boot via Telegram

    # --- Configurações do Telegram ---
    TOKEN="8367007877:AAE5Qemr0HG8ZApGwOR2HgkzC1UnxJjQtj0"
    CHAT_ID="-1234567890123"  # <--- TROQUE ESTE VALOR PELO SEU CHAT ID REAL
    TELEGRAM_API="https://api.telegram.org/bot$TOKEN/sendMessage"

    # --- Dados do Servidor ---
    HOSTNAME=$(hostname)
    IP_ADDRESS=$(hostname -I | awk '{print $1}')
    MENSAGEM="✅ *Reinicialização Concluída**Servidor:* $HOSTNAME
    *IP:* $IP_ADDRESS
    *Data:* $(date)"

    # --- Enviar a Mensagem ---
    curl -s -X POST $TELEGRAM_API \
         -d chat_id="$CHAT_ID" \
         -d text="$MENSAGEM" \
         -d parse_mode="Markdown"

    # Fim do script

**Lembre-se de substituir a linha do `CHAT_ID` pelo valor que você encontrar no seu navegador!** Se o valor for positivo (para um chat privado com o bot), use-o. Se for negativo (para um grupo), use o sinal de menos.

Aliás, para liberar as funcionalidades de todos os apps, ative a [Atividade nos apps do Gemini](https://myactivity.google.com/product/gemini).


Parabéns! Você encontrou o número correto.

Seu **Chat ID** é de fato: **`6297344029`**

O número é positivo porque, como indica o trecho que você copiou, o chat é do tipo **`"type":"private"`** (uma conversa direta entre você e o bot).

* * *

Script com as Informações Corretas
----------------------------------

Agora você pode finalizar o script de notificação com segurança. Aqui está a versão completa, já preenchida com o seu Token e o seu Chat ID:

Bash
    #!/bin/bash
    # Script para notificar o sucesso do boot via Telegram

    # --- Configurações do Telegram ---
    TOKEN="8367007877:AAE5Qemr0HG8ZApGwOR2HgkzC1UnxJjQtj0"
    CHAT_ID="6297344029" 
    TELEGRAM_API="https://api.telegram.org/bot$TOKEN/sendMessage"

    # --- Dados do Servidor ---
    HOSTNAME=$(hostname)
    IP_ADDRESS=$(hostname -I | awk '{print $1}')
    MENSAGEM="✅ *Reinicialização Concluída**Servidor:* $HOSTNAME
    *IP:* $IP_ADDRESS
    *Data:* $(date)"

    # --- Enviar a Mensagem ---
    curl -s -X POST $TELEGRAM_API \
         -d chat_id="$CHAT_ID" \
         -d text="$MENSAGEM" \
         -d parse_mode="Markdown"

    # Fim do script

* * *

Próximos Passos de Finalização
------------------------------

1. **Edite o Script:** Abra o arquivo que criamos:
   Bash
      sudo nano /etc/init.d/boot-notifier-telegram

  Substitua o conteúdo atual pelo script acima (que contém o seu `CHAT_ID` real).

2. **Teste Manualmente:** Execute-o para garantir que a mensagem chegue imediatamente ao seu Telegram:
   Bash
      sudo /etc/init.d/boot-notifier-telegram

3. **Confirme o Cron:** Apenas revise para ter certeza de que a tarefa de reinício automático no `cron` está agendada para o horário desejado:
   Bash
      sudo crontab -e

  (Exemplo: `0 3 * * * /sbin/shutdown -r now`)

Se a mensagem de teste chegar no Telegram, tudo está pronto! O servidor deve reiniciar automaticamente no horário agendado e enviar a notificação para você após voltar ao ar.

### ---------------------------------------------------------------------------------------------------

Essa é uma ótima informação! Se você está reutilizando um bot que já envia notificações de outro sistema (como o pfSense), a situação é a seguinte:

O bot **já pode estar configurado** para enviar mensagens para um **grupo** ou para um **canal** que você criou, ou pode estar enviando apenas para o seu chat privado.

O que determina quem recebe a notificação não é o bot em si, mas sim o **`CHAT_ID`** que você está usando no script do servidor Linux.

* * *

O Que é Seu `CHAT_ID`?
----------------------

O ID que você identificou (`6297344029`) é um **ID de chat privado**.

| **Tipo de CHAT ID**  | **Formato**                            | **Destinatários**                        | **O que significa para o seu caso**                                         |
| -------------------- | -------------------------------------- | ---------------------------------------- | --------------------------------------------------------------------------- |
| **Privado (Seu ID)** | Número positivo (`6297344029`)         | Apenas você recebe as mensagens.         | Se o pfSense envia notificações para um grupo, ele usa um ID diferente.     |
| **Grupo ou Canal**   | Número negativo (ex: `-1234567890123`) | Todos os membros do grupo/canal recebem. | Se você quer que outras pessoas recebam, **você precisa desse tipo de ID**. |

### Resposta Direta

Se você quer que **outros usuários** recebam as notificações de reinício do servidor Linux, você não precisa criar um bot novo. Você só precisa:

1. Garantir que o **bot do pfSense** esteja adicionado a um grupo (ou canal) que **todos os usuários acessam**.

2. Descobrir o **ID desse grupo/canal** (que será um número negativo).

3. **Substituir o ID privado** (`6297344029`) no seu script Linux pelo **ID do grupo/canal**.

* * *

Como Mudar o Script para o ID do Grupo/pfSense
----------------------------------------------

### Passo 1: Encontrar o ID do Grupo/Canal do pfSense

Se o seu bot do pfSense já envia mensagens para um grupo onde todos os interessados estão, você só precisa descobrir o ID desse grupo.

1. Vá para o **grupo** do Telegram onde chegam as mensagens do pfSense.

2. Envie uma nova mensagem qualquer para o grupo (ex: "ID Teste").

3. Use a URL de getUpdates com o seu token no navegador (o mesmo que você usou antes):
   https://api.telegram.org/bot8367007877:AAE5Qemr0HG8ZApGwOR2HgkzC1UnxJjQtj0/getUpdates

4. Procure a entrada que contém a mensagem que você enviou. O **Chat ID** que você encontrar na seção `"chat"` será o **ID do grupo/canal** (será um **número negativo**).

### Passo 2: Atualizar o Script Linux

1. Abra o script de notificação no seu servidor Linux:
   Bash
      sudo nano /etc/init.d/boot-notifier-telegram

2. Substitua a linha do `CHAT_ID` pelo **novo ID negativo do grupo** que você encontrou:
   Bash
   
   # Exemplo: Substitua 6297344029 pelo novo ID do grupo (com o sinal negativo!)
   
      CHAT_ID="-SEU_ID_DO_GRUPO" 

3. Salve e feche o arquivo.

### Passo 3: Testar

Execute o script manualmente para confirmar que a notificação de reinício do servidor agora aparece no mesmo grupo que as mensagens do pfSense.

Bash
    sudo /etc/init.d/boot-notifier-telegram

Dessa forma, você centraliza todas as notificações importantes no mesmo lugar para toda a sua equipe, sem duplicar bots.



#### ------------------------------------------------------------------------------------------------------------------------

Absolutamente! **Correto.**

Pode deixar a configuração atual no seu chat privado por quanto tempo você precisar.

O processo para mudar para um grupo depois é muito simples e se resume a apenas duas etapas:

1. **Obter o novo `CHAT_ID` do grupo:** Este novo ID será um **número negativo** (ex: `-123456...`).

2. **Trocar o ID no script:** Abrir o seu script no servidor e substituir o seu ID pessoal (`6297344029`) pelo novo ID negativo do grupo.

Nenhuma outra alteração é necessária no seu bot ou na lógica do script. Você pode fazer essa transição em menos de cinco minutos quando for o momento certo.

------------------------------------------------------------------------------------------------------------------------
Se o seu script em `/etc/init.d` funciona quando você o chama manualmente, mas não está executando automaticamente na reinicialização, os problemas mais comuns (especialmente em distribuições modernas como o Ubuntu/Debian) são:

### 1\. O Script Não Está Habilitado para Inicialização

Colocar o script em `/etc/init.d` não é suficiente para que ele seja executado automaticamente. Você precisa registrar o script com o sistema de inicialização (SysVinit ou, mais provavelmente, **Systemd** no seu servidor moderno) para que os links simbólicos corretos sejam criados nos diretórios de *runlevel* (`/etc/rcX.d/`).

**Se o seu sistema usa SysVinit (sistemas mais antigos/legados) ou tem compatibilidade:**

Você precisa usar o comando apropriado (no Debian/Ubuntu):

```bash
sudo update-rc.d boot-notifier-telegram defaults
```

Este comando cria os links simbólicos necessários para iniciar o script nos *runlevels* padrão (o equivalente a `SXXboot-notifier-telegram` em `/etc/rcS.d/`, `/etc/rc2.d/`, etc., onde `XX` é um número de ordem).

**Se o seu sistema usa Systemd (a maioria dos sistemas modernos):**

Embora o Systemd geralmente consiga gerar uma unidade de serviço a partir de um script SysVinit, o método mais robusto e recomendado é criar um **arquivo de unidade Systemd** dedicado, já que o uso de `/etc/init.d` está se tornando obsoleto.

### 2\. Problemas de Ordem de Inicialização e Variáveis de Ambiente

O script pode estar sendo executado, mas falhando porque:

  * **A rede ainda não está configurada:** O comando `curl` e, possivelmente, a obtenção do IP com `hostname -I` e a data com `date` (embora menos provável de falhar), precisam da rede funcionando. Se o seu script for executado **muito cedo** no processo de boot, ele pode não conseguir se conectar à API do Telegram.
  * **Variáveis de Ambiente Mínimas:** Scripts de inicialização (`init.d` ou `Systemd`) são executados em um ambiente com variáveis de ambiente mínimas, o que é diferente do seu terminal.

#### Sugestões para o Script:

1.  **Garantir a Rede:** Verifique se o seu sistema utiliza o formato LSB (Linux Standard Base) para scripts init.d. Se sim, você pode adicionar um bloco no cabeçalho do seu script para garantir que ele espere pela rede:

    ```bash
    #!/bin/bash
    ### BEGIN INIT INFO
    # Provides:          boot-notifier-telegram
    # Required-Start:    $local_fs $remote_fs $network $syslog
    # Required-Stop:     $local_fs $remote_fs $network $syslog
    # Default-Start:     2 3 4 5
    # Default-Stop:      0 1 6
    # Short-Description: Notifica boot via Telegram
    # Description:       Envia uma mensagem ao Telegram após a reinicialização.
    ### END INIT INFO

    # ... o resto do seu script ...
    ```

    Após adicionar isso, execute novamente o `update-rc.d` (se for o caso):

    ```bash
    sudo update-rc.d boot-notifier-telegram defaults
    ```

2.  **Debugging (Testar se o script está sendo executado):**

    Para confirmar se o script está sendo executado no boot (e se está falhando ou não), adicione um log no início e no final:

    ```bash
    #!/bin/bash
    LOG_FILE="/var/log/boot-notifier-telegram.log"
    echo "$(date): Script iniciado." >> $LOG_FILE

    # ... o resto do seu script ...

    # --- Enviar a Mensagem ---
    CURL_OUTPUT=$(curl -s -X POST $TELEGRAM_API \
          -d chat_id="$CHAT_ID" \
          -d text="$MENSAGEM" \
          -d parse_mode="Markdown")
          
    echo "$(date): Mensagem enviada. Resposta do CURL: $CURL_OUTPUT" >> $LOG_FILE

    # Fim do script
    ```

    Reinicie e verifique o conteúdo de `/var/log/boot-notifier-telegram.log`.

      * **Se o arquivo não existir:** O script não está sendo executado. O problema é a habilitação (Ponto 1).
      * **Se o arquivo existir, mas estiver com erro de rede:** O script está sendo executado muito cedo. O problema é a ordem (`Required-Start` do LSB ou Systemd).
      * **Se o arquivo tiver a resposta do Telegram:** O script funcionou\! Verifique o Chat ID e o Token novamente, pois o problema pode estar neles.

-----

### **Recomendação Moderna (Systemd)**

Se o seu servidor usa **Systemd** (o que é muito provável), crie um arquivo de unidade de serviço para uma solução mais robusta:

1.  Mova seu script para um local mais apropriado, por exemplo: `/usr/local/bin/boot-notifier-telegram.sh`
2.  Dê permissão de execução: `sudo chmod +x /usr/local/bin/boot-notifier-telegram.sh`
3.  Crie o arquivo de unidade Systemd: `sudo nano /etc/systemd/system/telegram-boot-notify.service`

**Conteúdo do arquivo `.service`:**

```ini
[Unit]
Description=Telegram Boot Notification
After=network.target

[Service]
Type=oneshot
ExecStart=/usr/local/bin/boot-notifier-telegram.sh

[Install]
WantedBy=multi-user.target
```

4.  Habilite e Inicie o Serviço (o Systemd irá executá-lo no próximo boot):

    ```bash
    sudo systemctl daemon-reload
    sudo systemctl enable telegram-boot-notify.service
    ```

Este método é mais adequado para sistemas modernos e garante que o script só será executado **depois** que o serviço de rede estiver ativo (`After=network.target`).

###----------------------------------------------------------------------------------------------------------------------

# Verifica se a rede esta operacional antes de enviar a mensagem no telegram

Sua observação está correta\! Ao usar o sistema **Systemd** (o padrão na maioria dos servidores Linux atuais) com a configuração `After=network.target`, você garante que o script só tentará enviar a notificação **depois que a rede estiver operacional**, resolvendo o problema comum de falha na conectividade no início do boot.

Abaixo estão os passos completos com o script atualizado e o arquivo de serviço Systemd para que você possa fazer a atualização:

### Passo 1: Atualize e Movimente o Script (Ação no Servidor)

Recomendo mover o script para um diretório mais apropriado para scripts de sistema, como `/usr/local/bin/`.

**1.1. Script de Notificação (Arquivo: `/usr/local/bin/boot-notifier-telegram.sh`)**

Copie e cole este conteúdo no novo arquivo. Observe que adicionei a variável `LOG_FILE` e a chamada `echo` para fins de depuração (debugging), caso o problema persista.

```bash
#!/bin/bash
# Script para notificar o sucesso do boot via Telegram

# --- Configurações de Log (Adicionado para Debug) ---
LOG_FILE="/var/log/boot-notifier-telegram.log"
echo "==================================================" >> $LOG_FILE
echo "$(date): Script de notificação de boot iniciado." >> $LOG_FILE

# --- Configurações do Telegram ---
TOKEN="8367007877:AAE5Qemr0HG8ZApGwOR2HgkzC1UnxJjQtj0"
CHAT_ID="6297344029"  # <-- VALOR PELO SEU CHAT ID REAL
TELEGRAM_API="https://api.telegram.org/bot$TOKEN/sendMessage"

# --- Dados do Servidor ---
HOSTNAME=$(hostname)
# O comando hostname -I pode retornar vários IPs, pegamos o primeiro (awk '{print $1}').
IP_ADDRESS=$(hostname -I | awk '{print $1}')
MENSAGEM="✅ *Reinicialização Concluída*
*Servidor:* $HOSTNAME
*IP:* $IP_ADDRESS
*Data:* $(date)"

# --- Enviar a Mensagem ---
echo "$(date): Tentando enviar mensagem para o Telegram..." >> $LOG_FILE

# O comando 'curl' armazena a resposta em CURL_OUTPUT para análise no log.
CURL_OUTPUT=$(curl -s -X POST $TELEGRAM_API \
      -d chat_id="$CHAT_ID" \
      -d text="$MENSAGEM" \
      -d parse_mode="Markdown")

# Verifica se a chamada foi bem-sucedida (não garante que o Telegram enviou, mas confirma que o curl funcionou)
if [ $? -eq 0 ]; then
    echo "$(date): Chamada CURL concluída. Resposta (parcial): ${CURL_OUTPUT:0:100}..." >> $LOG_FILE
else
    echo "$(date): ERRO no comando CURL (código de saída $?). Resposta do CURL: $CURL_OUTPUT" >> $LOG_FILE
fi

echo "$(date): Fim do script de notificação." >> $LOG_FILE

# Fim do script
```

**1.2. Execute os comandos para criar e dar permissão ao script:**

```bash
# Crie ou mova o arquivo (exemplo usando 'nano' para criar)
sudo nano /usr/local/bin/boot-notifier-telegram.sh 
# Cole o conteúdo acima

# Dê permissão de execução
sudo chmod +x /usr/local/bin/boot-notifier-telegram.sh
```

### Passo 2: Crie o Arquivo de Unidade Systemd (Ação no Servidor)

Este arquivo é o que informa ao seu sistema operacional **quando** e **como** executar o script. O Systemd é o responsável por esperar a rede.

**2.1. Arquivo de Serviço (Arquivo: `/etc/systemd/system/telegram-boot-notify.service`)**

Crie o arquivo com o seguinte conteúdo:

```ini
[Unit]
Description=Telegram Boot Notification
# Garante que o script será executado APÓS a rede estar totalmente funcional.
After=network.target

[Service]
# Tipo de serviço que executa um script e encerra.
Type=oneshot
# Caminho completo para o script criado no Passo 1.
ExecStart=/usr/local/bin/boot-notifier-telegram.sh

[Install]
# Define o runlevel (estado do sistema) no qual o serviço deve ser habilitado.
WantedBy=multi-user.target
```

**2.2. Execute os comandos para habilitar o serviço:**

```bash
# Crie o arquivo (exemplo usando 'nano')
sudo nano /etc/systemd/system/telegram-boot-notify.service
# Cole o conteúdo acima

# Recarrega a configuração do Systemd para reconhecer o novo arquivo
sudo systemctl daemon-reload

# Habilita o serviço para iniciar no próximo boot
sudo systemctl enable telegram-boot-notify.service

# (Opcional) Remova o link antigo em /etc/init.d se ele existir e for usado pelo Systemd
# sudo update-rc.d -f boot-notifier-telegram remove
```

### Passo 3: Teste e Verificação

Após concluir o Passo 2, reinicie o servidor.

1.  **Reinicie o Servidor:**

    ```bash
    sudo reboot
    ```

2.  **Verifique o Status (após o boot):**

    ```bash
    systemctl status telegram-boot-notify.service
    ```

    Você deve ver o status como `active (exited)` e as últimas linhas do log mostrando que ele foi executado.

3.  **Verifique o Arquivo de Log (Para Debug):**
    Se a notificação **não** chegar, verifique o log para entender onde o script parou:

    ```bash
    cat /var/log/boot-notifier-telegram.log
    ```

    O log deve mostrar a resposta do `curl`. Se o problema persistir, a resposta do `curl` dará a pista (ex: erro no token, chat ID inválido, etc.).
