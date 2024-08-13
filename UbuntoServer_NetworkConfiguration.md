# configurando o __Network Configuration__ no Ubunto Server 22.04

Algumas vezes fico na dúvida sobre o que colocar no campo __Subnet__, vou descrever um exemplo:

- Subnet: 10.4.0.0/23 (Para subrede 255.255.254.0)
- Address: 10.4.0.11 (Ip fixo que pretendo usar neste exemplo)
- Gateway: 10.4.0.1
- Name Server: 8.8.8.8 
- Search Domains: seres10.pe.gov.br (facultativo, ou outro, ou nenhum)

Verifique a Configuração:

Para verificar a configuração atual da rede, você pode usar o comando ip a ou ifconfig.

Certifique-se de que o IP, a máscara e o gateway estão corretos e que a interface de rede está configurada corretamente.

Corrija Problemas Comuns:

Se o erro "has host bits set" persistir, verifique se você está especificando a máscara de sub-rede corretamente e se não há conflitos com outros IPs na mesma rede.

Confirme que o endereço IP e a máscara de sub-rede pertencem à mesma rede. No seu caso, 10.4.0.11/23 deve estar na sub-rede 10.4.0.0/23 com uma máscara de 255.255.254.0.

Verifique a Máscara de Sub-rede:

A máscara de sub-rede /23 é equivalente a 255.255.254.0.
Certifique-se de que a máscara de sub-rede está corretamente especificada.

Configuração no Netplan:

O Ubuntu 22.04 usa o Netplan para configuração de rede. A configuração é feita através de arquivos YAML localizados em /etc/netplan/. Siga estes passos para configurar seu IP e sub-rede:

Edite o arquivo de configuração do Netplan. Pode haver um arquivo como 01-netcfg.yaml ou similar em /etc/netplan/.

```
sudo nano /etc/netplan/01-netcfg.yaml
````