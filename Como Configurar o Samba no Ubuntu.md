Isso acontece porque o **Windows Explorer usa um protocolo diferente** do FileZilla e do MySQL.

  * **FileZilla** geralmente usa **SFTP** (que funciona sobre o SSH, porta 22) ou **FTP** (porta 21).
  * **MySQL** usa seu próprio protocolo na porta **3306**.
  * O **Windows Explorer** (para acessar "pastas de rede") usa o protocolo **SMB/CIFS**.

Para que o Windows Explorer consiga "enxergar" seu desktop Ubuntu, você precisa instalar e configurar um serviço chamado **Samba** no seu Ubuntu. O Samba "fala a língua" que o Windows Explorer entende.

-----

### 👣 Como Configurar o Samba no Ubuntu (Guia Rápido)

Aqui estão os passos básicos para fazer seu Ubuntu aparecer no Windows Explorer:

#### 1\. Instale o Samba

Abra o terminal no seu Ubuntu e execute:

```bash
sudo apt update
sudo apt install samba
```

#### 2\. Crie uma Senha do Samba

O Samba não usa a senha normal do seu usuário Linux. Você precisa criar uma senha específica para o Samba. Substitua `seu_usuario` pelo seu nome de usuário no Ubuntu:

```bash
sudo smbpasswd -a seu_usuario
```

Você será solicitado a digitar e confirmar uma nova senha. **É essa senha que você usará no Windows**.

#### 3\. Configure o Compartilhamento

Agora, você precisa editar o arquivo de configuração do Samba para dizer a ele qual pasta compartilhar.

  * Primeiro, faça um backup do arquivo original:

    ```bash
    sudo cp /etc/samba/smb.conf /etc/samba/smb.conf_backup
    ```

  * Agora, abra o arquivo para edição:

    ```bash
    sudo nano /etc/samba/smb.conf
    ```

  * Role até o final do arquivo e adicione um bloco como este. Este exemplo compartilha sua pasta `home`:

    ```ini
    [MeuHome]
       comment = Minha Pasta Pessoal
       path = /home/seu_usuario
       read only = no
       browsable = yes
    ```

      * `[MeuHome]`: Este será o nome do compartilhamento que você verá no Windows.
      * `path`: O caminho da pasta que você quer compartilhar (lembre-se de trocar `seu_usuario`).
      * `read only = no`: Permite que você escreva arquivos (mude para `yes` se quiser apenas leitura).

  * Salve o arquivo e saia do editor (no `nano`, pressione `Ctrl+X`, depois `Y` e `Enter`).

#### 4\. Reinicie o Serviço do Samba

Para que as mudanças tenham efeito, reinicie o serviço:

```bash
sudo systemctl restart smbd
```

#### 5\. Libere o Firewall (Se Estiver Ativo)

Se você usa o firewall UFW no Ubuntu, precisa permitir o tráfego do Samba:

```bash
sudo ufw allow samba
```

-----

### 🖥️ Como Acessar Pelo Windows

Agora, no seu PC com Windows:

1.  Abra o **Explorador de Arquivos**.
2.  Na barra de endereço (onde aparece "Este Computador"), digite o IP do seu Ubuntu seguido de duas barras invertidas:
    `\\ip-do-seu-ubuntu`
3.  Pressione `Enter`.
4.  Uma janela deve aparecer pedindo um nome de usuário e senha.
      * **Usuário:** `seu_usuario` (o mesmo do Ubuntu)
      * **Senha:** A senha que você criou com o comando `smbpasswd`.
5.  Você deverá ver a pasta `[MeuHome]` que você configurou\!

Este vídeo (em inglês) mostra o processo passo a passo de como configurar um compartilhamento de arquivos com Samba no Ubuntu.
