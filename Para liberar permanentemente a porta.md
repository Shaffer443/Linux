### ✅ Para liberar permanentemente a porta 9596:

Execute:

```bash
sudo ufw allow 9596
```

Isso cria uma **regra persistente**, armazenada no arquivo de configuração do `ufw`, e estará ativa mesmo após reboot.

---

### 🔍 Confirmar se está tudo certo:

```bash
sudo ufw status numbered
```

Você deve ver algo como:

```
[ N] 9596                   ALLOW       Anywhere
[ N] 9596 (v6)              ALLOW       Anywhere (v6)
```

Essas regras **não somem** nem com `reboot`, nem com `ufw reload`.

---

Se por algum motivo você quiser **ver os arquivos de regras salvas**, eles estão aqui:

* `/etc/ufw/user.rules` (IPv4)
* `/etc/ufw/user6.rules` (IPv6)

Mas normalmente não precisa mexer diretamente neles.

---


### ✅ Passo 1: Liberar a porta 9596

Execute os comandos abaixo para liberar o acesso tanto para IPv4 quanto IPv6:

```bash
sudo ufw allow 9596
```

Depois, verifique se ela foi adicionada:

```bash
sudo ufw status
```

Deve aparecer algo assim:

```
9596                       ALLOW       Anywhere
9596 (v6)                  ALLOW       Anywhere (v6)
```

---

### ✅ Passo 2: Testar manualmente o comando PHP

Antes de reiniciar o serviço, execute isso manualmente para garantir que o comando em si funciona:

```bash
php -S 10.4.0.6:9596 -t /home/gti/formulario_ggp
```

* Se funcionar, ótimo.
* Se **der erro**, me diga o erro exato que aparecer.

---

### ✅ Passo 3 (se necessário): Use IP 0.0.0.0 como alternativa

Se mesmo com a porta liberada e IP correto continuar com erro no systemd, você pode trocar no `.service`:

```ini
ExecStart=/usr/bin/php -S 0.0.0.0:9596 -t /home/gti/formulario_ggp
```

Isso faz o PHP escutar **em todas as interfaces**, inclusive no `10.4.0.6`.

---

Se quiser, me mande o conteúdo do seu `formulario_ggp.service` para revisar também.

