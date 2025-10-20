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

Fico à disposição quando você decidir fazer a mudança ou se precisar de ajuda com qualquer outra coisa no servidor!
