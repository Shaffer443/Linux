Como você tem múltiplos serviços Flask rodando, cada um em uma porta diferente, aqui está como descobrir **em qual porta cada script está rodando** e **como abrir a porta no firewall**:

---

### ✅ 1. Descobrir a porta usada pelo script Flask

Normalmente, no seu script Flask (`app_velocidade.py` ou outro), a execução está assim:

```python
app.run(host='0.0.0.0', port=5000)
```

ou apenas:

```python
app.run()
```

(nesse caso, usa **5000 por padrão**).

Para conferir a porta:

```bash
grep -R "app.run" /home/gti/monitoramento_ip_seap/Monitoramento_de_Dispositivos_na_Rede/
```

Se você tem outros serviços em pastas diferentes, rode:

```bash
grep -R "app.run" /home/gti/
```

---

### ✅ 2. Descobrir em tempo real qual porta está aberta pelos processos Flask

Execute:

```bash
sudo lsof -i -P -n | grep LISTEN | grep python
```

Isso mostra algo como:

```
python3  1234 gti    3u  IPv4  123456   0t0  TCP *:5000 (LISTEN)
python3  1235 gti    3u  IPv4  123457   0t0  TCP *:5001 (LISTEN)
python3  1236 gti    3u  IPv4  123458   0t0  TCP *:5002 (LISTEN)
```

Ou:

```bash
ss -ltnp | grep python
```

Assim você descobre a porta do terceiro serviço.

---

### ✅ 3. Abrir a porta no firewall (UFW)

Se você usa **UFW** (padrão no Ubuntu):

```bash
sudo ufw allow 5000/tcp
sudo ufw allow 5001/tcp
sudo ufw allow 5002/tcp
```

Depois:

```bash
sudo ufw status
```

Se não usa UFW e está usando **iptables**, me fala que mando os comandos.

---

### ✅ 4. Onde definir a porta no script Flask

No seu `app.run`, use:

```python
if __name__ == "__main__":
    app.run(host="0.0.0.0", port=5002)
```

Cada serviço precisa de **porta única** (5000, 5001, 5002, etc).

---

❓ Quer que eu te ajude a **listar todas as portas abertas pelos seus três .service** agora com um comando único e te mostre qual é qual?
Ou quer que eu **edite o .service para especificar a porta no ExecStart**, em vez de depender do script?
