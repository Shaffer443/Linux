Se você quer **finalizar um sistema** rodando no Ubuntu, pode estar se referindo a:  

1. **Finalizar um processo específico**  
2. **Desligar ou reiniciar o sistema inteiro**  

### 1️⃣ **Finalizar um processo específico**  
Se você quer encerrar um programa que está rodando, siga um desses métodos:  

🔹 **Pelo nome do processo:**  
```bash
pkill -9 nome_do_programa
```
Exemplo:  
```bash
pkill -9 firefox
```

🔹 **Pelo PID (ID do Processo):**  
Primeiro, encontre o PID do processo:  
```bash
ps aux | grep nome_do_programa
```
Depois, mate o processo:  
```bash
kill -9 PID
```
Exemplo:  
```bash
kill -9 1234
```

🔹 **Usando `htop` (se estiver instalado)**  
Se tem o `htop`, basta rodar:  
```bash
htop
```
Depois, encontre o processo, selecione e pressione **F9**, depois escolha **SIGKILL**.

---

### 2️⃣ **Finalizar o sistema (Desligar/Reiniciar)**  
🔻 **Para desligar:**  
```bash
shutdown -h now
```
ou  
```bash
poweroff
```

🔄 **Para reiniciar:**  
```bash
reboot
```
ou  
```bash
shutdown -r now
```

Se precisar de algo mais específico, manda aí! 🚀
