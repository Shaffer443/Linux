Perfeito, Rafael! Vamos fazer seu projeto Django rodar e ficar **acessível na rede local**. Abaixo está um passo a passo objetivo:

---

### ✅ 1. **Ative o ambiente virtual**

Se ainda não estiver ativado:

```bash
cd ~/django/CRUD_Impressoras/meu_projeto
source venv/bin/activate
```

---

### ✅ 2. **Edite `settings.py` para liberar acesso de outros computadores**

Abra o arquivo `settings.py` dentro da pasta `meu_projeto` (a interna):

```bash
nano meu_projeto/settings.py
```

Encontre esta linha:

```python
ALLOWED_HOSTS = []
```

E altere para:

```python
ALLOWED_HOSTS = ['*']
```

> Para testes locais, o `'*'` permite acesso de qualquer IP. Mais seguro em produção seria colocar apenas IPs ou domínios autorizados.

Salve com `Ctrl + O`, depois `Enter` e saia com `Ctrl + X`.

---

### ✅ 3. **Descubra o IP do servidor**

No terminal, use:

```bash
ip a
```

Procure por um IP do tipo `192.168.x.x` ou `10.x.x.x`.
Exemplo de saída:

```
inet 192.168.0.121/24
```

Vamos supor que seu IP seja `192.168.0.121`.

---

### ✅ 4. **Execute o servidor Django na rede**

No terminal:

```bash
python manage.py runserver 0.0.0.0:8000
```

> O `0.0.0.0` faz o Django escutar em todas as interfaces de rede.

---

### ✅ 5. **Acesse a partir de outro computador**

No navegador de outro PC da rede, digite:

```
http://192.168.0.121:8000
```

(Substitua o IP pelo IP real do seu servidor Ubuntu)

---

### ❗ Se não abrir:

* Verifique se o **firewall** do Ubuntu está bloqueando a porta 8000:

  ```bash
  sudo ufw allow 8000
  ```

* Confirme se o outro computador está na **mesma rede**.

---

Se quiser depois posso te ajudar a rodar isso em segundo plano (com `screen`, `gunicorn`, `systemd` ou até com `nginx` para produção).

Quer ajuda agora para deixar esse servidor rodando sempre, mesmo após logout ou reinício?
