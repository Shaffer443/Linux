O Ubuntu Server não tem um servidor gráfico (interface gráfica) instalado por padrão e, ao usar o Tkinter, ele precisa de um ambiente gráfico para renderizar a janela. Como você está conectando via SSH, o script não consegue acessar a variável de ambiente `DISPLAY`, que é usada para direcionar o conteúdo gráfico para a tela correta.

### Solução para rodar o Tkinter remotamente (usando um desktop na rede)

Há algumas maneiras de resolver isso. Vou detalhar os métodos principais:

---

### 1. **Habilitar o X11 Forwarding via SSH**
O **X11 Forwarding** permite que você redirecione o ambiente gráfico do servidor para o desktop cliente onde você está rodando o SSH.

#### Passos:
1. **Instale o servidor X11 no Ubuntu Server**:
   Certifique-se de que o pacote `xauth` está instalado no servidor:
   ```bash
   sudo apt install xauth -y
   ```
   Também instale o suporte ao X11:
   ```bash
   sudo apt install x11-apps -y
   ```

2. **No cliente (desktop), configure o SSH para X11 Forwarding**:
   Ao conectar ao servidor via SSH, use a opção `-X` para habilitar o X11 Forwarding:
   ```bash
   ssh -X usuario@ip_do_servidor
   ```
   Ou, em alguns casos, você pode usar `-Y` para um redirecionamento mais permissivo:
   ```bash
   ssh -Y usuario@ip_do_servidor
   ```

3. **Teste o X11 Forwarding**:
   No servidor, execute:
   ```bash
   xclock
   ```
   Isso abrirá um relógio gráfico na sua máquina cliente. Se funcionar, o X11 Forwarding está configurado corretamente.

4. **Execute o seu script Python com Tkinter**:
   Agora, quando você executar o script Python com Tkinter, a interface gráfica será exibida no seu desktop cliente.

---

### 2. **Usar o VNC para acesso remoto com interface gráfica**
Outra opção é instalar uma interface gráfica leve no servidor Ubuntu e usar um cliente VNC para acessá-la.

#### Passos:
1. **Instale uma interface gráfica leve no servidor**:
   Recomendo instalar o **XFCE** por ser leve:
   ```bash
   sudo apt install xfce4 xfce4-goodies -y
   ```

2. **Instale o servidor VNC**:
   Instale o **tightvncserver** ou outro servidor VNC de sua preferência:
   ```bash
   sudo apt install tightvncserver -y
   ```

3. **Configure o VNC**:
   Inicie o servidor VNC para configurar uma senha:
   ```bash
   vncserver
   ```

4. **Conecte ao VNC do desktop cliente**:
   Use um cliente VNC (como o **Remmina** ou **TigerVNC**) e conecte ao endereço IP do servidor na porta `5901` (ou outra configurada). Após acessar o servidor gráfico, você pode executar seu script Python diretamente na interface gráfica do servidor.

---

### 3. **Exportar a interface gráfica para o cliente com `DISPLAY`**
Se você tem um servidor gráfico rodando no cliente, pode usar a variável `DISPLAY` para exportar a saída gráfica para o desktop.

#### Passos:
1. **No cliente, habilite o servidor X**:
   No desktop (Linux ou outro com suporte a X11), habilite o servidor X. Normalmente, ele já está em execução. Se você estiver em Windows, pode instalar o **Xming** ou o **VcXsrv**.

2. **No cliente, permita conexões remotas no servidor X**:
   Execute no terminal do cliente:
   ```bash
   xhost +
   ```

3. **No servidor, configure a variável `DISPLAY`**:
   Após conectar ao servidor via SSH, configure a variável `DISPLAY` apontando para o IP do cliente:
   ```bash
   export DISPLAY=ip_do_cliente:0.0
   ```
   Substitua `ip_do_cliente` pelo endereço IP do desktop.

4. **Execute o script Python no servidor**:
   Agora, quando você rodar o script, a interface gráfica será exibida no cliente.

---

### Dica Extra: Verifique dependências do Tkinter
Se ainda não fez isso, garanta que o `Tkinter` está instalado:
```bash
sudo apt install python3-tk -y
```

Se tiver dúvidas ou dificuldades com algum passo, posso te ajudar a ajustar!
