Para executar um servidor PHP embutido com o comando `php -S localhost:8579`, você precisa ter o PHP instalado no seu sistema.

No __Ubuntu Server__, __Ubunto__ ou algumas distribuições Linux, o PHP pode ser instalado usando o gerenciador de pacotes `apt`.

Aqui estão os passos para garantir que você tem tudo o que precisa:

1. **Atualize o índice de pacotes:**

   ```
   sudo apt update
   ```

2. **Instale o PHP (e o pacote do servidor embutido se necessário):**

   Se você já não tiver o PHP instalado, você pode instalar a versão padrão do PHP com:

   ```
   sudo apt install php
   ```

   Este comando instala o PHP junto com o servidor embutido, que é necessário para o comando `php -S`.

3. **Verifique a instalação do PHP:**

   Após a instalação, você pode verificar se o PHP está instalado corretamente e qual versão está disponível usando:

   ```
   php -v
   ```

   Isso deve mostrar a versão do PHP instalada e confirmar que o comando `php` está disponível.

4. **Execute o servidor embutido PHP:**

   Com o PHP instalado, você pode usar o comando:

   ```
   php -S localhost:8579
   ```

   Para iniciar o servidor embutido na porta 8579. Certifique-se de estar no diretório onde está o seu arquivo PHP ou especifique o caminho para o script.

Se você também precisa de módulos específicos do PHP para o seu projeto, como `php-mysql`, `php-xml`, ou outros, você pode instalá-los com:

   ```
   sudo apt install php-<module>
   ```

Substitua `<module>` pelo nome do módulo necessário.
