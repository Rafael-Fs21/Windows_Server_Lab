# Proxmox VE Home Lab — Active Directory, DHCP e File Server

Laboratório de estudo em virtualização e infraestrutura Windows Server, construído do zero em ambiente **Proxmox VE rodando dentro do VirtualBox**. Documentação incremental, com comandos reais, prints reais e registro de todos os erros encontrados durante o processo — sem conteúdo genérico ou inventado.

---

## 🖥️ Ambiente

| Item | Valor |
|---|---|
| Host | Windows 11 Pro |
| Hypervisor | VirtualBox 7.2.6 |
| Proxmox VE | 9.2-1 |
| Rede | Roteador Mikrotik (`192.168.88.0/24`) |

O Proxmox roda como uma VM dentro do VirtualBox (virtualização aninhada / nested virtualization), o que trouxe desafios específicos — documentados em detalhe, especialmente relacionados a drivers VirtIO e configuração de rede em bridge.

---

## 📚 Documentação

### Infraestrutura base

- **[Instalação do Proxmox VE — Máquina 2](./DOC_Instalacao_Proxmox_VirtualBox_Maquina2.md)**
  Instalação do hypervisor, configuração de repositórios APT (enterprise → no-subscription) e primeiro boot.

### Servidor Windows (Win-AD) — Multi-Role

Todos os roles a seguir estão hospedados na **mesma VM** (`Win-AD`), simulando um servidor corporativo multi-função:

- **[Active Directory Domain Services + DNS](./DOC_VM_Win-AD_Active-Directory.md)**
  Criação da VM, instalação do Windows Server, promoção a Controlador de Domínio (`ad.local`), estrutura organizacional (OUs, grupos de segurança, usuários) e delegação de permissões administrativas (RBAC).

- **[DHCP Server](./DOC_VM_DHCP-Server.md)**
  Instalação do role, migração de rede motivada pela introdução de um roteador Mikrotik, divisão de faixas de IP, criação do escopo e resolução de uma instabilidade de IP que impedia a distribuição automática de endereços.

- **[File Server](./DOC_VM_FileServer.md)**
  Disco de dados dedicado, estrutura de pastas por departamento (RH, Financeiro, TI) e permissões de compartilhamento/NTFS alinhadas aos grupos de segurança do AD.

- **[Backup Server](./DOC_VM_Backup-Server.md)**
  Rotinas de backup com Iperius Backup, destino redundante (disco local + USB passthrough), agendamento fora do horário de pico e nomenclatura ofuscada como camada extra de proteção contra ransomware.

### Estação de trabalho

- **[VM Win-Client-01](./DOC_VM_Win-Client-01.md)**
  Criação de uma VM cliente (Windows 10 Enterprise LTSC), ingresso no domínio e validação de login com usuário do Active Directory — fechando o ciclo de ponta a ponta do AD.

---

## 🧭 Ordem de leitura recomendada

1. [Instalação do Proxmox](./DOC_Instalacao_Proxmox_VirtualBox_Maquina2.md)
2. [Active Directory](./DOC_VM_Win-AD_Active-Directory.md)
3. [DHCP Server](./DOC_VM_DHCP-Server.md)
4. [File Server](./DOC_VM_FileServer.md)
5. [Backup Server](./DOC_VM_Backup-Server.md)
6. [VM Cliente e validação](./DOC_VM_Win-Client-01.md)

---

## 🛠️ Stack técnica

- Proxmox VE 9.2-1
- Windows Server 2022 Standard (Evaluation)
- Windows 10 Enterprise LTSC 2021
- Roteador Mikrotik (RouterOS)
- Drivers VirtIO (virtio-win)

---

## 📌 Destaques técnicos

Alguns dos problemas reais enfrentados e documentados neste lab:

- Dependência circular entre driver VirtIO SCSI e a unidade que o carrega (resolvida movendo o CD para barramento IDE)
- Virtualização aninhada exigindo desativação de KVM por VM
- Modo Promíscuo do VirtualBox bloqueando tráfego entre camadas de bridge aninhadas
- Windows 10 (edição consumer) travando no OOBE por exigir conta Microsoft — resolvido com Windows 10 Enterprise LTSC
- Serviço DHCP do Windows Server não distribuindo IPs por instabilidade de binding, causada por IP dinâmico do próprio servidor
- Backup com destino externo via USB passthrough (Proxmox → VM), contornando falhas de autenticação SMB entre host físico e VM

Cada um desses está documentado com contexto, causa raiz e solução no arquivo correspondente.

---

## 📄 Licença / Uso

Documentação de estudo pessoal, disponibilizada para fins de aprendizado e portfólio.
