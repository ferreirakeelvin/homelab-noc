# Troubleshooting — QEMU Guest Agent no Windows 10

**Data:** 23/11/2026
**VM:** win10
**Sistema convidado:** Windows 10 IoT Enterprise LTSC 2021
**Hipervisor:** Debian 13 / KVM / QEMU / libvirt

## 1. Sintoma

O Zabbix apresentava o alerta:

`Windows: "QEMU-GA" (QEMU Guest Agent) is not running (startup type automatic)`

No Windows, o serviço QEMU-GA estava configurado para inicialização automática, mas permanecia parado.

O log do Service Control Manager registrava timeouts durante as tentativas de inicialização do serviço.

## 2. Diagnóstico

Foram verificadas as seguintes condições:

- Executável do QEMU Guest Agent presente no Windows.
- Driver VirtIO Serial reconhecido pelo Windows.
- Serviço QEMU-GA instalado e configurado para inicialização automática.
- Canal SPICE `com.redhat.spice.0` presente no XML da VM.
- Canal `org.qemu.guest_agent.0` ausente na configuração da VM.

A ausência do canal dedicado ao QEMU Guest Agent foi identificada como causa raiz provável dos timeouts.

O canal SPICE existente não substitui o canal de comunicação do QEMU Guest Agent.

## 3. Correção aplicada

### 3.1. Backup da configuração

Com a VM desligada, foi realizado backup do XML persistente:

```bash
sudo virsh dumpxml --inactive win10 > "$HOME/win10-xml-before-qga-2026-11-23-175046.xml"
```

O backup foi mantido fora do repositório.

### 3.2. Inclusão do canal VirtIO

A configuração persistente foi editada com:

```bash
sudo virsh edit win10
```

Dentro da seção `<devices>`, foi adicionado:

```xml
<channel type='unix'>
  <target type='virtio' name='org.qemu.guest_agent.0'/>
</channel>
```

O canal SPICE `com.redhat.spice.0` foi preservado.

### 3.3. Verificação da configuração

Comando executado:

```bash
sudo virsh dumpxml --inactive win10 | grep -A4 -B2 'org.qemu.guest_agent.0'
```

Resultado: canal `org.qemu.guest_agent.0` presente, associado à porta VirtIO Serial 2.

### 3.4. Inicialização da VM

A VM foi iniciada normalmente:

```bash
sudo virsh start win10
```

## 4. Validação

Os testes foram executados no host físico Debian 13.

### 4.1. Teste de comunicação

Comando:

```bash
sudo virsh qemu-agent-command win10 '{"execute":"guest-ping"}'
```

Retorno:

```json
{"return":{}}
```

Comunicação entre o host e o agente confirmada.

### 4.2. Identificação dos recursos disponíveis

Comando:

```bash
sudo virsh qemu-agent-command win10 '{"execute":"guest-info"}'
```

Resultado relevante:

- Versão informada pelo agente: `0.12.1`.
- `guest-ping`: habilitado.
- `guest-info`: habilitado.
- `guest-get-vcpus`: habilitado.
- `guest-get-time`: habilitado.
- `guest-get-host-name`: não consta na lista de comandos disponíveis.

### 4.3. Consulta dos processadores virtuais

Comando:

```bash
sudo virsh qemu-agent-command win10 '{"execute":"guest-get-vcpus"}'
```

Retorno:

```json
{
  "return": [
    {
      "online": true,
      "can-offline": false,
      "logical-id": 0
    },
    {
      "online": true,
      "can-offline": false,
      "logical-id": 1
    }
  ]
}
```

Os dois processadores virtuais foram identificados como online.

### 4.4. Validação no Zabbix

O evento relacionado ao serviço QEMU-GA passou para o estado RESOLVIDO.

A disponibilidade do Zabbix Agent 2 também foi restabelecida após a inicialização da VM.

## 5. Causa raiz e resolução

**Causa raiz provável:** ausência do canal VirtIO dedicado ao QEMU Guest Agent na configuração XML da VM.

**Resolução:** inclusão do canal `org.qemu.guest_agent.0`, sem remover o canal SPICE existente.

**Resultado:** comunicação entre host e sistema convidado validada por `guest-ping`, consulta dos recursos disponíveis e leitura dos processadores virtuais.

## 6. Observações técnicas

O QEMU Guest Agent e o Zabbix Agent 2 possuem finalidades diferentes:

- **QEMU Guest Agent:** permite a comunicação entre o hipervisor e o sistema operacional convidado.
- **Zabbix Agent 2:** coleta métricas e informações para o monitoramento pelo Zabbix Server.

O comando `guest-get-host-name` não está disponível na versão identificada. Isso não representa falha de comunicação, pois o `guest-info` confirmou os comandos efetivamente suportados.

Não foi necessária a reinstalação do agente.

O canal utilizado é do tipo Unix/VirtIO, gerenciado pelo libvirt, sem necessidade de expor uma porta TCP para o QEMU Guest Agent.

## 7. Evidências e rastreabilidade

Os resultados dos comandos e do frontend Zabbix foram observados durante a intervenção em 23/11/2026.

O backup original do XML permanece no diretório pessoal do administrador, fora do repositório.
