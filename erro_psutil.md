Ah, agora o motivo do **service ficar tentando iniciar e falhando repetidamente** ficou claro pelo log:

```
ModuleNotFoundError: No module named 'psutil'
```

Ou seja, o Python não consegue encontrar o módulo `psutil` quando o serviço é iniciado pelo **systemd**.

---

### Por que funciona manualmente e não pelo service

* Quando você roda manualmente no terminal:

  ```bash
  python3 /home/gti/monitoramento_ip_seap/Monitoramento_de_Dispositivos_na_Rede/app.py
  ```

  provavelmente você está usando **um ambiente virtual (venv)** ou seu PATH já inclui o Python correto e os pacotes instalados.

* Quando o systemd roda o serviço, ele **não carrega o ambiente do usuário**, nem um venv, então só encontra o Python do sistema e os pacotes instalados globalmente.

---

### ✅ Como corrigir

#### **Opção 1: Instalar psutil globalmente**

```bash
sudo apt update
sudo apt install python3-pip
sudo pip3 install psutil
```

Depois reinicie o serviço:

```bash
sudo systemctl daemon-reload
sudo systemctl restart monitoramento_ip.service
```

Isso funciona, mas **não é ideal se você quiser usar venv**.

---

#### **Opção 2: Usar um ambiente virtual (recomendado)**

1. Crie ou use o venv que você já tinha:

```bash
cd /home/gti/monitoramento_ip_seap/Monitoramento_de_Dispositivos_na_Rede
python3 -m venv venv
source venv/bin/activate
pip install psutil flask
deactivate
```

2. Edite o `.service` para usar o Python do venv:

```ini
[Service]
User=gti
WorkingDirectory=/home/gti/monitoramento_ip_seap/Monitoramento_de_Dispositivos_na_Rede
ExecStart=/home/gti/monitoramento_ip_seap/Monitoramento_de_Dispositivos_na_Rede/venv/bin/python app.py
Restart=always
StandardOutput=journal
StandardError=journal
```

3. Recarregue e reinicie:

```bash
sudo systemctl daemon-reload
sudo systemctl restart monitoramento_ip.service
sudo systemctl status monitoramento_ip.service
```

---

💡 **Resumo:**

* O erro `ModuleNotFoundError` indica que o Python não encontra `psutil`.
* A solução mais segura é usar **um venv** e apontar o `ExecStart` do systemd para o Python do venv.
* Instalar globalmente funciona, mas mistura pacotes do sistema e do projeto.

---

Se você quiser, posso **reescrever o `.service` de todos os três serviços Flask** para usar venv, host 0.0.0.0, portas corretas e reinício automático, pronto para rodar sem erro.
Quer que eu faça isso?
