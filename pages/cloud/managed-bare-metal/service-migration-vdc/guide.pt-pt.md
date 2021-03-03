---
title: Migrar uma infraestrutura para um novo vDC
excerpt: Saiba como gerir todos os aspetos da migração de uma infraestrutura para um vDC
slug: vdc-migration
section: Introdução
order: 6
hidden: true
---

> [!primary]
> Esta tradução foi automaticamente gerada pelo nosso parceiro SYSTRAN. Em certos casos, poderão ocorrer formulações imprecisas, como por exemplo nomes de botões ou detalhes técnicos. Recomendamos que consulte a versão inglesa ou francesa do manual, caso tenha alguma dúvida. Se nos quiser ajudar a melhorar esta tradução, clique em "Contribuir" nesta página.
>

**Última atualização: 16 de dezembro de 2020**

> [!warning]
>
> O caminho de migração vDC ainda não está disponível, pois estão em curso atualizações e operações de manutenção. Iremos notificá-lo assim que for possível.
>

## Objetivo

A migração de uma infraestrutura para um novo vDC comporta dois aspetos:

* O contexto Managed Bare Metal OVHcloud que compreende a parte cliente, a administração da sua infraestrutura.
* O contexto VMware, que inclui todo o eco-sistema VMware, na própria infraestrutura.

**Este guia explica como cobrir todos os aspetos da migração de uma infraestrutura OVHcloud pré-existente para um novo vDC.**

## Requisitos

* Uma infraestrutura PCC
* Ter acesso à Área de [Cliente OVHcloud](https://www.ovh.com/auth/?action=gotomanager&from=https://www.ovh.pt/&ovhSubsidiary=pt){.external}, na secção `Bare Metal Cloud`{.action} e depois `Managed Bare Metal`{.action}.

## Instruções

Este guia utiliza as noções de **infraestrutura de origem** e de **vDC de destino**.

### Contexto OVHcloud

#### Segurança

##### **Contexto de ligação ao Managed Bare Metal**

Para se ligar à plataforma VMware, pode escolher bloquear o acesso ao vSphere de forma padrão. Para isso, consulte o nosso guia sobre a [política de acesso ao vCenter](../alterar-a-politica-de-acesso-ao-vcenter/).

No seguimento da alteração da política de acesso, se esta passou para "restrita", é evidente que é necessário aplicar os mesmos IP de ligação no vDC de destino que na infraestrutura de origem.

##### **Utilizadores do Managed Bare Metal**

No ciclo de vida da infraestrutura original, pode ter sido criada uma lista de utilizadores para fins profissionais ou necessidades organizacionais. Assim, deve criá-los novamente no vDC de destino e atribuir-lhes os direitos adequados, em função da configuração do vDC de destino.

Para isso, consulte os nossos manuais para [alterar as permissões de um utilizador](../alterar-os-direitos-de-um-utilizador/), [alterar a palavra-passe de um utilizador](../alteracao-da-palavra-passe-de-utilizador/) e [associar um e-mail a um utilizador](../associar-e-mail-a-um-utilizador/).

##### **Key Management Server (KMS)**

Se as máquinas virtuais forem protegidas por encriptação e constituírem um pré-requisito para o vDC de destino, será necessário recriar o contexto de encriptação sobre este último.

Consulte o nosso manual sobre a [ativação da encriptação das máquinas virtuais](../vm-encrypt/) para ativar o KMS no vDC de destino.

#### Rede

##### **vRack**

> [!warning]
>
> As VMnetwork situadas na mesma região não poderão ser interligadas num vRack.
>

No âmbito de uma migração, pode associar os seus serviços Managed Bare Metal no mesmo vRack. Consulte o nosso manual sobre [a utilização do Private Cloud num vRack](../vrack-essentials/).

### Contexto VMware

#### Configuração do cluster

##### **Configuração da VMware HA**

A migração implica a reconfiguração do VMware High Availability (HA), nomeadamente a ordem e a prioridade do boot. Consulte o [nosso manual sobre a configuração](https://docs.ovh.com/gb/en/managed-bare-metal/vmware-ha-high-availability/).

Eis uma lista de elementos a ter em conta:

- Regulações de monitorização do host
- Regulação da monitorização das VM
- Controlo de admissão
- Opções HA avançadas
- Substituição de VM

**Conselhos de automatização:**
A aplicação de comando Powercli "Get-Cluster" envia informações sobre os parâmetros de configuração HA e DRS que podem ser aplicados ao cluster de destino com a aplicação de comando "Set-Cluster".

##### **Configuração da VMware DRS**

A migração implica a reconfiguração da função VMware DRS (Distributed Resource Scheduler), em particular regras de afinidade ou de anti-afinidade para os grupos de hosts e de VMs. Consulte o nosso manual sobre a [configuração do VMware DRS](../vmware-drs-distributed-ressource-scheduler-new/).

Eis uma lista dos elementos a ter em conta:

- Nível de automatização
- Grupos de VMs/hosts
- Regras de afinidade/anti-afinidade das VMs/hosts
- Substituição das máquinas virtuais

**Conselhos de automatização:** [Este fio de discussão da comunidade VMware](https://communities.vmware.com/t5/VMware-PowerCLI-Discussions/Backup-Restore-DRS-VM-affinity-anti-affinity-rules-can-these-be/td-p/733981/page/2) detalha as opções de exportação e importação das regras de afinidade através do powercli.

##### **Pools de recursos**

A migração requer a reconstrução dos pools de recursos, nomeadamente as reservas, as partilhas e as aplicações virtuais. Isto também se aplica aos vApps e a qualquer configuração de comando de arranque definida nos vApps.

Para mais informações, consulte a documentação da [VMware para a gestão das pools de recursos](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.resmgmt.doc/GUID-60077B40-66FF-4625-934A-641703ED7601.html){.external}.

Eis uma lista de elementos a ter em conta:

- Parâmetros das partilhas CPU/memória
- Reservas CPU/memória
- Opção evolutiva CPU/Memory
- Limites CPU/memória

**Conselhos de automatização:** A applet de comando Powercli "Get-ResourcePool" para reunir as informações da lista de recursos partilhados e a applet de comando "New-ResourcePool" para recriar a lista de recursos partilhados no vDC de destino.

##### **Clusters dos datastores**

Se estiverem presentes clusters de datastores na infraestrutura de origem, a migração pode exigir a recriação destes clusters de datastores no vDC de destino, se for necessário o mesmo nível de estrutura e o SDRS.

Eis uma lista dos elementos a ter em conta:

- Nível de automatização SDRS
- Espaço SDRS, I/O, regra, estratégia, parâmetros de evacuação das VMs
- Regras de afinidade/anti-afinidade SDRS
- Alterações das VMs SDRS

##### **Redes**

A migração implica a recriação dos grupos de portas virtuais do vRack no vDC de destino para garantir a coerência da rede de VMs. Se as VLAN vRack estiverem a ser utilizadas no vRack da infraestrutura de origem, podem ser utilizadas para alargar o domínio L2 ao vDC de destino, de modo a permitir um plano de migração mais escalonado. Para mais informações, consulte o nosso guia sobre a [Utilização da cloud privada num vRack](../vrack-essentials/).

Eis uma lista dos elementos a ter em conta:

- Tipo de VLAN dos portgroups
- Parâmetros de segurança
- Parâmetros de Teaming e Failover
- Atribuição dos recursos de rede do cliente

Para mais informações consulte a documentação da VMware:
<br>- >Teaming [& Failover](https://docs.vmware.com/en/VMware-vSphere/6.0/com.vmware.vsphere.hostclient.doc/GUID-BB8EC262-5F85-4F42-AFC5-5FED456E2C11.html)

**Conselhos de automatização:** A applet de comandos Powercli "Export-VDPortGroup" pode recuperar informações de Portgroups virtuais distribuídos que podem ser posteriormente importadas no Distributed Switch de destino através do applet de comandos "New-VDPortgroup -BackupPath".

##### **OVHcloud Veeam Backup**

Se a aplicação Veeam fornecida pela OVHcloud é atualmente utilizada para guardar as VMs na infraestrutura de origem, será necessário recriar a configuração de backup para as VMs no vDC de destino após a migração.

Eis uma lista dos elementos a ter em conta:

- Lista das VMs em curso de backup
- Parâmetros de backup

Consulte o nosso manual para [ativar e utilizar o Veeam Backup Managed](https://docs.ovh.com/gb/en/managed-bare-metal/veeam-backup-as-a-service/).

**Conselhos de automatização:** A API OVHcloud fornece informações relativas a cada backup de VM através:

> [!api]
>
> @api {GET} /dedicatedCloud/{serviceName}/datacenter/{datacenterId}/vm/{vmId}
>

A secção "backup" do ficheiro json devolvido fornece informações sobre a configuração de backup atual.

#### Organização logística

Por razões de organização, as máquinas virtuais, os hosts ou os datastores podem ter sido colocados em diretórios.

Se esta organização ainda for necessária, terá de a criar novamente no vDC de destino.

#### VM

Existem vários métodos para migrar VM para um novo vDC.

Se a sua fonte estiver em hosts Intel, pode utilizar o vMotion para migrar as VM para o novo VDC.

Se a sua fonte estiver localizada em hosts AMD, elimine-os e utilize a migração a frio.

## Saiba mais

Junte-se à nossa comunidade de utilizadores <https://community.ovh.com/>.
