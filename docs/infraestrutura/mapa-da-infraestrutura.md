# Mapa da Infraestrutura — Homelab NOC

## 1. Visão geral

O laboratório é composto por um host físico Debian 13, um ambiente de virtualização KVM/QEMU e um servidor Zabbix responsável pelo monitoramento centralizado.

Cinco sistemas estão integrados ao monitoramento:
- Samsung N150P — Debian minimal.
- Debian Server — Debian 13.
- Rocky Linux — Rocky Linux 10.2.
- Windows Server — Windows Server 2025.
- Windows 10 — IoT Enterprise LTSC 2021.

## 2. Topologia lógica

\`\`\`mermaid
flowchart TB
    Internet((Internet))
    LAN["Rede física doméstica<br/>Rede privada"]
    HOST["Host físico<br/>Debian 13<br/>KVM / QEMU / libvirt"]
    BRIDGE["Rede virtual libvirt<br/>virbr0 — 192.168.122.1/24"]
    ZBX["Servidor Zabbix<br/>192.168.122.9<br/>Debian 13 · Zabbix 7.0"]
    DEB["debian-server<br/>192.168.122.40<br/>Debian 13"]
    ROCKY["rocky-linux<br/>192.168.122.138<br/>Rocky Linux 10.2"]
    WINSRV["windows-server<br/>192.168.122.90<br/>Windows Server 2025"]
    WIN10["windows10<br/>192.168.122.3<br/>Windows 10 IoT LTSC 2021"]
    N150["Samsung N150P<br/>Debian minimal<br/>AdGuard Home"]

    Internet --- LAN
    LAN --- HOST
    LAN --- N150
    HOST --- BRIDGE
    BRIDGE --- ZBX
    BRIDGE --- DEB
    BRIDGE --- ROCKY
    BRIDGE --- WINSRV
    BRIDGE --- WIN10

    ZBX -. "TCP 10050<br/>Consultas passivas" .-> DEB
    ZBX -. "TCP 10050<br/>Consultas passivas" .-> ROCKY
    ZBX -. "TCP 10050<br/>Consultas passivas" .-> WINSRV
    ZBX -. "TCP 10050<br/>Consultas passivas" .-> WIN10

    DEB -. "TCP 10051<br/>Monitoramento ativo" .-> ZBX
    ROCKY -. "TCP 10051<br/>Monitoramento ativo" .-> ZBX
    WINSRV -. "TCP 10051<br/>Monitoramento ativo" .-> ZBX
    WIN10 -. "TCP 10051<br/>Monitoramento ativo" .-> ZBX
    ZBX -. "TCP 10050<br/>Consulta passiva" .-> N150
\`\`\`

> Diagrama lógico. O caminho de comunicação entre a rede física e a rede virtual depende do roteamento e das regras de firewall do host.

## 3. Host de virtualização

| Componente | Informação |
|---|---|
| Sistema operacional | Debian 13 |
| Hipervisor | KVM/QEMU |
| Gerenciamento | libvirt / virt-manager |
| Processador | AMD Ryzen 5 1600 AF |
| Memória RAM | 16 GB |
| GPU | Radeon RX 580 8 GB |
| Rede virtual | libvirt default / virbr0 |
| Gateway da rede virtual | 192.168.122.1 |

## 4. Servidor de monitoramento

| Componente | Informação |
|---|---|
| Hostname | zabbix |
| Sistema operacional | Debian 13 |
| Endereço | 192.168.122.9 |
| Zabbix Server | 7.0.32 |
| Banco de dados | MariaDB 11.8.6 |
| Servidor web | Apache |
| Porta do servidor | TCP 10051 |
| Interface web | Frontend web do Zabbix (acesso pela rede interna) |

## 5. Fluxos de monitoramento

| Origem | Destino | Porta | Finalidade |
|---|---|---:|---|
| Zabbix Server | Agentes | TCP 10050 | Consultas passivas |
| Agentes | Zabbix Server | TCP 10051 | Monitoramento ativo |

As regras de firewall dos sistemas Windows e Rocky Linux restringem o acesso à porta TCP 10050 ao endereço do servidor Zabbix.

## 6. Observações operacionais

- A rede 192.168.122.0/24 é a rede virtual NAT padrão do libvirt.
- Os endereços DHCP das VMs podem mudar.
- O host possui 16 GB de RAM; executar todas as VMs simultaneamente pode causar pressão de memória.
- O Samsung N150P é um equipamento físico separado do ambiente virtual.
- O servidor Zabbix centraliza os dados coletados pelos agentes.
