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
