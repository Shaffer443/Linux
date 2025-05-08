Ótima pergunta!

✅ **Você deve criar o arquivo `/etc/systemd/system/django_crud.service` fora da (venv)** — ou seja, **não precisa estar com o ambiente virtual ativado**.

---

O `systemd` roda como root, então o serviço vai chamar o Python da sua `venv` de forma automática, porque o caminho completo do executável da venv é informado no arquivo (`ExecStart`).

---

### ✅ Pode fazer assim:

Mesmo que você esteja dentro da venv, basta rodar:

```bash
deactivate  # opcional, apenas para sair da venv
sudo nano /etc/systemd/system/django_crud.service
```

E então cole o conteúdo com os **caminhos absolutos corretos** para o seu projeto e o executável Python da venv.

---

Se quiser, posso te ajudar a montar o conteúdo exato do serviço agora, baseado na estrutura que você já mostrou. Deseja isso?

---

Exatamente, Rafael! Como seu IP é `10.4.0.6`, você pode acessar o Django via:

```
http://10.4.0.6:8000
```

---

### ✅ Para que o servidor Django **suba automaticamente ao ligar o Ubuntu**, o ideal é usar o **`systemd`**. Ele cria um serviço que:

* Ativa seu ambiente virtual,
* Entra na pasta do projeto,
* Roda o `runserver`,
* E tudo isso em segundo plano, automaticamente no boot.

---

### 🔧 1. **Crie um arquivo de serviço**

Crie um novo serviço com:

```bash
sudo nano /etc/systemd/system/django_crud.service
```

E cole o conteúdo abaixo (ajustando os caminhos se necessário):

```ini
[Unit]
Description=Django CRUD Server
After=network.target

[Service]
User=gti
WorkingDirectory=/home/gti/django/CRUD_Impressoras/meu_projeto
ExecStart=/home/gti/django/CRUD_Impressoras/meu_projeto/venv/bin/python manage.py runserver 0.0.0.0:8000
Restart=always

[Install]
WantedBy=multi-user.target
```

---

### 🔍 Dicas:

* Altere `User=gti` se o nome do seu usuário for diferente.
* Confirme os caminhos com `pwd` e `which python` dentro do ambiente virtual.

---

### ✅ 2. **Recarregue os serviços e ative no boot**

```bash
sudo systemctl daemon-reexec
sudo systemctl daemon-reload
sudo systemctl enable django_crud.service
```

---

### ✅ 3. **Inicie agora manualmente (só uma vez)**

```bash
sudo systemctl start django_crud.service
```

Você pode verificar se está funcionando com:

```bash
sudo systemctl status django_crud.service
```

---

Se der erro ou não subir, me avise que revisamos os caminhos e permissões.

Quer que eu revise os caminhos com você?
