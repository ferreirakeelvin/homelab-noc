# Extensão SOC — Homelab NOC/SOC

**Status em 19/11/2026: em implementação.**

Esta documentação registra a evolução do laboratório NOC existente para incluir uma trilha de operações de segurança. O projeto NOC/Zabbix permanece preservado; esta extensão acrescenta um servidor Wazuh dedicado e descreve as etapas posteriores de integração de endpoints, telemetria de segurança e detecção de rede.

## O que já foi feito

- Criada uma VM dedicada `wazuh-soc` a partir de uma VM-base Ubuntu Server 24.04.5 LTS.
- Clonagem completa com disco independente.
- Alocação de 4 vCPU, 8 GiB de RAM e disco virtual de 50 GB.
- Generalização do clone com `virt-sysprep` (machine-id, hostname, chaves SSH e regras persistentes de interface).
- Hostname definido como `wazuh-soc`; SSH regenerado e acesso remoto validado.
- Conectividade IPv4 com o gateway e acesso HTTPS aos caminhos oficiais do pacote Wazuh validados.
- Instalador All-in-One da série 4.14 baixado; instalação executada e finalizada com a apresentação da URL do Dashboard.

A versão efetiva dos componentes e o estado individual dos serviços ainda precisam ser confirmados. A saída do instalador, por si só, não comprova a saúde operacional de Manager, Indexer e Dashboard.

## O que ainda não foi feito

- Validação dos serviços centrais e login no Dashboard com senha administrativa rotacionada.
- Instalação/registro de Wazuh Agents nos endpoints Linux e Windows.
- Sysmon nos endpoints Windows.
- pfSense e Suricata.
- Redes isoladas RED e TARGET.
- Kali Linux e alvos vulneráveis (OWASP Juice Shop / Metasploitable).
- Exercícios controlados, evidências, triagem e relatórios de investigação.

## Documentos

- [Registro da instalação Wazuh All-in-One](01-wazuh-all-in-one.md)
- [Topologia proposta e roadmap Red/Blue Team](02-topologia-e-roadmap.md)
- [Validação e pendências](03-validacao-e-pendencias.md)

## Princípios de publicação

Não publicar senhas, tokens, chaves privadas, MACs, UUIDs, endereços residenciais, arquivos de configuração com segredos ou capturas que exponham credenciais. IPs e detalhes de endereçamento do ambiente real foram omitidos ou devem ser sanitizados antes de publicar evidências.
