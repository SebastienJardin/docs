---
title: Eine Infrastruktur zu Managed Bare Metal migrieren
excerpt: In dieser Anleitung erfahren Sie, wie Sie alle Aspekte der Migration einer Infrastruktur auf die Managed Bare Metal Lösung verwalten.
slug: sddc-migration
section: Erste Schritte
order: 5
---

> [!primary]
> Diese Übersetzung wurde durch unseren Partner SYSTRAN automatisch erstellt. In manchen Fällen können ungenaue Formulierungen verwendet worden sein, z.B. bei der Beschriftung von Schaltflächen oder technischen Details. Bitte ziehen Sie beim geringsten Zweifel die englische oder französische Fassung der Anleitung zu Rate. Möchten Sie mithelfen, diese Übersetzung zu verbessern? Dann nutzen Sie dazu bitte den Button «Mitmachen» auf dieser Seite.
>

**Stand 24.11.2020**

## Ziel

Die Migration eines Managed Bare Metal Dienstes umfasst zwei Aspekte:

* OVHcloud Managed Bare Metal Kontext, der den Kunden und die Administration Ihrer Infrastruktur umfasst.
* Der VMware Kontext, der das gesamte VMware Ökosystem umfasst, auf der Infrastruktur selbst.

**Erfahren Sie, wie Sie alle Aspekte der Migration auf einen Managed Bare Metal Dienst abdecken**

> [!primary]
>
> Wenn Sie eine Infrastruktur auf ein neues vDC migrieren möchten, folgen Sie [dieser Anleitung](https://docs.ovh.com/gb/en/managed-bare-metal/vdc-migration/).
>

## Voraussetzungen

* Sie sind in Ihrem [OVHcloud](https://www.ovh.com/auth/?action=gotomanager&from=https://www.ovh.de/&ovhSubsidiary=de){.external} Kundencenter eingeloggt und befinden sich im `Bereich Bare Metal Cloud`{.action} und `Managed Bare Metal`{.action}.
* Sie besitzen ein [Managed Bare Metal Produkt](https://www.ovhcloud.com/de/managed-bare-metal/){.external}.

## In der praktischen Anwendung

In dieser Anleitung werden die Begriffe "**Original Infrastruktur"** und "**Bare Metal"beschrieben**.

### Kontext Managed Bare Metal

#### Sicherheit

##### **Kontext für die Verbindung zu Managed Bare Metal**

Um sich mit der VMware-Plattform zu verbinden, können Sie standardmäßig den Zugriff auf vSphere blockieren. Lesen Sie hierzu unsere Anleitung [zur Zugriffseinstellungen für vCenter](../zugriffsregeln-fuer-vcenter-aendern/).

Nach der Änderung der Zugangseinstellungen, wenn diese "eingeschränkt"wurde, müssen natürlich die gleichen Login-IPs für das Ziel Managed Bare Metal wie für die ursprüngliche Infrastruktur verwendet werden.

##### **Benutzer von Managed Bare Metal**

Im Lebenszyklus der ursprünglichen Infrastruktur kann eine Nutzerliste für Geschäftszwecke oder organisatorische Erfordernisse erstellt worden sein. Erstellen Sie sie also erneut auf dem Ziel-Managed Bare Metal und weisen Sie ihnen entsprechend der Konfiguration des Ziel-Managed Bare Metal die entsprechenden Rechte zu.

Lesen Sie hierzu unsere Anleitungen, [um die Rechte eines Benutzers](../die-rechte-eines-nutzers-aendern/) zu ändern, [das Passwort eines Benutzers zu ändern und ](../aenderung-des-nutzerpassworts/)einem Benutzer eine E-Mail zuzuweisen[ ](../e-mail-mit-nutzerprofil-verbinden/).

##### **KMS**

Wenn virtuelle Maschinen durch Verschlüsselung geschützt sind und dies eine Voraussetzung für das Ziel-Managed Bare Metal ist, muss der Verschlüsselungskontext neu definiert werden.

Lesen Sie unsere Anleitung zur [Aktivierung der Verschlüsselung von virtuellen Maschinen](../vm-encrypt/), um das KMS für das Ziel-Managed Bare Metal zu aktivieren.

#### Netzwerk

##### **vRack**

> [!warning]
>
> VMnetwork aus derselben Region können nicht in einem vRack untereinander verbunden werden.
>

Im Rahmen einer Migration können Sie Ihre Managed Bare Metal Dienste mit demselben vRack verbinden. Lesen Sie unsere [Anleitung zur Verwendung der Private Cloud in einem vRack](../managed-bare-metal-vrack/).

##### **Öffentliches Netzwerk**

> [!warning]
>
> Wenn Ihr Private Cloud/Dedicated Cloud Angebot vor 2016 liegt, wenden Sie sich bitte an unsere Support-Teams, um die notwendigen Voraussetzungen zu überprüfen.
>

Wenn für das Ziel-Managed Bare Metal öffentliche IP-Adressen benötigt werden, die mit der ursprünglichen Infrastruktur verbunden sind, muss der Transfer durchgeführt werden.

Lesen Sie unsere Anleitung [zur Migration von IP-Blöcken zwischen zwei Managed Bare Metal Diensten](../ip-block-hinzufuegen/#ip-block-zwischen-zwei-managed-bare-metal-migrieren).

Im folgenden Video erfahren Sie auch, wie Sie einen IP-Adressblock zwischen zwei Hosted Private Cloud umziehen. Diese Methode gilt auch für Managed Bare Metal Lösungen.

<iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/Gemao3Fd7rI" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

### VMware Kontext

#### Cluster-Konfiguration

##### **VMware HA Konfiguration**

Bei der Migration muss die VMware High Availability (HA) neu konfiguriert werden, darunter auch die Reihenfolge und Priorität des Boot. Zur [Konfiguration lesen Sie unsere Anleitung](../vmware-ha-high-availability/).

Hier eine Liste der zu berücksichtigenden Elemente:

- Monitoring-Einstellungen des Hosts
- VM-Monitoring-Einstellungen
- Zugangssteuerung
- Fortgeschrittene HA Optionen
- Ersatz von VMs

**Automatisierungsanleitungen:**
Das Powercli Powercli "Get-Cluster"sendet Informationen zu den HA- und DRS-Konfigurationseinstellungen zurück, die mit dem Bestellapplet "Set-Cluster"auf den Zielcluster angewendet werden können.

##### **VMware DRS Konfiguration**

Migration bedeutet eine Neukonfiguration der VMware DRS Funktion (Distributed Resource Scheduler), insbesondere der Affinitäts- oder Anti-Affinitätsregeln für Host- und VMs-Gruppen. Lesen Sie unsere Anleitung zur [Konfiguration von VMware DRS](../vmware-drs-distributed-resource-scheduler/).

Hier eine Liste der zu berücksichtigenden Elemente:

- Automatisierungslevel
- VMs- / Host-Gruppen
- Affinitätsregeln / Anti-Affinitätsregeln für VMs / Hosts
- Austausch der virtuellen Maschinen

**Automatisierungsanleitungen:** [Dieser Diskussionsfaden der VMware](https://communities.vmware.com/t5/VMware-PowerCLI-Discussions/Backup-Restore-DRS-VM-affinity-anti-affinity-rules-can-these-be/td-p/733981/page/2) Community beschreibt die Export- und Import-Optionen der Affinitätsregeln via Powercli.

##### **Ressourcen-Pools**

Die Migration erfordert die Rekonstruktion der Ressourcen-Pools, darunter Reservierungen, Freigaben und virtuelle Anwendungen. Dies gilt auch für vApps und alle in vApps definierten Startsteuerkonfigurationen.

Weitere Informationen finden Sie in der Dokumentation von [VMware zur Verwaltung von Ressourcen-Pools](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.resmgmt.doc/GUID-60077B40-66FF-4625-934A-641703ED7601.html){.external}.

Hier eine Liste der zu berücksichtigenden Elemente:

- Einstellungen der CPU / Arbeitsspeicher Freigaben
- CPU / RAM Reservierungen
- CPU/Memory Option skalierbar
- CPU / RAM Limits

**Automatisierungsanleitungen:** Powercli "Get-ResourcePool" Powercli-Bestellapplet, um die Informationen aus der Liste geteilter Ressourcen zu sammeln, und das Bestellapplet "New-ResourcePool", um die Liste geteilter Ressourcen im Ziel-Managed Bare Metal neu zu erstellen.

##### **Datastore-Cluster**

Wenn Datastore-Cluster in der ursprünglichen Infrastruktur vorhanden sind, kann es zur Migration notwendig sein, diese Datastore-Cluster auf dem Ziel-Managed Bare Metal neu zu erstellen, wenn die gleiche Struktur und das SDRS erforderlich sind.

Hier eine Liste der zu berücksichtigenden Elemente:

- SDRS Automatisierungslevel
- SDRS, I/O Bereich, Regel, Strategie, Evakuierungsparameter für VMs
- SDRS Affinitätsregeln
- Änderungen der SDRS VMs

##### **Netzwerke**

Die Migration beinhaltet die Rekrutierung der virtuellen Portgruppen von vRack auf dem Ziel-Managed Bare Metal, um die Kohärenz des VMs-Netzwerks zu gewährleisten. Werden vRack VLANs auf dem vRack der ursprünglichen Infrastruktur verwendet, können sie verwendet werden, um die L2-Domain auf das Ziel Managed Bare Metal zu erweitern, um einen gestuften Migrationsplan zu ermöglichen. Weitere Informationen finden Sie in unserer Anleitung zur Verwendung der [Private Cloud in einem vRack](../vrack-essentials/).

Hier eine Liste der zu berücksichtigenden Elemente:

- Typ des VLAN der Portgroups
- Sicherheitseinstellungen
- Teaming und Failover Einstellungen
- Zuweisung der Netzwerkressourcen des Kunden

Weitere Informationen finden Sie in der Dokumentation von VMware:
<br>- >- [Teaming & Failover](https://docs.vmware.com/en/VMware-vSphere/6.0/com.vmware.vsphere.hostclient.doc/GUID-BB8EC262-5F85-4F42-AFC5-5FED456E2C11.html)

**Automatisierungsanleitungen:** Das Powercli-Bestellapplet "Export-VDPortGroup" kann verteilte virtuelle Portgroups-Informationen abrufen, die dann mit dem "New-VDPortgroup -BackupPath"-Bestellapplet in den Ziel-Distributed Switch importiert werden können.

##### **OVHcloud Veeam Backup**

Wenn die von OVHcloud zur Verfügung gestellte Veeam-Anwendung derzeit verwendet wird, um die VMs auf der ursprünglichen Infrastruktur zu sichern, muss die Backup-Konfiguration für die VMs nach der Migration im Ziel-Managed Bare Metal neu erstellt werden.

Hier eine Liste der zu berücksichtigenden Elemente:

- Liste der VMs wird gesichert
- Backup-Einstellungen

Lesen Sie unsere Anleitung, [um Veeam Managed Backup zu aktivieren und zu verwenden](https://docs.ovh.com/gb/en/managed-bare-metal/veeam-backup-as-a-service/).

**Automatisierungsanleitungen:** Die OVHcloud API liefert Informationen zu jedem VM-Backup über:

> \[!api]
>
> @api {GET} /dedicatedCloud/{serviceName}/datacenter/{datacenterId}/vm/{vmId}
>

Der Abschnitt "Backup"der zurückgegebenen json-Datei enthält Informationen zur aktuellen Backup-Konfiguration.

#### Organisation des Inventars

Aus organisatorischen Gründen können VMs, Hosts oder Datastores in Ordnern angelegt worden sein.

Wenn diese Organisation immer noch notwendig ist, müssen Sie sie erneut im Ziel-Managed Bare Metal erstellen.

**Automatisierungsanleitungen:** Verwenden Sie das Powercli Powercli "Get-Folder" Bestellapplet, um Informationen zum Ordner und dem New-Folder Bestellapplet zu sammeln, um den Ordner auf dem Ziel-Managed Bare Metal wiederherzustellen.

#### VM

Es gibt mehrere Möglichkeiten, die VMs von einer Infrastruktur auf eine andere zu migrieren. Wir bieten Ihnen an, die Migration mit der Veeam Lösung und der Veeam Replication Technologie durchzuführen.

Hierzu sind folgende Elemente erforderlich:

* SPLA-Lizenzen (auf der ursprünglichen Infrastruktur und dem Ziel Managed Bare Metal);
* eine Veeam-Lizenz;
* eine auf der ursprünglichen Infrastruktur und dem Ziel-Managed Bare Metal verfügbare IP-Adresse;
* eine virtuelle [Veeam Backup & Replication](../../storage/veeam-backup-replication/) Maschine auf der ursprünglichen Infrastruktur;
* [Der Veeam Backup & Replication Virtuellen Maschine erlauben, sich](../verbindung-von-ip-zum-vcenter-erlauben/) mit dem Ursprungs- und dem Ziel-vCenter zu verbinden.

Lesen Sie unsere Anleitung [zum Einrichten von Veeam Backup & Replication](../../storage/veeam-backup-replication/).

Im folgenden Video erfahren Sie, wie Sie eine Hosted Private Cloud mit der Veeam Backup & Replication Lösung konfigurieren. Diese Methode gilt auch für Managed Bare Metal Lösungen.

<iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/f8ufrsP4PQw" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>


<br>Im folgenden Video erfahren Sie, wie Sie Ihre Hosted Private Cloud Infrastruktur mit der Veeam Backup & Replication Lösung replizieren. Diese Anleitung gilt auch für eine Managed Bare Metal Lösung.

<iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/NqNtKrJSH8w" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>


<br>Sie können auch [die Veeam-Dokumentation einsehen](https://www.veeam.com/veeam_backup_10_0_user_guide_vsphere_pg.pdf){.external}.


## Weiterführende Informationen

Für den Austausch mit unserer User Community gehen Sie auf <https://community.ovh.com/en/>.
