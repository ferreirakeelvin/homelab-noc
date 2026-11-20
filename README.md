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

### Implementado

- [x] Host Debian 13 com KVM/QEMU/libvirt.
- [x] Servidor Zabbix 7.0 com MariaDB e Apache.
- [x] Integração dos cinco hosts: Samsung N150P, Debian Server, Rocky Linux, Windows Server 2025 e Windows 10 IoT Enterprise LTSC 2021.
- [x] Instalação e configuração dos agentes Zabbix.
- [x] Associação dos templates Linux e Windows.
- [x] Comunicação e coleta de métricas validadas nos equipamentos ligados.
- [x] Consulta de métricas em Latest data.
- [x] Criação do dashboard Monitoramento NOC.
- [x] Identificação e inspeção das triggers existentes.
- [x] Documentação da infraestrutura, mapa e inventário.
- [x] Capturas de evidências do Zabbix organizadas em `evidencias/zabbix/`.

### Em validação

- [ ] Validar o comportamento das triggers, limiares e severidades.
- [ ] Testar detecção, geração de eventos e recuperação após normalização.
- [ ] Revisar e aprimorar a organização visual do dashboard, quando necessário.

### Pendências

- [ ] Executar simulações controladas de incidentes.
- [ ] Documentar procedimentos operacionais (runbooks).
- [ ] Registrar cenários de troubleshooting e respectivas soluções.

## Segurança e privacidade

Este repositório não deve conter senhas, tokens, chaves privadas, arquivos de configuração com credenciais ou dados pessoais.

Endereços IP privados apresentados na documentação pertencem ao ambiente de laboratório e podem mudar conforme a configuração local.

Configurações publicadas devem ser sanitizadas antes do versionamento.

## Finalidade

Projeto de estudo e portfólio técnico voltado a infraestrutura, monitoramento, suporte técnico e operações NOC.


<!-- SOC-HOMELAB-START -->

## Extensão SOC — em implementação

O laboratório NOC existente com Zabbix permanece preservado. Esta extensão acrescenta uma trilha de operações de segurança (SOC/Blue Team) e, posteriormente, exercícios ofensivos controlados em redes isoladas.

### Implementado nesta etapa (19/11/2026)

- VM dedicada `wazuh-soc` baseada em Ubuntu Server 24.04.5 LTS.
- Clone independente da VM-base, com 4 vCPU, 8 GiB de RAM e disco virtual de 50 GB.
- Hostname e SSH configurados; acesso remoto validado.
- Conectividade IPv4 e acesso HTTPS aos caminhos oficiais do repositório Wazuh validados.
- Instalador All-in-One da série 4.14 executado e finalizado com apresentação do endpoint do Dashboard.

**Estado de validação:** ainda falta confirmar estado dos serviços Wazuh Manager, Indexer e Dashboard, versões instaladas e login com a senha administrativa rotacionada. A instalação do assistente não é apresentada como validação operacional concluída.

### Próximas etapas (não implementadas)

- Integrar Wazuh Agents em Linux e Windows.
- Instalar Sysmon nos endpoints Windows e validar coleta de eventos.
- Adicionar pfSense e Suricata para firewall/IDS.
- Criar segmentos virtuais isolados `soc-red` e `soc-target`.
- Implantar Kali Linux e OWASP Juice Shop para exercícios autorizados.
- Documentar alertas, investigação, evidências e mitigação.

### Documentação SOC

- [Visão geral e status](docs/soc/README.md)
- [Registro da instalação Wazuh](docs/soc/01-wazuh-all-in-one.md)
- [Topologia proposta e roadmap](docs/soc/02-topologia-e-roadmap.md)
- [Validação e pendências](docs/soc/03-validacao-e-pendencias.md)

### Segurança e privacidade

Não incluir senhas, tokens, chaves privadas, MACs, UUIDs, IPs residenciais ou arquivos de configuração com credenciais. A senha inicial `admin` do Wazuh foi omitida e deve ser rotacionada antes da publicação de capturas de tela ou logs. Os endereços reais da implantação foram omitidos desta documentação pública.

<!-- SOC-HOMELAB-END -->
