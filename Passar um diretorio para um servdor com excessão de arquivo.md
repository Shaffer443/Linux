Sim, dá pra fazer isso de forma simples usando `rsync`. O comando abaixo copia tudo da pasta `WebDadosTraderBD`, **exceto** a pasta `conexao`, para o servidor remoto:  

```bash
rsync -av --exclude='conexao' ~/WebDadosTraderBD/ usuario@servidor:/caminho/destino/
```

### Explicação:
- `rsync` → Utilitário para sincronização de arquivos.  
- `-a` → Modo **arquivar** (mantém permissões, links simbólicos, etc.).  
- `-v` → Modo **verboso** (mostra o que está sendo copiado).  
- `--exclude='conexao'` → Exclui a pasta `conexao`.  
- `~/WebDadosTraderBD/` → Diretório **origem** (o `/` no final evita copiar a pasta raiz, passando apenas seu conteúdo).  
- `usuario@servidor:/caminho/destino/` → Diretório **destino** no servidor.  

### Se precisar copiar via SSH com uma porta diferente:
Se o SSH estiver rodando em outra porta (exemplo: `2222`), use:  
```bash
rsync -av -e "ssh -p 2222" --exclude='conexao' ~/WebDadosTraderBD/ usuario@servidor:/caminho/destino/
```

---

O erro ocorre porque o comando `cp` exige a opção `-r` (ou `-R`) para copiar diretórios recursivamente. O comando correto seria:

```bash
sudo cp -r WebDadosTraderBD/ /home/shaffer443/
```

### Explicação:
- `cp` → Comando para copiar arquivos.
- `-r` (ou `--recursive`) → Necessário para copiar diretórios e seu conteúdo.
- `WebDadosTraderBD/` → Diretório de origem.
- `/home/shaffer443/` → Diretório de destino.

Caso queira preservar permissões e atributos, use `-a` (archive):

```bash
sudo cp -a WebDadosTraderBD/ /home/shaffer443/
```

Se continuar dando erro, pode ser problema de permissões. Você pode verificar com:

```bash
ls -ld WebDadosTraderBD
```

E, se necessário, ajustar as permissões antes da cópia:

```bash
sudo chmod -R u+rwX WebDadosTraderBD/
```

Se precisar de mais alguma coisa, manda aí! 🚀
