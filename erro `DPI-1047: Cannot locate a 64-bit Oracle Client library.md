O erro `DPI-1047: Cannot locate a 64-bit Oracle Client library: "libclntsh.so: cannot open shared object file: No such file or directory"` indica que a biblioteca cliente do Oracle necessária para a conexão não está disponível ou não pode ser localizada no seu sistema.

Aqui estão os passos para resolver esse problema:

### 1. **Instalar a Biblioteca Cliente Oracle**

Você precisa instalar a biblioteca cliente Oracle (Oracle Instant Client) em seu sistema. Abaixo estão as instruções para diferentes sistemas operacionais.

#### **Para Linux:**

1. **Baixe o Oracle Instant Client:**

   Acesse a [página de downloads do Oracle Instant Client](https://www.oracle.com/database/technologies/instant-client/downloads.html) e baixe a versão apropriada para seu sistema (64-bit).

2. **Instale o Oracle Instant Client:**

   Descompacte o arquivo baixado e coloque o conteúdo em um diretório acessível. Exemplo:

   ```sh
   unzip instantclient-basic-linux.x64-19c.zip
   unzip instantclient-sqlplus-linux.x64-19c.zip
   ```

3. **Configure o `LD_LIBRARY_PATH`:**

   Adicione o diretório onde o Instant Client foi descompactado à variável de ambiente `LD_LIBRARY_PATH`. Isso permite que o sistema encontre as bibliotecas necessárias.

   ```sh
   export LD_LIBRARY_PATH=/caminho/para/instantclient:$LD_LIBRARY_PATH
   ```

   Para tornar essa configuração permanente, adicione a linha acima ao seu arquivo `~/.bashrc` ou `~/.bash_profile`, dependendo do seu shell.

4. **Verifique a Instalação:**

   Certifique-se de que as bibliotecas necessárias estão localizadas e acessíveis:

   ```sh
   ls /caminho/para/instantclient
   ```

   Verifique se `libclntsh.so` e outros arquivos necessários estão presentes.

#### **Para Windows:**

1. **Baixe o Oracle Instant Client:**

   Acesse a [página de downloads do Oracle Instant Client](https://www.oracle.com/database/technologies/instant-client/downloads.html) e baixe a versão apropriada para Windows (64-bit).

2. **Instale o Oracle Instant Client:**

   Extraia o conteúdo do arquivo zip para um diretório de sua escolha.

3. **Configure o `PATH`:**

   Adicione o diretório onde o Instant Client foi descompactado ao `PATH`. Você pode fazer isso através do Painel de Controle → Sistema → Configurações Avançadas do Sistema → Variáveis de Ambiente.

   ```sh
   set PATH=C:\caminho\para\instantclient;%PATH%
   ```

   Para tornar essa configuração permanente, adicione o diretório ao `PATH` nas variáveis de ambiente do sistema.

4. **Verifique a Instalação:**

   Certifique-se de que o diretório do Instant Client foi adicionado ao `PATH` e que as bibliotecas necessárias estão presentes.

### 2. **Verificar Permissões e Caminho**

Certifique-se de que o diretório onde você instalou o Oracle Instant Client possui as permissões corretas e que não há problemas de acesso. 

### 3. **Verificar a Versão do cx_Oracle**

Certifique-se de que você está usando uma versão compatível do pacote `cx_Oracle`. A versão do `cx_Oracle` deve corresponder à versão do Oracle Client instalado. Atualize o pacote, se necessário:

```sh
pip install --upgrade cx_Oracle
```

### 4. **Reiniciar o Ambiente**

Após fazer essas mudanças, reinicie seu terminal ou ambiente de desenvolvimento para garantir que as variáveis de ambiente atualizadas sejam carregadas.

Após seguir esses passos, tente novamente executar seu script. Isso deve resolver o erro relacionado à biblioteca cliente do Oracle e permitir que você se conecte ao banco de dados. Se você ainda enfrentar problemas, verifique a [documentação do cx_Oracle](https://cx-oracle.readthedocs.io/en/latest/user_guide/installation.html) para mais detalhes sobre a instalação e configuração.
