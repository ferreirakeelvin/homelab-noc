# Inventário Técnico — Homelab NOC

## 1. Host físico

| Item | Informação |
|---|---|
| Sistema | Debian 13 |
| Processador | AMD Ryzen 5 1600 AF |
| Memória | 16 GB |
| GPU | Radeon RX 580 8 GB |
| Virtualização | KVM/QEMU |
| Gerenciamento | libvirt / virt-manager |

## 2. Servidor Zabbix

| Item | Informação |
|---|---|
| Hostname | zabbix |
| Sistema | Debian 13 |
| IP | 192.168.122.9 |
| Zabbix Server | 7.0.32 |
| MariaDB | 11.8.6 |
| Apache | Instalado |
| Porta | TCP 10051 |
| Estado | Operacional |

## 3. Sistemas monitorados

| Host | Sistema operacional | Endereço IP | Agente | Estado |
|---|---|---|---|---|
| Samsung-N150P | Debian minimal | Rede física privada (IP omitido) | Zabbix Agent 2 | Integrado |
| debian-server | Debian 13.7 | 192.168.122.40 | Zabbix Agent 2 | Integrado |
| rocky-linux | Rocky Linux 10.2 | 192.168.122.138 | Zabbix Agent 2 | Integrado |
| windows-server | Windows Server 2025 | 192.168.122.90 | Zabbix Agent 2 | Integrado |
| windows10 | Windows 10 IoT Enterprise LTSC 2021 | 192.168.122.3 | Zabbix Agent 2 | Integrado |

## 4. Detalhes dos agentes

### Samsung N150P

- Equipamento físico com Debian minimal.
- Executa AdGuard Home.
- Monitoramento de serviços, DNS, latência DNS, CPU, memória, swap, disco e rede.
- Interface Wi-Fi utilizada para conectividade.
- Interface Ethernet permanece desativada quando não utilizada.
- Zabbix Agent 2 configurado para verificações passivas (`Server`); `ServerActive` não está configurado.

### Debian Server

- VM Debian 13.7.
- Zabbix Agent 2 instalado.
- Serviço ativo e habilitado.
- Porta TCP 10050 em escuta.
- Host integrado ao template `Linux by Zabbix agent`.

### Rocky Linux

- VM Rocky Linux 10.2.
- Zabbix Agent 2 instalado pelo repositório oficial Zabbix.
- Serviço ativo e habilitado.
- Firewall firewalld ativo.
- Regra permanente de entrada TCP 10050 autorizando somente o servidor Zabbix (192.168.122.9).
- Host integrado ao template `Linux by Zabbix agent`.

### Windows Server

- VM Windows Server 2025.
- Zabbix Agent 2 7.0.32 instalado.
- Serviço em execução.
- Porta TCP 10050 em escuta.
- Regra de firewall de entrada limitada ao servidor Zabbix.
- Host integrado ao template `Windows by Zabbix agent`.

### Windows 10

- VM Windows 10 IoT Enterprise LTSC 2021.
- Build observado: 19044.1288.
- Zabbix Agent 2 7.0.32 instalado.
- Serviço em execução.
- Porta TCP 10050 em escuta.
- Regra de firewall de entrada limitada ao servidor Zabbix.
- Host integrado ao template `Windows by Zabbix agent`.
- Coleta de informações de sistema, CPU, memória, discos, interfaces de rede e serviços confirmada.

## 5. Portas utilizadas

| Porta | Protocolo | Origem | Destino | Finalidade |
|---|---|---|---|---|
| 10050 | TCP | Zabbix Server | Agentes | Consultas passivas |
| 10051 | TCP | Agentes com verificações ativas | Zabbix Server | Monitoramento ativo (VMs Linux e Windows) |
| 53 | UDP/TCP | Clientes | AdGuard Home | Resolução DNS |
| 80 | TCP | Navegador | Zabbix Server | Interface web HTTP |

## 6. Evidências de integração

A integração dos cinco sistemas foi verificada por meio de:
- Estado dos serviços Zabbix Agent 2.
- Testes de conectividade TCP na porta 10050.
- Testes locais do agente com `agent.ping`.
- Estado de disponibilidade ZBX no frontend.
- Dados recentes em Monitoring > Latest data.

A disponibilidade dos hosts foi confirmada. Nem todos os itens de descoberta precisam apresentar valores imediatamente após o cadastro; a coleta depende dos intervalos de atualização.

## 7. Cuidados com o ambiente

- Não executar todas as VMs simultaneamente sem avaliar a memória.
- Manter regras de firewall restritas às origens necessárias.
- Não desativar o firewall para resolver falhas de conectividade.
- Confirmar o endereço DHCP antes de diagnosticar indisponibilidade.
- Não publicar credenciais, tokens ou chaves privadas.
