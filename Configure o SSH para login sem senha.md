É possível criar um alias para facilitar a execução do seu script Python no servidor sem precisar logar manualmente toda vez. Você pode configurar o alias no **cliente** (seu desktop), para que ele se conecte automaticamente ao servidor via SSH e execute o script Python com Tkinter. Aqui está como você pode fazer isso:

---

### Passos para criar um alias no cliente:

1. **Configure o SSH para login sem senha**:
   Para automatizar o login ao servidor sem precisar digitar a senha toda vez, você pode configurar a autenticação por chave SSH.

   No cliente (seu desktop), gere um par de chaves SSH, caso ainda não tenha:
   ```bash
   ssh-keygen -t rsa
   ```

   Depois, copie a chave pública para o servidor:
   ```bash
   ssh-copy-id usuario@ip_do_servidor
   ```

   Teste se a autenticação por chave funciona:
   ```bash
   ssh usuario@ip_do_servidor
   ```
   Você não deve precisar digitar a senha.

2. **Crie o alias no cliente**:
   No cliente, edite o arquivo `~/.bashrc` (ou `~/.zshrc`, se você usa Zsh) para adicionar um alias. Abra o arquivo com um editor:
   ```bash
   nano ~/.bashrc
   ```

   Adicione uma linha como esta:
   ```bash
   alias rodar_script="ssh -X usuario@ip_do_servidor 'python3 /caminho/para/seu/script/main.py'"
   ```
   - Substitua `usuario` pelo nome do usuário no servidor.
   - Substitua `ip_do_servidor` pelo endereço IP do servidor.
   - Substitua `/caminho/para/seu/script/main.py` pelo caminho completo para o script Python no servidor.

3. **Recarregue o arquivo de configuração do shell**:
   Após editar o arquivo `~/.bashrc`, recarregue-o:
   ```bash
   source ~/.bashrc
   ```

4. **Teste o alias**:
   Agora, você pode executar o alias para rodar o script diretamente do cliente:
   ```bash
   rodar_script
   ```
   Isso conectará ao servidor via SSH, redirecionará o ambiente gráfico com `-X`, e executará o script Python com Tkinter. A interface gráfica será exibida no cliente.

---

### Passos adicionais (opcionais):
Se o script precisa ser executado frequentemente e você deseja ainda mais facilidade, você pode criar um **atalho no desktop** (no Linux ou mesmo no Windows).

- Para Linux:
  Crie um arquivo `.desktop` com o comando que aponta para o alias:
  ```bash
  nano ~/Desktop/rodar_script.desktop
  ```
  Insira o conteúdo:
  ```plaintext
  [Desktop Entry]
  Name=Rodar Script Tkinter
  Exec=bash -c "rodar_script"
  Terminal=true
  Type=Application
  ```
  Torne o atalho executável:
  ```bash
  chmod +x ~/Desktop/rodar_script.desktop
  ```

- Para Windows:
  Instale um cliente SSH como [PuTTY](https://www.putty.org/) ou use o WSL (Windows Subsystem for Linux). No caso do PuTTY, você pode configurar uma sessão automática que execute o script.

---

### Considerações:
- Certifique-se de que o X11 Forwarding está configurado corretamente entre o cliente e o servidor, conforme expliquei antes.
- Lembre-se de que o alias só funciona no shell onde foi configurado. Se estiver usando outro shell, como Zsh, você deve configurá-lo no arquivo de inicialização correspondente (`~/.zshrc` em vez de `~/.bashrc`).

Se precisar de mais ajuda, me avise!
