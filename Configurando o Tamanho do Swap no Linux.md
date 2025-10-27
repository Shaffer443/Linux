# Configurando o Tamanho do Swap no Linux

## 📋 **Métodos para Configurar o Swap**

### 1. **Verificar Swap Atual**
```bash
# Ver swap atual
free -h
swapon --show

# Informações detalhadas
cat /proc/swaps
```

### 2. **Configurar via Arquivo de Swap**

#### **A) Criar arquivo de swap**
```bash
# Desativar swap atual (se necessário)
sudo swapoff -a

# Criar arquivo de swap (exemplo: 8GB)
sudo dd if=/dev/zero of=/swapfile bs=1G count=8 status=progress

# Alternativa mais rápida:
sudo fallocate -l 8G /swapfile

# Verificar se o arquivo foi criado
ls -lh /swapfile
```

#### **B) Configurar permissões**
```bash
# Dar permissões seguras
sudo chmod 600 /swapfile

# Formatar como área de swap
sudo mkswap /swapfile

# Ativar o swapfile
sudo swapon /swapfile

# Verificar se está ativo
swapon --show
free -h
```

#### **C) Configurar para inicialização automática**
```bash
# Backup do fstab
sudo cp /etc/fstab /etc/fstab.bak

# Adicionar ao fstab
echo '/swapfile none swap sw 0 0' | sudo tee -a /etc/fstab

# Verificar se foi adicionado corretamente
cat /etc/fstab | grep swap
```

### 3. **Configurar via Partição de Swap**

#### **Se você tem uma partição dedicada:**
```bash
# Ativar partição de swap (substitua /dev/sdX pelo seu dispositivo)
sudo mkswap /dev/sdX
sudo swapon /dev/sdX

# Adicionar ao fstab
echo '/dev/sdX none swap sw 0 0' | sudo tee -a /etc/fstab
```

## ⚙️ **Ajustar Parâmetros do Swap**

### **Configurar Swappiness**
```bash
# Ver valor atual (0-100)
cat /proc/sys/vm/swappiness

# Alterar temporariamente
sudo sysctl vm.swappiness=10

# Alterar permanentemente
echo 'vm.swappiness=10' | sudo tee -a /etc/sysctl.conf

# Aplicar mudanças
sudo sysctl -p
```

### **Configurar Cache Pressure**
```bash
# Ajustar pressão do cache
echo 'vm.vfs_cache_pressure=50' | sudo tee -a /etc/sysctl.conf
sudo sysctl -p
```

## 📊 **Tabela de Tamanhos Recomendados**

| RAM do Sistema | Tamanho Recomendado do Swap | Swappiness |
|----------------|-----------------------------|------------|
| **≤ 2GB** | 2x da RAM | 60 |
| **4GB** | 4GB | 30 |
| **8GB** | 4GB | 20 |
| **16GB** | 4GB | 10 |
| **32GB+** | 2-4GB | 5-10 |

## 🎯 **Cenários Específicos**

### **Para Desktop/Geral:**
```bash
# 4GB de swap para sistemas com 8GB+ de RAM
sudo swapoff /swapfile 2>/dev/null
sudo fallocate -l 4G /swapfile
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile
```

### **Para Hibernação:**
```bash
# Swap deve ser ≥ tamanho da RAM
sudo swapoff /swapfile
sudo fallocate -l 16G /swapfile  # Para 16GB RAM
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile
```

### **Para Servidores:**
```bash
# Swap mínimo para estabilidade
sudo fallocate -l 2G /swapfile
echo 'vm.swappiness=5' | sudo tee -a /etc/sysctl.conf
```

## 🔧 **Script Automatizado**

### **Criar script de configuração:**
```bash
#!/bin/bash
# swap-config.sh

set -e

SWAP_SIZE="${1:-4G}"
SWAPPINESS="${2:-10}"

echo "Configurando swap de $SWAP_SIZE com swappiness=$SWAPPINESS"

# Parar swap atual
sudo swapoff -a 2>/dev/null || true

# Criar novo arquivo de swap
sudo fallocate -l "$SWAP_SIZE" /swapfile
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile

# Configurar fstab
if ! grep -q "/swapfile" /etc/fstab; then
    echo '/swapfile none swap sw 0 0' | sudo tee -a /etc/fstab
fi

# Configurar swappiness
echo "vm.swappiness=$SWAPPINESS" | sudo tee -a /etc/sysctl.conf
sudo sysctl -p

echo "Configuração concluída!"
echo "Swap atual:"
swapon --show
echo "Swappiness: $(cat /proc/sys/vm/swappiness)"
```

### **Usar o script:**
```bash
# Dar permissão de execução
chmod +x swap-config.sh

# Executar com tamanhos diferentes
./swap-config.sh 2G 15    # 2GB com swappiness 15
./swap-config.sh 8G 10    # 8GB com swappiness 10
```

## 🛠️ **Remover/Reduzir Swap**

### **Reduzir tamanho do swap:**
```bash
# Desativar swap
sudo swapoff /swapfile

# Remover arquivo antigo
sudo rm /swapfile

# Criar novo arquivo menor
sudo fallocate -l 2G /swapfile
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile
```

### **Remover completamente:**
```bash
sudo swapoff /swapfile
sudo rm /swapfile
sudo sed -i '/swapfile/d' /etc/fstab
```

## 📝 **Verificação Final**

### **Comandos para verificar:**
```bash
# Verificar se está funcionando
free -h
swapon --show

# Verificar configurações
cat /proc/sys/vm/swappiness
cat /proc/sys/vm/vfs_cache_pressure

# Monitorar uso
watch -n 1 'free -h && echo "---" && swapon --show'
```

## 💡 **Dicas Importantes:**

1. **Reinicie após mudanças** ou use `sudo sysctl -p`
2. **Teste a estabilidade** do sistema após configurar
3. **Monitore o uso** com `htop` ou `top`
4. **SSDs**: Swap em SSD é mais rápido, mas causa mais desgaste
5. **RAID**: Considere swap em múltiplos dispositivos para performance

# Aumentando o Swap para 16GB

## 📋 **Passos para Aumentar o Swap**

### 1. **Verificar configuração atual**
```bash
# Ver detalhes do swap atual
swapon --show
cat /proc/swaps
ls -lh /swapfile
```

### 2. **Desativar o swap atual**
```bash
# Desativar swap
sudo swapoff /swapfile

# Verificar se foi desativado
free -h
```

### 3. **Aumentar o arquivo de swap para 16GB**
```bash
# Remover arquivo antigo
sudo rm /swapfile

# Criar novo arquivo de 16GB
sudo fallocate -l 16G /swapfile

# Verificar criação
ls -lh /swapfile
```

### 4. **Configurar o novo arquivo de swap**
```bash
# Configurar permissões
sudo chmod 600 /swapfile

# Formatar como área de swap
sudo mkswap /swapfile

# Ativar o novo swap
sudo swapon /swapfile
```

### 5. **Verificar se está funcionando**
```bash
# Verificar novo swap
free -h
swapon --show

# Deve mostrar aproximadamente 16GB
```

## ⚙️ **Script Automatizado para Aumentar**

### **Criar script:**
```bash
#!/bin/bash
# increase-swap.sh

echo "Aumentando swap para 16GB..."

# Desativar swap atual
echo "Desativando swap atual..."
sudo swapoff /swapfile

# Remover arquivo antigo
echo "Removendo arquivo swap antigo..."
sudo rm /swapfile

# Criar novo de 16GB
echo "Criando novo arquivo de swap de 16GB..."
sudo fallocate -l 16G /swapfile

# Configurar
echo "Configurando novo swap..."
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile

# Verificar
echo "Verificando nova configuração:"
free -h
swapon --show

echo "Concluído! Swap aumentado para 16GB."
```

### **Executar:**
```bash
# Dar permissão e executar
chmod +x increase-swap.sh
./increase-swap.sh
```

## 🔧 **Configurações Adicionais Recomendadas**

### **Ajustar swappiness (opcional)**
```bash
# Ver valor atual
cat /proc/sys/vm/swappiness

# Configurar para valor mais baixo (recomendado com 16GB swap)
echo 'vm.swappiness=10' | sudo tee -a /etc/sysctl.conf
sudo sysctl -p
```

### **Verificar se está no fstab**
```bash
# Verificar se o swap está configurado para inicializar
cat /etc/fstab | grep swap

# Se não estiver, adicionar:
echo '/swapfile none swap sw 0 0' | sudo tee -a /etc/fstab
```

## 📊 **Verificação Final**

### **Comandos para confirmar:**
```bash
# Verificar memória e swap
free -h

# Ver detalhes do swap
swapon --show

# Verificar uso em tempo real
watch -n 2 'free -h && echo "---" && swapon --show'
```

### **Resultado esperado:**
```
               total       usada       livre
Mem.:          7,7Gi       6.xGi        x.xGi
Swap:          16,0Gi       x.xGi       x.xGi
```

## ⚠️ **Possíveis Problemas e Soluções**

### **Se encontrar erro de "fallocate failed":**
```bash
# Usar dd como alternativa
sudo dd if=/dev/zero of=/swapfile bs=1G count=16 status=progress
```

### **Se o swap não ativar:**
```bash
# Verificar se o arquivo foi criado corretamente
ls -lh /swapfile

# Refazer o mkswap
sudo mkswap -f /swapfile
```

### **Se não persistir após reinício:**
```bash
# Verificar fstab
cat /etc/fstab

# Adicionar se necessário
echo '/swapfile none swap sw 0 0' | sudo tee -a /etc/fstab
```

## 💡 **Dicas para seu Caso Específico:**

Com **7.7GB de RAM** e **2GB de swap totalmente usado**, aumentar para **16GB** é uma boa estratégia. Recomendo também:

1. **Swappiness baixo**: `vm.swappiness=10` para priorizar RAM
2. **Monitorar processos**: Identificar o que está consumindo tanta RAM/swap
3. **Considerar upgrade de RAM**: Se você frequentemente usa todo o swap

### **Monitorar consumo:**
```bash
# Ver processos consumindo mais memória
ps aux --sort=-%mem | head -10

# Monitorar em tempo real
htop
```

Após executar esses passos, seu sistema terá swap suficiente para operar suavemente mesmo com uso intensivo de memória!
