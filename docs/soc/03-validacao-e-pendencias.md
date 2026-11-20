# Validação e pendências — Homelab SOC

**Atualizado:** 19/11/2026
**Estado geral:** instalador Wazuh concluído; validação operacional pendente.

## Concluído nesta etapa

- [x] Ubuntu Server 24.04.5 LTS instalado como VM-base.
- [x] VM-base desligada antes da clonagem e preservada.
- [x] Clone independente `wazuh-soc` criado.
- [x] 4 vCPU e 8 GiB RAM configurados.
- [x] `virt-sysprep` aplicado somente ao clone.
- [x] Chaves SSH regeneradas e conexão SSH validada.
- [x] Hostname `wazuh-soc` configurado.
- [x] Gateway e DNS verificados.
- [x] Acesso IPv4 HTTPS aos caminhos oficiais do instalador e da chave GPG verificado.
- [x] Script oficial da série 4.14 baixado.
- [x] Instalador All-in-One terminou e apresentou endpoint do Dashboard.

## Bloqueadores imediatos

- [ ] Rotacionar a senha inicial da conta `admin`; a senha original foi compartilhada durante a sessão e deve ser considerada exposta.
- [ ] Confirmar `wazuh-manager` ativo.
- [ ] Confirmar `wazuh-indexer` ativo.
- [ ] Confirmar `wazuh-dashboard` ativo.
- [ ] Registrar as versões efetivamente instaladas.
- [ ] Validar login no Dashboard com a senha rotacionada.
- [ ] Registrar uso de memória, disco e logs sem credenciais.

## Etapas seguintes (não implementadas)

- [ ] Adicionar Wazuh Agent ao Debian Server e Rocky Linux.
- [ ] Adicionar Wazuh Agent ao Windows Server 2025 e Windows 10.
- [ ] Instalar/configurar Sysmon nos endpoints Windows.
- [ ] Integrar o canal Sysmon Operational ao Wazuh.
- [ ] Criar redes virtuais isoladas `soc-red` e `soc-target`.
- [ ] Criar pfSense virtual com interfaces separadas.
- [ ] Instalar Suricata inicialmente em modo IDS.
- [ ] Encaminhar logs de firewall/IDS ao Wazuh.
- [ ] Criar Kali Linux e OWASP Juice Shop em redes isoladas.
- [ ] Executar exercícios autorizados e documentar evidências/detecções.

## Critério de aceitação da instalação inicial

A instalação inicial só será considerada validada quando Manager, Indexer e Dashboard estiverem ativos, as versões forem registradas, o usuário conseguir entrar no Dashboard com a senha rotacionada e o uso básico de recursos estiver dentro da capacidade do host.

## Higiene do repositório

Antes de qualquer commit:

- verificar `git diff --check`;
- procurar por senhas, tokens, chaves privadas e dados pessoais;
- sanitizar IPs/MACs/UUIDs e capturas de tela quando apropriado;
- confirmar que o texto distingue claramente itens executados, pendentes e planejados;
- não incluir saída do instalador que contenha senha.
