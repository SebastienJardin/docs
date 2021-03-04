---
title: Eine Infrastruktur zu Managed Bare Metal migrieren
excerpt: Erfahren Sie hier, wie Sie alle Aspekte der Migration einer Infrastruktur zu Managed Bare Metal verwalten
slug: sddc-migration
section: Erste Schritte
order: 5
---

> [!primary]
> Diese Übersetzung wurde durch unseren Partner SYSTRAN automatisch erstellt. In manchen Fällen können ungenaue Formulierungen verwendet worden sein, z.B. bei der Beschriftung von Schaltflächen oder technischen Details. Bitte ziehen Sie beim geringsten Zweifel die englische oder französische Fassung der Anleitung zu Rate. Möchten Sie mithelfen, diese Übersetzung zu verbessern? Dann nutzen Sie dazu bitte den Button «Mitmachen» auf dieser Seite.
>

**Letzte Aktualisierung am 24.11.2020**

## Ziel

Die Migration eines Managed Bare Metal Dienstes umfasst zwei Aspekte:

- Die OVHcloud Infrastruktur selbst, die die Kundenseite der Verwaltung einer Infrastruktur umfasst. 
- Die VMware Infrastruktur, die das VMware Ökosystem beinhaltet.

**Diese Anleitung erklärt, wie Sie alle Aspekte der Migration auf einen Managed Bare Metal Dienst abdecken.**

> [!primary]
>
> Wenn Sie eine Infrastruktur auf ein neues vDC migrieren möchten, folgen Sie [dieser Anleitung](../vdc-migration/).
>

## Voraussetzungen

- Sie haben Zugriff auf Ihr [OVHcloud Kundencenter](https://www.ovh.com/auth/?action=gotomanager&from=https://www.ovh.de/&ovhSubsidiary=de), Bereich `Bare Metal Cloud`{.action} und `Managed Bare Metal`{.action}.
- Sie verfügen über eine [Managed Bare Metal](https://www.ovhcloud.com/de/managed-bare-metal/) Plattform.

## In der praktischen Anwendung

Diese Anleitung verwendet nachfolgend die Bezeichnungen **Quellinfrastruktur** und **Ziel-Managed Bare Metal**.

### OVHcloud Kontext

#### Sicherheit

##### **Managed Bare Metal Zugriff**

Um sich mit der VMware-Plattform zu verbinden, können Sie standardmäßig den Zugriff auf vSphere blockieren. Lesen Sie hierzu unsere Anleitung bezüglich  [Zugriffseinstellungen für vCenter](../zugriffsregeln-fuer-vcenter-aendern/).

Wenn die Zugangseinstellungen auf "Eingeschränkt" gesetzt wurden, müssen die gleichen Login-IPs für die Ziel-Managed Bare Metal wie für die Quellinfrastruktur freigegeben werden.

##### **Managed Bare Metal Benutzer**

Im Lebenszyklus der ursprünglichen Infrastruktur kann eine Nutzerliste für betriebliche oder organisatorische Erfordernisse erstellt worden sein. Diese müssen erneut auf der Ziel-Managed Bare Metal angelegt werden. Sie müssen den Benutzern dann entsprechend der Konfiguration des Ziel-Managed Bare Metal die angemessenen Rechte zuweisen.

Lesen Sie hierzu unsere Anleitungen zu [Änderung von Benutzerrechten](../die-rechte-eines-nutzers-aendern/), [Passwortänderung](../aenderung-des-nutzerpassworts/) und [Zuweisung von E-Mail](../e-mail-mit-nutzerprofil-verbinden/).

##### **Key Management Server (KMS)**

Wenn virtuelle Maschinen mit Verschlüsselung geschützt sind und dies eine Voraussetzung für das Ziel-Managed Bare Metal ist, muss der Verschlüsselungskontext dort neu erstellt werden.

Lesen Sie unsere Anleitung zur [Aktivierung der Verschlüsselung von virtuellen Maschinen](../vm-encrypt/), um KMS für das Ziel-Managed Bare Metal zu aktivieren.

#### Netzwerk

##### **vRack**

> [!warning]
>
> VMnetworks aus derselben Region können nicht in einem vRack untereinander verbunden werden.
>

Im Rahmen einer Migration können Sie Ihre Managed Bare Metal Dienste mit demselben vRack verbinden. Lesen Sie unsere Anleitung zur [Verwendung von Managed Bare Metal in einem vRack](../vrack-essentials/).

##### **Öffentliches Netzwerk**

> [!warning]
>
> Wenn Ihre Quellinfrastruktur vor 2016 eingerichtet wurde, wenden Sie sich bitte an unsere Support-Teams, um die notwendigen Voraussetzungen zu überprüfen.
>

Wenn für das Ziel-Managed Bare Metal die öffentlichen IP-Adressen benötigt werden, die mit der Quellinfrastruktur verbunden sind, müssen sie migriert werden.

Lesen Sie hierzu unsere Anleitung zur [Migration von IP-Blöcken zwischen zwei Managed Bare Metal Diensten](../ip-block-hinzufuegen/#ip-block-zwischen-zwei-managed-bare-metal-migrieren).

Dieses Video zeigt, wie Sie einen IP-Adressblock zwischen zwei Hosted Private Cloud Diensten umziehen. Diese Methode gilt auch für Managed Bare Metal Lösungen.

<iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/Gemao3Fd7rI" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

### VMware Kontext

#### Cluster-Konfiguration

##### **VMware HA Konfiguration**

Bei der Migration muss VMware High Availability (HA) neu konfiguriert werden, darunter auch die Reihenfolge und Priorität des Boot. Lesen Sie unsere Anleitung zur [VMware HA Konfiguration](../vmware-ha-high-availability/).

Hier eine Liste der zu berücksichtigenden Elemente:

- Monitoring-Einstellungen des Hosts
- VM-Monitoring-Einstellungen
- Zugangssteuerung
- Fortgeschrittene HA Optionen
- VM Overrides

**Automatisierungshinweise:**
Das Powercli Cmdlet "Get-Cluster" gibt Informationen zu den HA- und DRS-Konfigurationseinstellungen zurück, die mit dem Cmdlet "Set-Cluster" auf dem Zielcluster angewendet werden können.

##### **VMware DRS Konfiguration**

Die Migration zieht eine Neukonfiguration der VMware DRS Funktion (Distributed Resource Scheduler) nach sich, insbesondere der Affinitäts- oder Anti-Affinitätsregeln für Host- und VM-Gruppen. Lesen Sie unsere Anleitung zur [Konfiguration von VMware DRS](../vmware-drs-distributed-resource-scheduler/).

Hier eine Liste der zu berücksichtigenden Elemente:

- Automatisierungslevel
- VM- / Host-Gruppen
- Affinitätsregeln / Anti-Affinitätsregeln für VMs / Hosts
- VM Overrides

**Automatisierungshinweise:** Dieser [Beitrag in der VMware Community](https://communities.vmware.com/t5/VMware-PowerCLI-Discussions/Backup-Restore-DRS-VM-affinity-anti-affinity-rules-can-these-be/td-p/733981/page/2) beschreibt Export- und Import-Optionen von Affinitätsregeln via Powercli.

##### **Ressourcen-Pools**

Die Migration erfordert die Rekonstruktion der Ressourcen-Pools, darunter Reservierungen, Freigaben und vApps. Dies gilt auch für vApps und alle in vApps definierten Startupkonfigurationen.

Weitere Informationen finden Sie in der [VMware Dokumentation zur Verwaltung von Ressourcen-Pools](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.resmgmt.doc/GUID-60077B40-66FF-4625-934A-641703ED7601.html){.external}.

Hier eine Liste der zu berücksichtigenden Elemente:

- CPU / Memory Freigaben
- CPU / Memory Reservierungen
- CPU / Memory Skalieroptionen
- CPU / Memory Limits

**Automatisierungshinweise:**
Das Powercli Cmdlet "Get-ResourcePool" sammelt die Ressourcenpool-Informationen und das Cmdlet "New-ResourcePool" erstellt den Ressourcenpool am Ziel neu. 

##### **Datastore-Cluster**

Wenn Datastore-Cluster in der Quellinfrastruktur vorhanden sind, kann es zur Migration notwendig sein, diese Datastore-Cluster auf dem Ziel-Managed Bare Metal neu zu erstellen, wenn die gleiche Struktur und SDRS erforderlich sind.

Hier eine Liste der zu berücksichtigenden Elemente:

- SDRS Automatisierungslevel
- SDRS I/O, Bereich, Regel, Policy, Evakuierungsparameter für VMs
- SDRS Affinitäts- oder Anti-Affinitätsregeln
- SDRS VM Overrides

**Automatisierungshinweise:**
Das Powercli Cmdlet "Get-Cluster" gibt Informationen zu HA- und DRS-Konfigurationseinstellungen zurück, die mit dem Cmdlet "Set-Cluster" auf dem Zielcluster angewendet werden können.

##### **Netzwerke**

Die Migration beinhaltet die Neueinrichtung der virtuellen Portgruppen von vRack auf dem Ziel-Managed Bare Metal, um die Kohärenz von VMnetwork zu gewährleisten. Werden vRack VLANs auf dem vRack der Quellinfrastruktur verwendet, kann mittels vRack die L2-Domain auf das Ziel-Managed Bare Metal erweitert werden, um einen gestuften Migrationsplan zu ermöglichen.

Weitere Informationen finden Sie in unserer Anleitung zur Verwendung von [Managed Bare Metal in einem vRack](../vrack-essentials/).

Hier eine Liste der zu berücksichtigenden Elemente:

- VLAN-Typ der Portgroups
- Sicherheitseinstellungen
- Teaming- und Failover-Einstellungen
- Zuweisung der Netzwerkressourcen des Kunden

Weitere Informationen finden Sie in der Dokumentation von VMware zu

Weitere Informationen finden Sie in der VMware Dokumentation zum [Bearbeiten der Portgruppen](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.networking.doc/GUID-FCA2AE5E-83D7-4FEE-8DFF-540BDB559363.html) sowie zum [Konfigurieren von Teaming und Failover](https://docs.vmware.com/en/VMware-vSphere/6.0/com.vmware.vsphere.hostclient.doc/GUID-BB8EC262-5F85-4F42-AFC5-5FED456E2C11.html).

**Automatisierungshinweise:** Das Powercli-Cmdlet "Export-VDPortGroup" kann Informationen zu verteilten virtuellen Portgruppen abrufen, die dann mit dem Cmdlet  "New-VDPortgroup -BackupPath" in den Ziel-Distributed Switch importiert werden können.

##### **OVHcloud Veeam Backup**

Wenn die von OVHcloud zur Verfügung gestellte Veeam-Anwendung derzeit verwendet wird, um die VMs auf der Quellinfrastruktur zu sichern, muss die Backup-Konfiguration für die VMs nach der Migration im Ziel-Managed Bare Metal neu erstellt werden.

Hier eine Liste der zu berücksichtigenden Elemente:

- Liste der zu sichernden VMs
- Backup-Einstellungen

Lesen Sie auch unsere Anleitung zur [Verwendung von Veeam Managed Backup](https://docs.ovh.com/gb/en/managed-bare-metal/veeam-backup-as-a-service/).

**Automatisierungshinweise:** Die OVHcloud API liefert Informationen zu jedem VM-Backup über:

> [!api]
>
> @api {GET} /dedicatedCloud/{serviceName}/datacenter/{datacenterId}/vm/{vmId}
>

Der Abschnitt "Backup" in der zurückgegebenen json-Datei enthält Informationen zur aktuellen Backup-Konfiguration.

#### Organisation des Inventars

Aus organisatorischen Gründen können VMs, Hosts oder Datastores in Ordnern angelegt worden sein.

Wenn diese Organisation weiterhin notwendig ist, muss sie erneut im Ziel-Managed Bare Metal erstellt werden.

**Automatisierungshinweise:** Verwenden Sie das Powercli Cmdlet "Get-Folder", um Informationen zu erhalten und "New-Folder", um den Ordner auf dem Ziel-Managed Bare Metal wiederherzustellen.

#### VM

Es gibt mehrere Möglichkeiten, die VMs von einer Infrastruktur auf eine andere zu migrieren. Wir bieten Ihnen an, die Migration mit Veeam sowie mittels Veeam Replication durchzuführen.

Hierzu sind folgende Elemente erforderlich:

- SPLA-Lizenzen (auf der Quellinfrastruktur und dem Ziel Managed Bare Metal)
- eine Veeam-Lizenz
- eine auf der Quellinfrastruktur und dem Ziel-Managed Bare Metal verfügbare IP-Adresse
- eine [Veeam Backup & Replication](../../storage/veeam-backup-replication/) VM auf der Quellinfrastruktur
- [Authorisierung der Veeam Backup & Replication VM](../verbindung-von-ip-zum-vcenter-erlauben/), sich mit der Quellinfrastruktur und dem Ziel-vCenter zu verbinden.

Lesen Sie auch unsere Anleitung zum [Einrichten von Veeam Backup & Replication](../../storage/veeam-backup-replication/).

Im folgenden Video erfahren Sie, wie Sie eine Hosted Private Cloud mit der Veeam Backup & Replication Lösung konfigurieren. Diese Methode gilt auch für Managed Bare Metal Lösungen.

<iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/f8ufrsP4PQw" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
<br>

Im folgenden Video erfahren Sie, wie Sie Ihre Hosted Private Cloud Infrastruktur mit der Veeam Backup & Replication Lösung replizieren. Diese Methode gilt auch für eine Managed Bare Metal Lösung.

<iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/NqNtKrJSH8w" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
<br>

Sie können auch die [Veeam-Dokumentation](https://www.veeam.com/veeam_backup_10_0_user_guide_vsphere_pg.pdf){.external} verwenden.


## Weiterführende Informationen

Für den Austausch mit unserer User Community gehen Sie auf <https://community.ovh.com/en/>.
