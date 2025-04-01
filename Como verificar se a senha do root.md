No Ubuntu (e na maioria das distribuições Linux), a senha do usuário **root** não é definida por padrão. Em vez disso, o acesso administrativo é concedido a usuários com privilégios **sudo**.  

### Como verificar se a senha do root está definida:
1. **Verifique se o usuário root tem senha definida**:
   ```bash
   sudo passwd -S root
   ```
   - Se a saída mostrar `root LK ...`, significa que a senha do root **não está definida** (LK = "locked").  
   - Se mostrar `root PS ...`, então o root **tem uma senha definida** (PS = "password set").  

2. **Se quiser definir ou alterar a senha do root** (não recomendado para uso diário):
   ```bash
   sudo passwd root
   ```
   - Isso pedirá uma nova senha para o root.  

### Alternativa Recomendada:
- **Use `sudo`** em vez de habilitar o login direto como root.  
- Para executar comandos como administrador, prefira:
  ```bash
  sudo comando
  ```
- Se precisar de um shell root temporário, use:
  ```bash
  sudo -i
  ```
  ou
  ```bash
  sudo su
  ```

### Importante:
- Habilitar o login do root pode ser um risco de segurança.  
- O Ubuntu desencoraja o uso direto do root, priorizando `sudo` para tarefas administrativas.  

Se você esqueceu sua senha de usuário (não a do root), pode redefini-la no modo de recuperação.
