
## Removendo APACHE2



Para remover completamente o Apache2 do seu sistema Ubuntu, você pode seguir estes passos:

1. Parar o serviço do Apache2 antes de remover o Apache2, pare o serviço:

	``` 
	sudo systemctl stop apache2 
	
	```

2. Remover o Apache2 e seus pacotes relacionados use o seguinte comando para remover o Apache2 e todos os pacotes relacionados:

	``` 
	sudo apt-get purge apache2 apache2-utils apache2-bin apache2.2-common 
	```



3. Remover arquivos de configuração residuais após remover os pacotes, remova quaisquer arquivos de configuração residuais:

	``` 
	sudo apt-get autoremove 
	
	```
	
	``` 
	sudo apt-get autoclean 
	
	```

4. Verificar se há arquivos de configuração restantes, às vezes, alguns arquivos de configuração ainda podem estar presentes. Para garantir a remoção completa, você pode verificar manualmente e remover os diretórios relacionados ao Apache2:

	``` 
	sudo rm -rf /etc/apache2 

	``` 

	``` 
	sudo rm -rf /var/www/html 

	``` 

	``` 
	sudo rm -rf /var/log/apache2 

	``` 

5. Verificar se o Apache2 foi completamente removido finalmente, verifique se o Apache2 foi completamente removido:

    ``` 
    apache2 -v 
    
    ```

    Esse comando deve retornar um erro indicando que o comando não foi encontrado, confirmando que o Apache2 foi removido.

Seguindo esses passos, o Apache2 deve ser completamente removido do seu sistema Ubuntu.
