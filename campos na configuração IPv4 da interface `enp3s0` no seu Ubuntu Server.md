Com base nas informações fornecidas, aqui está como você deve preencher os campos na configuração IPv4 da interface `enp3s0` no seu Ubuntu Server:

### Configuração IPv4 Manual (Static):
1. **Address (Endereço IP):**  
   `192.168.1.200`  
   *(Este é o IP fixo do seu servidor.)*

2. **Netmask (Máscara de Rede):**  
   `255.255.255.0`  
   *(Equivalente a `/24` em notação CIDR.)*  
   *(No Ubuntu, você pode usar `255.255.255.0` ou `/24` dependendo do formato solicitado.)*

3. **Gateway (Roteador Padrão):**  
   `192.168.1.254`  
   *(O gateway geralmente é o IP do seu roteador.)*

4. **Name Servers (DNS):**  
   Você pode usar:  
   - DNS públicos do Google: `8.8.8.8, 8.8.4.4`  
   - Ou o DNS do seu provedor/roteador (ex.: `192.168.1.254` se o roteador fornecer DNS).  
   *(Separe múltiplos servidores com vírgulas.)*

5. **Search Domains (Domínios de Busca):**  
   - Opcional. Se você tem um domínio local (ex.: `local.domain`), pode adicioná-lo aqui.  
   - Caso contrário, deixe em branco ou use um valor padrão como `local`.

---

### Resumo dos campos:
| Campo            | Valor               |
|------------------|---------------------|
| **Address**      | `192.168.1.200`     |
| **Netmask**      | `255.255.255.0` (ou `/24`) |
| **Gateway**      | `192.168.1.254`     |
| **Name Servers** | `8.8.8.8, 8.8.4.4` (ou outro DNS) |
| **Search Domains** | (Opcional, ex.: `local`) |

---

### Observações:
- **Subnet (Rede):**  
  Se for pedido separadamente, use `192.168.1.0/24` (equivalente à máscara `255.255.255.0`).
- **Hostname (Nome do Servidor):**  
  Defina como `UbuntoServer` (verifique o uso de maiúsculas/minúsculas).
- **Após configurar:**  
  Reinicie a rede ou execute:  
  ```bash
  sudo netplan apply
  ```
  (Se estiver usando `netplan` no Ubuntu Server).

Se precisar de ajuda adicional, me avise!
