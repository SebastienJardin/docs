---
title: Eine Infrastruktur auf ein neues vDC migrieren
excerpt: Hier erfahren Sie, wie Sie alle Aspekte der Migration von einer Infrastruktur auf ein vDC verwalten.
slug: vdc-migration
section: Erste Schritte
order: 6
hidden: true
---

> [!primary]
> Diese Übersetzung wurde durch unseren Partner SYSTRAN automatisch erstellt. In manchen Fällen können ungenaue Formulierungen verwendet worden sein, z.B. bei der Beschriftung von Schaltflächen oder technischen Details. Bitte ziehen Sie beim geringsten Zweifel die englische oder französische Fassung der Anleitung zu Rate. Möchten Sie mithelfen, diese Übersetzung zu verbessern? Dann nutzen Sie dazu bitte den Button «Mitmachen» auf dieser Seite.
>

**LLetzte Aktualisierung am 16.12.2020**

> [!warning]
>
> Der vDC-Migrationsweg ist noch nicht verfügbar, da Updates und Wartungsarbeiten laufen. Wir informieren Sie, sobald diese Migration möglich ist.
>

## Ziel

Die Migration einer Infrastruktur auf ein neues vDC hat zwei Aspekte:

- Die OVHcloud Infrastruktur selbst, die die Kundenseite der Verwaltung einer Infrastruktur umfasst.
- Die VMware Infrastruktur, die das VMware Ökosystem beinhaltet.

**In dieser Anleitung erfahren Sie, wie Sie alle Aspekte der Migration von einer bereits bestehenden OVHcloud-Infrastruktur auf ein neues vDC abdecken.**

## Voraussetzungen

- Sie haben Zugriff auf Ihr [OVHcloud Kundencenter](https://www.ovh.com/auth/?action=gotomanager&from=https://www.ovh.de/&ovhSubsidiary=de), Bereich `Bare Metal Cloud`{.action} und `Managed Bare Metal`{.action}.
- Sie verfügen über eine PCC Infrastruktur

## In der praktischen Anwendung

Diese Anleitung verwendet nachfolgend die Bezeichnungen **Quellinfrastruktur** und **Ziel-vDC**.

### OVHcloud Kontext

#### Sicherheit

##### **Managed Bare Metal Zugriff**

Um sich mit der VMware-Plattform zu verbinden, können Sie standardmäßig den Zugriff auf vSphere blockieren. Lesen Sie hierzu unsere Anleitung bezüglich  [Zugriffseinstellungen für vCenter](../zugriffsregeln-fuer-vcenter-aendern/).

Wenn die Zugangseinstellungen auf "Eingeschränkt" gesetzt wurden, müssen die gleichen Login-IPs für die Ziel-vDC wie für die Quellinfrastruktur freigegeben werden.

##### **Managed Bare Metal Benutzer**

Im Lebenszyklus der ursprünglichen Infrastruktur kann eine Nutzerliste für betriebliche oder organisatorische Erfordernisse erstellt worden sein. Diese müssen erneut auf der Ziel-vDC angelegt werden. Sie müssen den Benutzern dann entsprechend der Konfiguration des Ziel-vDC die angemessenen Rechte zuweisen.

Lesen Sie hierzu unsere Anleitungen zu [Änderung von Benutzerrechten](../die-rechte-eines-nutzers-aendern/), [Passwortänderung](../aenderung-des-nutzerpassworts/) und [Zuweisung von E-Mail](../e-mail-mit-nutzerprofil-verbinden/).

##### **Key Management Server (KMS)**

Wenn virtuelle Maschinen mit Verschlüsselung geschützt sind und dies eine Voraussetzung für das Ziel-vDC ist, muss der Verschlüsselungskontext dort neu erstellt werden.

Lesen Sie unsere Anleitung zur [Aktivierung der Verschlüsselung von virtuellen Maschinen](../vm-encrypt/), um KMS für das Ziel-vDC zu aktivieren.

#### Netzwerk

##### **vRack**

> [!warning]
>
> VMnetworks aus derselben Region können nicht in einem vRack untereinander verbunden werden.
>

Im Rahmen einer Migration können Sie Ihre Managed Bare Metal Dienste mit demselben vRack verbinden. Lesen Sie unsere Anleitung zur [Verwendung von Managed Bare Metal in einem vRack](../vrack-essentials/).

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

Wenn Datastore-Cluster in der Quellinfrastruktur vorhanden sind, kann es zur Migration notwendig sein, diese Datastore-Cluster auf dem Ziel-vDC neu zu erstellen, wenn die gleiche Struktur und SDRS erforderlich sind.

Hier eine Liste der zu berücksichtigenden Elemente:

- SDRS Automatisierungslevel
- SDRS I/O, Bereich, Regel, Policy, Evakuierungsparameter für VMs
- SDRS Affinitäts- oder Anti-Affinitätsregeln
- SDRS VM Overrides

##### **Netzwerke**

Die Migration beinhaltet die Neueinrichtung der virtuellen Portgruppen von vRack auf dem Ziel-vDC, um die Kohärenz von VMnetwork zu gewährleisten. Werden vRack VLANs auf dem vRack der Quellinfrastruktur verwendet, kann mittels vRack die L2-Domain auf das Ziel-vDC erweitert werden, um einen gestuften Migrationsplan zu ermöglichen.

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

Wenn die von OVHcloud zur Verfügung gestellte Veeam-Anwendung derzeit verwendet wird, um die VMs auf der Quellinfrastruktur zu sichern, muss die Backup-Konfiguration für die VMs nach der Migration im Ziel-vDC neu erstellt werden.

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

Wenn diese Organisation weiterhin notwendig ist, muss sie erneut im Ziel-vDC erstellt werden.

#### VM

Es gibt mehrere Methoden, um VMs auf ein neues vDC zu migrieren.

Wenn sich Ihre Quelle auf Intel Hosts befindet, können Sie die VMs mit vMotion auf das neue vDC migrieren.

Wenn Ihre Quelle sich auf AMD Hosts befindet, schalten Sie diese aus und verwenden Sie die Cold Migration.

## Weiterführende Informationen

Für den Austausch mit unserer Community gehen Sie auf <https://community.ovh.com/en/>.
