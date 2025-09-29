verificar até quanto mu model de internet aceita de trafego via terminal linux
Pensando
Iniciando 5 pesquisas
Analisando 59 sites
Deixando tudo pronto
Não é possível verificar diretamente no terminal Linux o limite máximo de tráfego que um modem suporta, pois esse limite é uma característica do hardware e do contrato com seu provedor de internet, não algo que o sistema operacional possa determinar
. 
O Linux pode monitorar e medir o tráfego que está sendo usado no momento, o que pode ajudá-lo a identificar se a sua conexão está operando abaixo da velocidade contratada ou se está atingindo um gargalo.
Limitações de hardware e do provedor
O limite de tráfego que o seu modem aceita é determinado por:

    Contrato com o provedor: O principal fator é a velocidade de download e upload que você contratou com sua operadora de internet.
    Capacidade do hardware: O modem/roteador possui especificações técnicas que definem sua capacidade máxima de processamento de dados. Por exemplo, uma porta Ethernet de 100 Mbps não permitirá que você receba uma internet de 300 Mbps, mesmo que seu plano seja mais rápido. 

Como testar a velocidade da sua internet
Para verificar se sua conexão está operando na velocidade contratada, você pode usar ferramentas de linha de comando no Linux.
Com speedtest-cli
Essa ferramenta executa um teste de velocidade usando os servidores do Speedtest.net, medindo o ping, a velocidade de download e a de upload. 

    Instale a ferramenta:
    sh

    sudo apt install speedtest-cli

    Use o código com cuidado.

(Para distribuições baseadas em Debian/Ubuntu).
Execute o teste:
sh

speedtest-cli

Use o código com cuidado.
 

Com fast
Desenvolvido pela Netflix, este utilitário é uma alternativa para medir a velocidade de download.

    Baixe a ferramenta:
    sh

    wget https://github.com/ddo/fast/releases/download/v0.0.4/fast_linux_amd64
    chmod +x fast_linux_amd64

    Use o código com cuidado.

Execute o teste:
sh

./fast_linux_amd64

Use o código com cuidado.

Como monitorar o tráfego em tempo real
Para visualizar o uso da sua rede no momento, use ferramentas que mostram as conexões ativas e a largura de banda utilizada.
Com iftop
O iftop é um utilitário que exibe o uso de banda em tempo real por interface de rede. 

    Instale a ferramenta:
    sh

    sudo apt install iftop

    Use o código com cuidado.

Execute o monitoramento:
sh

sudo iftop -i [interface]

Use o código com cuidado.

    Substitua [interface] pelo nome da sua interface de rede (por exemplo, eth0 ou wlp2s0). Para ver o nome das suas interfaces, use o comando ip a.

Com nethogs
O nethogs agrupa o tráfego por processo, permitindo que você veja quais programas estão usando a sua rede. 

    Instale a ferramenta:
    sh

    sudo apt install nethogs

    Use o código com cuidado.

Execute o monitoramento:
sh

sudo nethogs

Use o código com cuidado.
