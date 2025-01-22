Configurar um endereço IP fixo no Ubuntu 18.04 envolve editar os arquivos de configuração de rede. A partir do Ubuntu 18.04, a configuração da rede é gerenciada pelo Netplan, um sistema de configuração baseado em YAML.

Aqui está o passo a passo:
1. Identifique a interface de rede

Execute o seguinte comando para listar as interfaces de rede disponíveis:
```bash
ip a
```
Identifique o nome da interface de rede que você deseja configurar (por exemplo, eth0 ou ens33).
2. Edite o arquivo de configuração do Netplan

Os arquivos de configuração do Netplan estão localizados em /etc/netplan/. O nome do arquivo geralmente é algo como 01-netcfg.yaml ou 50-cloud-init.yaml.

Abra o arquivo com um editor de texto, como o Nano:
```bash
sudo nano /etc/netplan/01-netcfg.yaml
```
3. Configure o IP fixo

Substitua o conteúdo existente (ou adicione um novo bloco) pelo seguinte, adaptando as informações para a sua rede:
```bash
network:
  version: 2
  renderer: networkd
  ethernets:
    ens33:  # Substitua "ens33" pelo nome da sua interface de rede
      dhcp4: no
      addresses:
        - 192.168.1.100/24  # IP fixo e máscara de sub-rede
      gateway4: 192.168.1.1  # Gateway padrão
      nameservers:
        addresses:
          - 8.8.8.8          # Servidor DNS primário
          - 8.8.4.4          # Servidor DNS secundário
```
4. Aplique as configurações

Após editar o arquivo, salve-o (Ctrl + O e depois Enter no Nano) e feche (Ctrl + X). Em seguida, aplique as configurações do Netplan com o comando:
```bash
sudo netplan apply
```
5. Verifique a configuração

Confira se o IP fixo foi aplicado corretamente:
```bash
ip a
```
E teste a conectividade:
```bash
ping 8.8.8.8
```
6. Solucionando problemas

    Se ocorrerem erros ao aplicar as configurações, execute:
```bash
sudo netplan try
```
Isso permite testar as configurações antes de aplicá-las permanentemente.

Verifique os logs para mensagens de erro:
```bash
    journalctl -xe
```
Após seguir esses passos, sua interface de rede estará configurada com um IP fixo no Ubuntu 18.0
