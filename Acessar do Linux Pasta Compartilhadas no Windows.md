Isso acontece porque o Windows 11, por padrão, exige autenticação para acessar pastas compartilhadas. Aqui estão algumas formas de resolver isso:  

---

### **Opção 1: Usar as credenciais do Windows**
1. No Windows 11, pressione `Win + R`, digite `cmd` e aperte `Enter`.
2. Digite `whoami` e aperte `Enter`. Isso mostrará seu usuário no formato `SEU-PC\seu-usuario`.
3. No Ubuntu, ao tentar acessar a pasta compartilhada, insira:
   - **Usuário:** o nome que apareceu no `whoami` (sem `SEU-PC\` se não funcionar).
   - **Senha:** a senha do seu usuário do Windows (se você usa PIN no Windows, tente a senha real da conta).

---

### **Opção 2: Criar um usuário para compartilhamento**  
Se você não quer usar seu usuário principal do Windows:  
1. No Windows, vá para **Configurações** → **Contas** → **Família e outros usuários**.
2. Clique em **Adicionar outro usuário** e crie um usuário local sem conta da Microsoft.
3. Defina uma senha e use essas credenciais ao acessar a pasta pelo Ubuntu.

---

### **Opção 3: Desativar a exigência de senha no compartilhamento**
Se quer acessar sem precisar digitar usuário e senha:  
1. No Windows, pressione `Win + R`, digite `control /name Microsoft.NetworkAndSharingCenter` e aperte `Enter`.
2. No lado esquerdo, clique em **Alterar configurações de compartilhamento avançadas**.
3. Role até **Todas as redes** e **Desative o compartilhamento protegido por senha**.
4. Clique em **Salvar alterações**.

Agora, o Ubuntu deve acessar as pastas sem pedir senha.

---

### **Dica Extra: Montar automaticamente no Ubuntu**
Se quiser montar a pasta automaticamente no Ubuntu sem precisar inserir a senha toda vez:  
1. No terminal do Ubuntu, instale o pacote necessário:
   ```bash
   sudo apt install cifs-utils
   ```
2. Monte a pasta manualmente para testar:
   ```bash
   sudo mount -t cifs //IP_DO_WINDOWS/NOME_DA_PASTA /mnt/pasta -o username=SEU_USUARIO,password=SUA_SENHA,uid=$(id -u),gid=$(id -g)
   ```
3. Para montar automaticamente no boot, edite o `/etc/fstab`:
   ```bash
   sudo nano /etc/fstab
   ```
   Adicione esta linha no final:
   ```
   //IP_DO_WINDOWS/NOME_DA_PASTA /mnt/pasta cifs username=SEU_USUARIO,password=SUA_SENHA,uid=1000,gid=1000 0 0
   ```
   Salve (`Ctrl + X`, `Y`, `Enter`).

Agora a pasta será montada automaticamente no boot. 🔥

Me avise se precisar de mais detalhes! 🚀
