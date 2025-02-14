No Ubuntu, você pode listar e desinstalar programas diretamente pelo terminal usando o gerenciador de pacotes. Aqui estão algumas maneiras de fazer isso:

### 🔍 **Listar programas instalados**  
1. **Usando o `dpkg` (para pacotes `.deb`)**  
   ```bash
   dpkg --list
   ```
   Ou para buscar um programa específico:  
   ```bash
   dpkg --list | grep nome-do-programa
   ```

2. **Usando o `apt` (para pacotes instalados via APT)**  
   ```bash
   apt list --installed
   ```
   Ou para um programa específico:  
   ```bash
   apt list --installed | grep nome-do-programa
   ```

3. **Usando o `snap` (para pacotes Snap)**  
   ```bash
   snap list
   ```

4. **Usando o `flatpak` (para pacotes Flatpak, caso tenha instalado)**  
   ```bash
   flatpak list
   ```

---

### 🗑️ **Desinstalar programas**  
#### 📦 **Se foi instalado via APT (`.deb`)**
```bash
sudo apt remove nome-do-programa
```
Se quiser remover completamente, incluindo arquivos de configuração:
```bash
sudo apt purge nome-do-programa
```
Depois, para limpar pacotes não utilizados:
```bash
sudo apt autoremove
```

#### 🚀 **Se foi instalado via Snap**
```bash
sudo snap remove nome-do-programa
```

#### 📦 **Se foi instalado via Flatpak**
```bash
flatpak uninstall nome-do-programa
```

#### 🔍 **Se não sabe como foi instalado**
Se não encontrar o programa nos métodos acima, pode tentar:
```bash
which nome-do-programa
whereis nome-do-programa
```
Isso mostra onde o executável está localizado, o que pode ajudar a identificar o gerenciador de pacotes usado.

----

Esse aviso:  

```
WARNING: apt does not have a stable CLI interface. Use with caution in scripts.
```  

significa que a interface de linha de comando (`CLI`) do `apt` pode mudar entre versões do Ubuntu, e, por isso, não é garantido que os comandos usados hoje funcionarão exatamente da mesma forma no futuro.  

### 🔹 O que fazer?  
Se você estiver apenas rodando comandos manualmente no terminal, **pode ignorar esse aviso** sem problemas. Ele não impede que os comandos funcionem.  

### 🔹 Se for usar em scripts?  
Se você estiver criando scripts para automação, o ideal é usar o `dpkg` ou `apt-cache`, que são mais estáveis:  

✅ Para listar pacotes instalados de forma estável:  
```bash
dpkg --list | grep discord
```
ou  
```bash
apt-cache policy discord
```

Mas se for só um uso casual, manda ver com `apt list --installed` sem preocupação. 😉
