---
title: Migrare un'infrastruttura verso un nuovo vDC
excerpt: Come gestire tutti gli aspetti della migrazione da un'infrastruttura a un vDC
slug: vdc-migration
section: Per iniziare
order: 6
hidden: true
---

> [!primary]
> Questa traduzione è stata generata automaticamente dal nostro partner SYSTRAN. I contenuti potrebbero presentare imprecisioni, ad esempio la nomenclatura dei pulsanti o alcuni dettagli tecnici. In caso di dubbi consigliamo di fare riferimento alla versione inglese o francese della guida. Per aiutarci a migliorare questa traduzione, utilizza il pulsante "Modifica" di questa pagina.
>

**Ultimo aggiornamento: 16 dicembre 2020**

> [!warning]
>
> Il percorso di migrazione vDC non è ancora disponibile, in quanto sono in corso aggiornamenti e interventi di manutenzione. Ti avviseremo non appena possibile.
>

## Obiettivo

La migrazione da un'infrastruttura a una nuova vDC comporta due aspetti:

* Il contesto Managed Bare Metal OVHcloud, che comprende la parte cliente e l'amministrazione della tua infrastruttura.
* Il contesto VMware che comprende l'intero ecosistema VMware, sull'infrastruttura stessa.

**Questa guida ti mostra come migrare un'infrastruttura OVHcloud preesistente verso un nuovo vDC.**

## Prerequisiti

* Un'infrastruttura Dedicated Cloud
* Avere accesso allo [Spazio Cliente OVHcloud](https://www.ovh.com/auth/?action=gotomanager&from=https://www.ovh.it/&ovhSubsidiary=it){.external}, sezione `Bare Metal Cloud`{.action} e `Managed Bare Metal`{.action}.

## Procedura

Questa guida utilizza le nozioni di **infrastruttura di origine** e di **vDC di destinazione**.

### Contexte OVHcloud

#### Sicurezza

##### **Contesto di connessione al Managed Bare Metal**

Per connetterti alla piattaforma VMware, puoi scegliere di bloccare di default l'accesso a vSphere. Per effettuare questa operazione, consulta la guida sulla [politica di accesso al vCenter](../modificare-la-politica-di-accesso-al-vcenter/).

In seguito al cambiamento della politica di accesso, se questa è passata in "limitata", è necessario applicare gli stessi IP di connessione al vDC di destinazione che all'infrastruttura di origine.

##### **Utenti del Managed Bare Metal**

Nel ciclo di vita dell'infrastruttura originaria, può essere stato creato un elenco di utenti per esigenze aziendali o organizzative. È quindi necessario crearli nuovamente sul vDC di destinazione e attribuire loro i diritti adeguati, in funzione della configurazione del vDC di destinazione.

Consulta le nostre guide per [modificare i diritti di un utente](../modificare-permessi-di-un-utente/), [modificare la password di un utente](../modificare-password-utente/) e [associare un'email a un utente](../associare-email-a-un-utente-vsphere/).

##### **Key Management Server (KMS)**

Se le macchine virtuali sono protette da crittografia e questo costituisce un prerequisito per il vDC di destinazione, sarà necessario ricreare il contesto di cifratura su quest'ultimo.

Consulta la nostra guida sull'[attivazione della crittografia delle macchine virtuali](../vm-encrypt/) per attivare il KMS sulla vDC di destinazione.

#### Rete

##### **vRack**

> [!warning]
>
> Le VMnetwork situate nella stessa Region non potranno essere interconnesse con una vRack.
>

In caso di migrazione, puoi associare i tuoi servizi Managed Bare Metal alla stessa vRack. Consulta la nostra guida [sull'utilizzo del Private Cloud in una vRack](../vrack-essentials/).

### Contexte VMware

#### Configurazione del cluster

##### **Configurazione di VMware HA**

La migrazione comporta la rielaborazione della configurazione del VMware High Availability (HA), in particolare l'ordine e la priorità del boot. Consulta [la nostra guida sulla configurazione](https://docs.ovh.com/gb/en/managed-bare-metal/vmware-ha-high-availability/).

Ecco una lista di elementi da considerare:

- Regolamenti di monitoring dell'host
- Regolamenti di monitoring delle VM
- Controllo di ammissione
- Opzioni HA avanzate
- Sostituzioni di VM

**Consigli di automatizzazione:**
L'applet di comando Powercli "Get-Cluster" restituisce informazioni sui parametri di configurazione HA e DRS che possono essere applicati al cluster di destinazione con l'applet di comando "Set-Cluster".

##### **Configurazione di VMware DRS**

La migrazione comporta la riconfigurazione della funzione VMware DRS (Distributed Resource Scheduler), in particolare delle regole di affinità o di anti-affinità per i gruppi di host e di VM. Consulta la nostra guida sulla [configurazione di VMware DRS](https://docs.ovh.com/gb/en/managed-bare-metal/vmware-drs-distributed-ressource-scheduler/).

Ecco un elenco degli elementi da prendere in considerazione:

- Livello di automatizzazione
- Gruppi di VM/host
- Regole di affinità/anti-affinità delle VM/host
- Sostituzioni delle macchine virtuali

**Consigli di automatizzazione:** [Questa conversazione della Community VMware](https://communities.vmware.com/t5/VMware-PowerCLI-Discussions/Backup-Restore-DRS-VM-affinity-anti-affinity-rules-can-these-be/td-p/733981/page/2) descrive in dettaglio le opzioni di esportazione e importazione delle regole di affinità via powercli.

##### **Pool di risorse**

La migrazione richiede la ricostruzione dei pool di risorse, in particolare prenotazioni, condivisioni e applicazioni virtuali. Ciò vale anche per le vApp e per qualsiasi configurazione di comando di avviamento definita nelle vApp.

Per maggiori informazioni, consulta la documentazione di [VMware per la gestione dei pool di risorse](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.resmgmt.doc/GUID-60077B40-66FF-4625-934A-641703ED7601.html){.external}.

Ecco una lista di elementi da considerare:

- Impostazioni CPU/memoria
- Prenotazioni CPU/memoria
- Opzione scalabile CPU/Memory
- Limiti CPU/memoria

**Consigli di automatizzazione:** L'applet di comando Powercli "Get-ResourcePool" per raccogliere le informazioni della lista di risorse condivise e l'applet di ordine "New-ResourcePool" per ricreare la lista di risorse condivise sulla vDC di destinazione.

##### **Cluster dei datastore**

Se nell'infrastruttura di origine sono presenti cluster di datastore, la migrazione può richiedere la creazione di questi cluster di datastore sul vDC di destinazione, se sono necessari lo stesso livello di struttura e l'SDRS.

Ecco un elenco degli elementi da prendere in considerazione:

- Livello di automatizzazione SDRS
- Spazio SDRS, I/O, regola, strategia, parametri di evacuazione delle VM
- Regole di affinità/anti-affinità SDRS
- Modifica delle VM SDRS

##### **Rete**

La migrazione comporta la creazione di gruppi di porte virtuali di vRack sulla vDC di destinazione per garantire la coerenza della rete di VM. Se sulla vRack dell'infrastruttura di origine sono in corso di utilizzo VLAN vRack, possono essere utilizzate per estendere il dominio L2 alla vDC di destinazione per consentire un piano di migrazione più scaglionato. Per maggiori informazioni, consulta la nostra guida sull'[utilizzo del Cloud privato in una vRack](../vrack-essentials/).

Ecco un elenco degli elementi da prendere in considerazione:

- Tipo di VLAN dei portgroups
- Parametri di sicurezza
- Impostazioni di Teaming e Failover
- Assegnazione delle risorse di rete del cliente

Per maggiori informazioni, consulta la documentazione di VMware:
<br>- >- [Teaming & Failover](https://docs.vmware.com/en/VMware-vSphere/6.0/com.vmware.vsphere.hostclient.doc/GUID-BB8EC262-5F85-4F42-AFC5-5FED456E2C11.html)

**Consigli di automatizzazione:** L'applet di comando Powercli "Export-VDPortGroup" può recuperare informazioni da Portgroups virtuali distribuiti che possono essere successivamente importate nel Distributed Switch di destinazione tramite l'applet di comando "New-VDPortgroup -BackupPath".

##### **OVHcloud Veeam Backup**

Se l'applicazione Veeam fornita da OVHcloud è utilizzata per salvare le VM sull'infrastruttura di origine, sarà necessario ricreare la configurazione di backup delle VM nella vDC di destinazione dopo la migrazione.

Ecco un elenco degli elementi da prendere in considerazione:

- Lista delle VM in corso di backup
- Impostazioni di backup

Consulta la nostra guida per [attivare e utilizzare Veeam Backup Managed](https://docs.ovh.com/gb/en/managed-bare-metal/veeam-backup-as-a-service/).

**Consigli di automatizzazione:** L'API OVHcloud fornisce informazioni relative a ogni backup delle VM tramite:

> [!api]
>
> @api {GET} /dedicatedCloud/{serviceName}/datacenter/{datacenterId}/vm/{vmId}
>

La sezione "backup" del file json restituito fornisce informazioni sulla configurazione di backup corrente.

#### Organizzazione logistica

Per motivi organizzativi, le macchine virtuali, gli host o i datastore possono essere stati inseriti in directory.

Se questa organizzazione è ancora necessaria, dovrai crearla nuovamente nella vDC di destinazione.

#### VM

Esistono diversi metodi per migrare le VM verso un nuovo vDC.

Se la tua fonte si trova su host Intel, puoi utilizzare vMotion per migrare le VM sulla nuova VDC.

Se la tua fonte è presente su host AMD, disattivali e utilizza la migrazione a freddo.

## Per saperne di più

Unisciti alla nostra Community di utenti <https://community.ovh.com/en/>.
