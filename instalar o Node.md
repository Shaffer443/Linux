Para instalar o Node.js no seu **Ubuntu Server**, você pode seguir os passos abaixo. Existem várias maneiras de instalar o Node.js, mas as mais comuns são:

### **1. Instalar via `apt` (repositório padrão do Ubuntu)**
Esta é a maneira mais simples, mas pode não ter a versão mais recente.

```bash
sudo apt update
sudo apt install nodejs npm
```

Verifique a instalação:
```bash
node -v
npm -v
```

Se precisar de uma versão mais recente, siga os próximos métodos.

---

### **2. Instalar via NodeSource (recomendado para versões mais recentes)**
O NodeSource fornece versões atualizadas do Node.js.

#### **Escolha a versão desejada (LTS ou Current) e execute:**
```bash
# Exemplo para Node.js 20.x (LTS)
curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash -
sudo apt-get install -y nodejs
```

Verifique a instalação:
```bash
node -v
npm -v
```

---

### **3. Instalar via `nvm` (Node Version Manager)**
O `nvm` permite gerenciar múltiplas versões do Node.js.

#### **Instale o `nvm`:**
```bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash
```

#### **Recarregue o terminal ou reinicie a sessão:**
```bash
source ~/.bashrc  # Ou ~/.zshrc, dependendo do shell
```

#### **Instale uma versão do Node.js (ex: 20.x):**
```bash
nvm install 20
```

#### **Defina como padrão:**
```bash
nvm use 20
nvm alias default 20
```

Verifique a instalação:
```bash
node -v
npm -v
```

---

### **4. Verificando e corrigindo permissões (opcional)**
Se você usou `npm` globalmente e teve problemas de permissão, execute:
```bash
mkdir ~/.npm-global
npm config set prefix '~/.npm-global'
echo 'export PATH=~/.npm-global/bin:$PATH' >> ~/.bashrc
source ~/.bashrc
```

---

### **Conclusão**
- Se quer uma instalação rápida e simples, use o **método 1 (`apt`)**.
- Se precisa da versão mais recente, use o **método 2 (NodeSource)**.
- Se quer controle total sobre as versões, use o **método 3 (`nvm`)**.

Pronto! Agora você tem o Node.js instalado no seu **Ubuntu Server**. 🚀
