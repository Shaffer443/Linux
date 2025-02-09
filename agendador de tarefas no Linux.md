O `cron` é um agendador de tarefas no Linux que permite executar comandos ou scripts automaticamente em horários específicos. Para agendar uma tarefa usando o `cron`, você pode seguir os passos abaixo:

### 1. Abrir o `crontab` para edição
O comando `crontab -e` abre o arquivo de configuração do `cron` para o usuário atual. Se você nunca usou o `cron` antes, ele pode abrir um editor de texto (como `nano` ou `vim`) para que você possa adicionar suas tarefas.

```bash
crontab -e
```

### 2. Entender o formato do `cron`
Cada linha no arquivo `crontab` representa uma tarefa agendada e segue este formato:

```
* * * * * comando_a_ser_executado
```

Os cinco asteriscos representam, respectivamente:

1. **Minuto** (0 - 59)
2. **Hora** (0 - 23)
3. **Dia do mês** (1 - 31)
4. **Mês** (1 - 12)
5. **Dia da semana** (0 - 7, onde 0 e 7 representam domingo)

### 3. Exemplos de agendamento

- **Executar um script todos os dias à meia-noite:**

  ```bash
  0 0 * * * /caminho/do/seu/script.sh
  ```

- **Executar um script a cada 15 minutos:**

  ```bash
  */15 * * * * /caminho/do/seu/script.sh
  ```

- **Executar um script às 8:30 da manhã, de segunda a sexta:**

  ```bash
  30 8 * * 1-5 /caminho/do/seu/script.sh
  ```

- **Executar um script todos os dias às 18:00:**

  ```bash
  0 18 * * * /caminho/do/seu/script.sh
  ```

### 4. Salvar e sair
Depois de adicionar a tarefa, salve o arquivo e saia do editor. O `cron` começará a executar a tarefa no horário especificado.

### 5. Verificar tarefas agendadas
Para verificar as tarefas agendadas, você pode usar o comando:

```bash
crontab -l
```

Isso listará todas as tarefas agendadas para o usuário atual.

### 6. Remover tarefas agendadas
Se você quiser remover todas as tarefas agendadas, use:

```bash
crontab -r
```

Isso removerá todas as tarefas do `cron` para o usuário atual.

### 7. Logs e erros
Se você quiser verificar se a tarefa está sendo executada corretamente ou se há erros, pode redirecionar a saída e os erros para um arquivo de log:

```bash
0 0 * * * /caminho/do/seu/script.sh >> /caminho/do/log.txt 2>&1
```

Isso redirecionará a saída padrão e os erros para o arquivo `log.txt`.

### 8. Considerações
- Certifique-se de que o script que você está agendando tem permissão de execução (`chmod +x script.sh`).
- Se o script depende de variáveis de ambiente, você pode precisar configurá-las no próprio script ou no `crontab`.

Com isso, você pode agendar tarefas para serem executadas automaticamente no seu sistema Linux usando o `cron`.

----
# Passo a Passo

Você está no caminho certo! Vamos configurar a tarefa no `cron` para executar o script `backup_mysql.sh` todos os dias às 18:00 (6 da tarde). Aqui está o passo a passo:

---

### 1. Escolha um editor de texto
No terminal, você viu a mensagem:

```
Select an editor.  To change later, run 'select-editor'.
  1. /bin/nano        <---- easiest
  2. /usr/bin/vim.basic
  3. /usr/bin/vim.tiny
  4. /bin/ed

Choose 1-4 [1]:
```

- Se você prefere um editor simples, escolha **1** (`nano`).
- Se você está familiarizado com o `vim`, escolha **2** ou **3**.

Digite o número correspondente ao editor que deseja usar e pressione **Enter**.

---

### 2. Adicionar a tarefa ao `cron`
Depois de escolher o editor, o arquivo `crontab` será aberto. Como é a primeira vez que você está usando o `cron`, ele estará vazio. Adicione a seguinte linha ao arquivo:

```bash
0 18 * * * /home/shaffer443/Script SHELL Pessoal/backup_mysql.sh
```

Isso significa:
- **0 18 * * ***: Executar todos os dias às 18:00.
- **/home/shaffer443/Script SHELL Pessoal/backup_mysql.sh**: Caminho completo do script que será executado.

---

### 3. Salvar e sair
- Se estiver usando o `nano`, pressione **Ctrl + O** para salvar e **Ctrl + X** para sair.
- Se estiver usando o `vim`, pressione **Esc**, digite **:wq** e pressione **Enter** para salvar e sair.

---

### 4. Verificar se a tarefa foi adicionada
Para confirmar que a tarefa foi adicionada corretamente, execute o comando:

```bash
crontab -l
```

Isso listará todas as tarefas agendadas. Você deve ver a linha que você adicionou:

```bash
0 18 * * * /home/shaffer443/Script SHELL Pessoal/backup_mysql.sh
```

---

### 5. Dicas adicionais
#### a) Permissão de execução
Certifique-se de que o script `backup_mysql.sh` tem permissão de execução. Execute o seguinte comando:

```bash
chmod +x /home/shaffer443/Script SHELL Pessoal/backup_mysql.sh
```

#### b) Logs de execução
Se você quiser registrar a saída do script (por exemplo, para verificar se ele está funcionando corretamente), redirecione a saída para um arquivo de log. Modifique a linha no `crontab` para:

```bash
0 18 * * * /home/shaffer443/Script SHELL Pessoal/backup_mysql.sh >> /home/shaffer443/backup_log.txt 2>&1
```

Isso salvará a saída e os erros no arquivo `backup_log.txt`.

#### c) Testar o script
Antes de confiar no `cron`, teste o script manualmente para garantir que ele funciona como esperado:

```bash
/home/shaffer443/Script SHELL Pessoal/backup_mysql.sh
```

---

### 6. Pronto!
Agora o script será executado automaticamente todos os dias às 18:00. Se precisar de mais ajuda, é só perguntar! 😊
