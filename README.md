# Homelab NOC — Infraestrutura e Monitoramento

Laboratório prático de infraestrutura de TI, administração de sistemas, monitoramento centralizado e troubleshooting, desenvolvido para simular atividades operacionais de um NOC (Network Operations Center).

## Objetivos

- Administrar sistemas Linux e Windows.
- Implementar monitoramento centralizado com Zabbix.
- Acompanhar disponibilidade, desempenho e serviços.
- Investigar falhas de conectividade, serviços e agentes.
- Documentar procedimentos de diagnóstico e resolução.
- Praticar administração de redes, servidores e firewalls.
- Versionar documentação, configurações sanitizadas e scripts com Git.

## Arquitetura

O ambiente utiliza Debian 13 como host de virtualização, KVM/QEMU e libvirt. O servidor Zabbix centraliza o monitoramento dos sistemas Linux e Windows do laboratório, além de um equipamento físico Debian.

Consulte o [mapa da infraestrutura](docs/infraestrutura/mapa-da-infraestrutura.md) e o [inventário técnico](docs/infraestrutura/inventario.md).

## Tecnologias

| Área | Tecnologias |
|---|---|
| Virtualização | KVM, QEMU, libvirt, virt-manager |
| Sistemas Linux | Debian 13, Rocky Linux 10 |
| Sistemas Windows | Windows Server 2025, Windows 10 IoT Enterprise LTSC 2021 |
| Monitoramento | Zabbix Server 7.0, Zabbix Agent 2 |
| Banco de dados | MariaDB |
| Servidor web | Apache |
| Redes | TCP/IP, DNS, ICMP, TCP, NAT |
| Firewall | firewalld, Windows Defender Firewall |
| Versionamento | Git, GitHub |
| Documentação | Markdown, Mermaid, Obsidian |

## Estrutura do repositório

\`\`\`text
homelab-noc/
├── docs/
│   ├── infraestrutura/
│   ├── monitoramento/
│   ├── procedimentos/
│   └── troubleshooting/
├── configs/
├── scripts/
├── evidencias/
└── README.md
\`\`\`

## Documentação

- [Mapa da infraestrutura](docs/infraestrutura/mapa-da-infraestrutura.md)
- [Inventário técnico](docs/infraestrutura/inventario.md)

## Escopo atual

- [x] Host Debian 13 com KVM/QEMU.
- [x] Servidor Zabbix 7.0.
- [x] Monitoramento do equipamento físico Debian.
- [x] Monitoramento de servidor Debian.
- [x] Monitoramento de servidor Rocky Linux.
- [x] Monitoramento de Windows Server 2025.
- [x] Monitoramento de Windows 10 IoT Enterprise LTSC 2021.
- [ ] Dashboard operacional NOC.
- [ ] Triggers e definição de severidades.
- [ ] Simulações de incidentes.
- [ ] Runbooks de atendimento e troubleshooting.

## Segurança e privacidade

Este repositório não deve conter senhas, tokens, chaves privadas, arquivos de configuração com credenciais ou dados pessoais.

Endereços IP privados apresentados na documentação pertencem ao ambiente de laboratório e podem mudar conforme a configuração local.

Configurações publicadas devem ser sanitizadas antes do versionamento.

## Finalidade

Projeto de estudo e portfólio técnico voltado a infraestrutura, monitoramento, suporte técnico e operações NOC.
