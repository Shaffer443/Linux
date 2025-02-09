Para criar um script em shell que monitore a disponibilidade do servidor TrueNAS e se conecte automaticamente quando ele estiver disponível, você pode seguir os passos abaixo. Este script verificará periodicamente se o servidor TrueNAS está acessível e, caso esteja, ele se conectará ao servidor.

### Passo 1: Criar o Script

Crie um arquivo chamado `monitor_truenas.sh` e adicione o seguinte conteúdo:

```bash
#!/bin/bash

# Endereço do servidor TrueNAS
TRUENAS_SERVER="TrueNAS.local"

# Intervalo de verificação em segundos
CHECK_INTERVAL=60

# Loop infinito para monitorar o servidor
while true; do
    # Verifica se o servidor está respondendo ao ping
    if ping -c 1 $TRUENAS_SERVER &> /dev/null; then
        echo "TrueNAS está online. Conectando..."
        
        # Comando para conectar ao TrueNAS (substitua pelo comando correto)
        # Exemplo: mount -t nfs TrueNAS.local:/mnt/pool /mnt/truenas
        mount -t nfs $TRUENAS_SERVER:/mnt/pool /mnt/truenas
        
        # Verifica se a montagem foi bem-sucedida
        if mount | grep -q "/mnt/truenas"; then
            echo "Montagem bem-sucedida!"
            break  # Sai do loop após a montagem bem-sucedida
        else
            echo "Falha na montagem. Tentando novamente em $CHECK_INTERVAL segundos..."
        fi
    else
        echo "TrueNAS está offline. Tentando novamente em $CHECK_INTERVAL segundos..."
    fi
    
    # Aguarda o intervalo definido antes de tentar novamente
    sleep $CHECK_INTERVAL
done
```

### Passo 2: Tornar o Script Executável

Depois de criar o script, torne-o executável com o seguinte comando:

```bash
chmod +x monitor_truenas.sh
```

### Passo 3: Executar o Script

Você pode executar o script manualmente ou configurá-lo para ser executado automaticamente na inicialização do sistema.

#### Executar Manualmente

```bash
./monitor_truenas.sh
```

#### Executar Automaticamente na Inicialização

Para executar o script automaticamente na inicialização, você pode adicionar uma entrada ao `crontab` com o comando `@reboot`.

1. Abra o `crontab` para edição:

   ```bash
   crontab -e
   ```

2. Adicione a seguinte linha ao final do arquivo:

   ```bash
   @reboot /caminho/para/monitor_truenas.sh
   ```

   Substitua `/caminho/para/monitor_truenas.sh` pelo caminho completo onde o script está localizado.

### Passo 4: Personalizar o Script

- **Montagem do Sistema de Arquivos**: O exemplo acima usa `mount -t nfs` para montar um sistema de arquivos NFS. **Se você estiver usando um protocolo diferente (como SMB/CIFS), substitua o comando de montagem pelo apropriado.**
  
- **Intervalo de Verificação**: O intervalo de verificação está definido para 60 segundos. Você pode ajustar esse valor conforme necessário.

### Passo 5: Testar

Depois de configurar o script, reinicie o sistema ou execute o script manualmente para garantir que ele funcione conforme o esperado.

Esse script deve garantir que, sempre que o TrueNAS estiver disponível na rede, ele será conectado automaticamente.

---
Se você estiver usando o protocolo **SMB/CIFS** para se conectar ao TrueNAS, o script precisa ser ajustado para usar o comando `mount.cifs` (ou `mount -t cifs`) em vez de `mount -t nfs`. Abaixo está a versão adaptada do script para SMB/CIFS:

---

### Script para SMB/CIFS

```bash
#!/bin/bash

# Endereço do servidor TrueNAS
TRUENAS_SERVER="TrueNAS.local"

# Compartilhamento SMB/CIFS no TrueNAS
SHARE_NAME="compartilhamento"  # Substitua pelo nome do compartilhamento SMB/CIFS

# Ponto de montagem local
MOUNT_POINT="/mnt/truenas"

# Credenciais de acesso (substitua pelos seus dados)
USERNAME="seu_usuario"
PASSWORD="sua_senha"

# Intervalo de verificação em segundos
CHECK_INTERVAL=60

# Loop infinito para monitorar o servidor
while true; do
    # Verifica se o servidor está respondendo ao ping
    if ping -c 1 $TRUENAS_SERVER &> /dev/null; then
        echo "TrueNAS está online. Conectando..."

        # Monta o compartilhamento SMB/CIFS
        mount -t cifs //$TRUENAS_SERVER/$SHARE_NAME $MOUNT_POINT -o username=$USERNAME,password=$PASSWORD

        # Verifica se a montagem foi bem-sucedida
        if mount | grep -q "$MOUNT_POINT"; then
            echo "Montagem bem-sucedida!"
            break  # Sai do loop após a montagem bem-sucedida
        else
            echo "Falha na montagem. Tentando novamente em $CHECK_INTERVAL segundos..."
        fi
    else
        echo "TrueNAS está offline. Tentando novamente em $CHECK_INTERVAL segundos..."
    fi

    # Aguarda o intervalo definido antes de tentar novamente
    sleep $CHECK_INTERVAL
done
```

---

### Passo a Passo para Usar o Script com SMB/CIFS

1. **Instale o Pacote `cifs-utils`**:
   O comando `mount.cifs` (ou `mount -t cifs`) requer o pacote `cifs-utils`. Se ainda não estiver instalado, instale-o com o seguinte comando:

   - No Ubuntu/Debian:
     ```bash
     sudo apt update && sudo apt install cifs-utils
     ```
   - No CentOS/RHEL:
     ```bash
     sudo yum install cifs-utils
     ```

2. **Crie o Ponto de Montagem**:
   Certifique-se de que o diretório de montagem (`/mnt/truenas` no exemplo) existe. Caso contrário, crie-o:
   ```bash
   sudo mkdir -p /mnt/truenas
   ```

3. **Substitua as Credenciais**:
   No script, substitua `seu_usuario` e `sua_senha` pelo usuário e senha configurados no TrueNAS para acessar o compartilhamento SMB/CIFS.

4. **Torne o Script Executável**:
   ```bash
   chmod +x monitor_truenas.sh
   ```

5. **Execute o Script**:
   Execute o script manualmente para testar:
   ```bash
   ./monitor_truenas.sh
   ```

6. **Configurar para Executar na Inicialização**:
   Adicione o script ao `crontab` para que ele seja executado automaticamente na inicialização:
   ```bash
   crontab -e
   ```
   Adicione a seguinte linha:
   ```bash
   @reboot /caminho/para/monitor_truenas.sh
   ```

---

### Explicação dos Parâmetros do `mount.cifs`

- `//$TRUENAS_SERVER/$SHARE_NAME`: Especifica o caminho do compartilhamento SMB/CIFS no TrueNAS.
- `$MOUNT_POINT`: Diretório local onde o compartilhamento será montado.
- `username=$USERNAME`: Nome de usuário para autenticação no TrueNAS.
- `password=$PASSWORD`: Senha para autenticação no TrueNAS.

---

### Segurança das Credenciais

Se você não quiser deixar a senha em texto claro no script, pode usar um arquivo de credenciais protegido:

1. Crie um arquivo de credenciais (por exemplo, `/etc/truenas.creds`):
   ```bash
   sudo nano /etc/truenas.creds
   ```
   Adicione as seguintes linhas:
   ```bash
   username=seu_usuario
   password=sua_senha
   ```

2. Proteja o arquivo de credenciais:
   ```bash
   sudo chmod 600 /etc/truenas.creds
   ```

3. Modifique o comando de montagem no script para usar o arquivo de credenciais:
   ```bash
   mount -t cifs //$TRUENAS_SERVER/$SHARE_NAME $MOUNT_POINT -o credentials=/etc/truenas.creds
   ```

---

### Testando

Após configurar, reinicie o sistema ou execute o script manualmente para garantir que ele funcione corretamente. O script deve montar o compartilhamento SMB/CIFS automaticamente quando o TrueNAS estiver disponível.
