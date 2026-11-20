# Registro de instalação — Wazuh All-in-One

- Data: 19/11/2026
- VM: `wazuh-soc`
- Sistema: Ubuntu Server 24.04.5 LTS
- Virtualização: KVM/QEMU + libvirt
- Recursos configurados: 4 vCPU, 8 GiB RAM, 50 GB de disco virtual
- Rede: rede NAT `default` do libvirt; endereço DHCP real omitido nesta documentação pública
- Status: instalador concluído; validação dos serviços pendente

## 1. VM-base e clonagem

Foi criada uma VM-base Ubuntu Server 24.04.5 LTS com 2 vCPU, 2 GiB de RAM e 50 GB de disco virtual. A base recebeu OpenSSH Server, QEMU Guest Agent e atualizações de pacotes. Ela foi desligada antes da clonagem e preservada.

A clonagem completa foi realizada com:

```bash
sudo virt-clone \
  --connect qemu:///system \
  --original ubuntu-server-base \
  --name wazuh-soc \
  --file /var/lib/libvirt/images/wazuh-soc.qcow2
```

A VM clonada foi configurada, desligada, com 8 GiB de RAM e 4 vCPU:

```bash
sudo virsh setmaxmem wazuh-soc 8388608 --config
sudo virsh setmem wazuh-soc 8388608 --config
sudo virsh setvcpus wazuh-soc 4 --config --maximum
sudo virsh setvcpus wazuh-soc 4 --config
sudo virsh dominfo wazuh-soc
```

O `dominfo` confirmou 4 CPUs e 8388608 KiB para memória máxima e configurada.

## 2. Generalização e primeiro boot

A limpeza foi aplicada somente ao clone, com as operações disponíveis na versão instalada de `virt-sysprep`:

```bash
sudo virt-sysprep \
  --connect qemu:///system \
  -d wazuh-soc \
  --operations machine-id,net-hostname,ssh-hostkeys,udev-persistent-net
```

Após o primeiro boot, o SSH recusou conexão porque as chaves do servidor tinham sido removidas. O problema foi corrigido dentro da VM:

```bash
sudo ssh-keygen -A
sudo systemctl restart ssh
sudo systemctl status ssh --no-pager -l
```

O acesso SSH a partir do host foi validado. O hostname foi definido como `wazuh-soc` e o arquivo `/etc/hosts` atualizado para refletir o novo nome.

## 3. Conectividade

- Interface `enp1s0` ficou UP.
- Gateway libvirt respondeu ao ICMP, com 0% de perda em três pacotes.
- DNS para `packages.wazuh.com` foi resolvido.
- A raiz do domínio respondeu HTTP 403, que não representa falha de transporte.
- HTTPS via IPv6 falhou no teste; via IPv4 funcionou.
- Os caminhos do instalador e da chave GPG responderam HTTP 200 via IPv4.

## 4. Download do instalador

```bash
curl -4 -I --connect-timeout 5 --max-time 15 \
  https://packages.wazuh.com/4.14/wazuh-install.sh

curl -4 -I --connect-timeout 5 --max-time 15 \
  https://packages.wazuh.com/key/GPG-KEY-WAZUH

curl -4 -fL --output wazuh-install.sh \
  https://packages.wazuh.com/4.14/wazuh-install.sh

file wazuh-install.sh
wc -c wazuh-install.sh
```

O arquivo baixado foi identificado como script Bash em texto ASCII e tinha 215431 bytes. O tamanho coincidiu com o `Content-Length` retornado pelo servidor; isso não substitui uma verificação criptográfica de integridade. Não há evidência registrada de execução de `bash -n`.

## 5. Resultado do instalador

O processo de instalação All-in-One terminou e apresentou a URL HTTPS do Dashboard e a conta administrativa `admin`. A senha não está incluída neste repositório.

**Ainda não confirmado:** versões efetivas dos componentes, estado dos serviços, login no Dashboard, saúde do Indexer e uso de recursos após a instalação.

## 6. Verificação pós-instalação pendente

Executar dentro da VM:

```bash
sudo systemctl status wazuh-manager --no-pager -l
sudo systemctl status wazuh-indexer --no-pager -l
sudo systemctl status wazuh-dashboard --no-pager -l

sudo systemctl is-active wazuh-manager wazuh-indexer wazuh-dashboard
sudo /var/ossec/bin/wazuh-control info

dpkg-query -W -f='${Package} ${Version}\n' \
  wazuh-manager wazuh-indexer wazuh-dashboard

df -h /
free -h
```

Registrar as saídas sem incluir credenciais. Se algum comando não existir ou pacote não estiver instalado, preservar a mensagem real e investigar; não inferir versão/saúde.

## 7. Credenciais e dados sensíveis

A senha administrativa inicial gerada pelo instalador foi compartilhada durante a sessão de configuração e, por isso, deve ser considerada exposta. Rotacionar antes de publicar screenshots ou continuar o uso do Dashboard. A nova senha deve ser guardada em gerenciador de senhas, nunca neste repositório.

UUID, MAC e endereços privados da implantação foram omitidos desta versão pública. A VM obtém IP por DHCP; endereço atual deve ser consultado antes de realizar conexões.
