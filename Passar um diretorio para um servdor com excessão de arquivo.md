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

Isso resolve seu problema?
