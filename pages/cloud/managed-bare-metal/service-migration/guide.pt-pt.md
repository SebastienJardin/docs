---
title: Migrar uma infraestrutura para o Managed Bare Metal
excerpt: Saiba como gerir todos os aspetos relacionados com a migração de uma infraestrutura para a solução Managed Bare Metal
slug: sddc-migration
section: Introdução
order: 5
---

> [!primary]
> Esta tradução foi automaticamente gerada pelo nosso parceiro SYSTRAN. Em certos casos, poderão ocorrer formulações imprecisas, como por exemplo nomes de botões ou detalhes técnicos. Recomendamos que consulte a versão inglesa ou francesa do manual, caso tenha alguma dúvida. Se nos quiser ajudar a melhorar esta tradução, clique em "Contribuir" nesta página.
>

**Última atualização: 24/11/2020**

## Objetivo

A migração de um serviço Managed Bare Metal inclui dois aspetos:

* O contexto Managed Bare Metal OVHcloud que compreende a parte cliente, a administração da sua infraestrutura.
* O contexto VMware, que inclui todo o eco-sistema VMware, na própria infraestrutura.

**Saiba como cobrir todos os aspetos relacionados com a migração para um serviço Managed Bare Metal**

> [!primary]
>
> Se optar por migrar uma infraestrutura para um novo vDC, siga [este guia dedicado](https://docs.ovh.com/gb/en/managed-bare-metal/vdc-migration/).
>

## Requisitos

* Ter acesso à Área de [Cliente OVHcloud](https://www.ovh.com/auth/?action=gotomanager&from=https://www.ovh.pt/&ovhSubsidiary=pt){.external}, na secção `Bare Metal Cloud`{.action} e depois `Managed Bare Metal`{.action}.
* Dispor de um produto [Managed Bare Metal](https://www.ovhcloud.com/pt/managed-bare-metal/){.external}.

## Instruções

Neste guia, abordaremos as noções de **infraestrutura de origem** e de **Managed Bare Metal de destino**.

### Contexto Managed Bare Metal

#### Segurança

##### **Contexto de ligação ao Managed Bare Metal**

Para se ligar à plataforma VMware, pode escolher bloquear o acesso ao vSphere de forma padrão. Para isso, consulte o nosso guia sobre a [política de acesso ao vCenter](../alterar-a-politica-de-acesso-ao-vcenter/).

No seguimento da alteração da política de acesso, se esta passou para "restrita", é evidente que é necessário aplicar os mesmos IP de ligação no Managed Bare Metal de destino que na infraestrutura de origem.

##### **Utilizadores do Managed Bare Metal**

No ciclo de vida da infraestrutura original, pode ter sido criada uma lista de utilizadores para fins profissionais ou necessidades organizacionais. Por isso, deve criar novamente os servidores no Managed Bare Metal de destino e atribuir-lhes os direitos adequados, em função da configuração do Managed Bare Metal de destino.

Para isso, consulte os nossos manuais para [alterar as permissões de um utilizador](../alterar-os-direitos-de-um-utilizador/), [alterar a palavra-passe de um utilizador](../alteracao-da-palavra-passe-de-utilizador/) e [associar um e-mail a um utilizador](../associar-e-mail-a-um-utilizador/).

##### **KMS**

Se as máquinas virtuais forem protegidas por encriptação e constituírem um pré-requisito para o Managed Bare Metal de destino, será necessário recriar o contexto de encriptação sobre este último.

Consulte o nosso manual sobre a [ativação da encriptação das máquinas virtuais](../vm-encrypt/) para ativar o KMS no Managed Bare Metal de destino.

#### Rede

##### **vRack**

> [!warning]
>
> As VMnetwork situadas na mesma região não poderão ser interligadas num vRack.
>

No âmbito de uma migração, pode associar os seus serviços Managed Bare Metal no mesmo vRack. Consulte o nosso manual sobre [a utilização do Private Cloud num vRack](../vrack-essentials/).

##### **Rede pública**

> [!warning]
>
> Se o Private Cloud/PCC for anterior a 2016, sugerimos que contacte as nossas equipas de suporte para verificar os pré-requisitos necessários.
>

Se os endereços IP públicos ligados à infraestrutura de origem forem necessários no Managed Bare Metal de destino, será necessário efetuar a sua transferência.

Consulte o nosso guia para [migrar blocos IP entre dois serviços Managed Bare Metal](../adicionar-bloco-ip/#migrar-um-bloco-ip-entre-dois-managed-bare-metal).

O vídeo abaixo também explica como migrar um bloco de endereços IP entre dois Hosted Private Cloud. Este método é igualmente aplicável a uma solução Managed Bare Metal.

<iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/Gemao3Fd7rI" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

### Contexto VMware

#### Configuração dos clusters

##### **Configuração da VMware HA**

A migração implica a reconfiguração do VMware High Availability (HA), nomeadamente a ordem e a prioridade do boot. Consulte o [nosso manual sobre a configuração](../vmware-ha-high-availability/).

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

**Conselhos de automatização:** A applet de comando Powercli "Get-ResourcePool" para reunir as informações da lista de recursos partilhados e a applet de comando "New-ResourcePool" para recriar a lista de recursos partilhados no Managed Bare Metal de destino.

##### **Clusters dos datastores**

Se estiverem presentes clusters de datastores na infraestrutura de origem, a migração pode exigir a recriação destes clusters de datastores no Managed Bare Metal de destino, se for necessário o mesmo nível de estrutura e o SDRS.

Eis uma lista dos elementos a ter em conta:

- Nível de automatização SDRS
- Espaço SDRS, I/O, regra, estratégia, parâmetros de evacuação das VMs
- Regras de afinidade/anti-afinidade SDRS
- Alterações das VMs SDRS

##### **Redes**

A migração implica a recriação dos grupos de portas virtuais do vRack no Managed Bare Metal de destino para garantir a coerência da rede de VM. Se as VLAN vRack estiverem a ser utilizadas no vRack da infraestrutura de origem, podem ser utilizadas para alargar o domínio L2 ao Managed Bare Metal de destino, de forma a permitir um plano de migração mais escalonado. Para mais informações, consulte o nosso guia sobre a [Utilização da cloud privada num vRack](../vrack-essentials/).

Eis uma lista dos elementos a ter em conta:

- Tipo de VLAN dos portgroups
- Parâmetros de segurança
- Parâmetros de Teaming e Failover
- Atribuição dos recursos de rede do cliente

Para mais informações consulte a documentação da VMware:
<br>- >Teaming [& Failover](https://docs.vmware.com/en/VMware-vSphere/6.0/com.vmware.vsphere.hostclient.doc/GUID-BB8EC262-5F85-4F42-AFC5-5FED456E2C11.html)

**Conselhos de automatização:** A applet de comandos Powercli "Export-VDPortGroup" pode recuperar informações de Portgroups virtuais distribuídos que podem ser posteriormente importadas no Distributed Switch de destino através do applet de comandos "New-VDPortgroup -BackupPath".

##### **OVHcloud Veeam Backup**

Se a aplicação Veeam fornecida pela OVHcloud é atualmente utilizada para guardar as VMs na infraestrutura de origem, será necessário recriar a configuração de backup para as VMs no Managed Bare Metal de destino após a migração.

Eis uma lista dos elementos a ter em conta:

- Lista das VMs em curso de backup
- Parâmetros de backup

Consulte o nosso manual para [ativar e utilizar o Veeam Backup Managed](https://docs.ovh.com/gb/en/managed-bare-metal/veeam-backup-as-a-service/).

**Conselhos de automatização:** A API OVHcloud fornece informações relativas a cada backup de VM através:

> \[!api]
>
> @api {GET} /dedicatedCloud/{serviceName}/datacenter/{datacenterId}/vm/{vmId}
>

A secção "backup" do ficheiro json devolvido fornece informações sobre a configuração de backup atual.

#### Organização do inventário

Por razões de organização, as VMs, os hosts ou os datastores podem ter sido colocados em diretórios.

Se esta organização ainda for necessária, terá de a criar novamente no Managed Bare Metal de destino.

**Conselhos de automatização:** Utilize a aplicação de comando Powercli "Get-Folder" para recolher as informações sobre a pasta e a aplicação de comando "New-Folder" para recriar a pasta no Managed Bare Metal de destino.

#### VM

Existem várias formas de migrar as VMs de uma infraestrutura para outra. Propomos-lhe que efetue a migração utilizando a solução Veeam e a tecnologia Veeam Replication.

São necessários os seguintes elementos:

* licenças SPLA (na infraestrutura de origem e no Managed Bare Metal de destino);
* uma licença Veeam;
* um endereço IP disponível na infraestrutura de origem e no Managed Bare Metal de destino;
* uma máquina virtual [Veeam Backup & Replication](../../storage/veeam-backup-replication/) na infraestrutura de origem;
* [autorizar a máquina virtual Veeam Backup & Replication a ligar](../autorizar-ip-a-ligar-se-ao-vcenter/)-se ao vCenter de origem e de destino.

Consulte o nosso manual para [implementar o Veeam Backup & Replication](../../storage/veeam-backup-replication/).

O vídeo abaixo explica como configurar um Hosted Private Cloud com a solução Veeam Backup & Replication. Este método é igualmente aplicável a uma solução Managed Bare Metal.

<iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/f8ufrsP4PQw" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>


<br>O vídeo seguinte explica em pormenor como replicar a sua infraestrutura Hosted Private Cloud com a solução Veeam Backup & Replication. Este tutorial também é válido para uma solução Managed Bare Metal.

<iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/NqNtKrJSH8w" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>


<br>Também pode consultar [a documentação do Veeam](https://www.veeam.com/veeam_backup_10_0_user_guide_vsphere_pg.pdf){.external}.


## Saiba mais

Fale com a nossa comunidade de utilizadores em <https://community.ovh.com/en/>.
