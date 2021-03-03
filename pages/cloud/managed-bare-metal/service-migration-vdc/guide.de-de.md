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

**Letzte Aktualisierung am 16\. Dezember 2020**

> [!warning]
>
> vDC-Migrationsweg ist noch nicht verfügbar, da Updates und Wartungsarbeiten laufen. Wir informieren Sie, sobald diese Migration möglich ist.
>

## Ziel

Die Migration einer Infrastruktur auf ein neues vDC hat zwei Aspekte:

* OVHcloud Managed Bare Metal Kontext, der den Kunden und die Administration Ihrer Infrastruktur umfasst.
* Der VMware Kontext, der das gesamte VMware Ökosystem umfasst, auf der Infrastruktur selbst.

**In dieser Anleitung erfahren Sie, wie Sie alle Aspekte der Migration von einer bereits bestehenden OVHcloud-Infrastruktur auf ein neues vDC abdecken.**

## Voraussetzungen

* Eine Dedicated Cloud Infrastruktur
* Sie sind in Ihrem [OVHcloud](https://www.ovh.com/auth/?action=gotomanager&from=https://www.ovh.de/&ovhSubsidiary=de){.external} Kundencenter eingeloggt und befinden sich im `Bereich Bare Metal Cloud`{.action} und `Managed Bare Metal`{.action}.

## In der praktischen Anwendung

In dieser Anleitung werden die **Begriffe "Infrastruktur"** und "**vDC"verwendet**.

### OVHcloud Kontext

#### Sicherheit

##### **Kontext für die Verbindung zu Managed Bare Metal**

Um sich mit der VMware-Plattform zu verbinden, können Sie standardmäßig den Zugriff auf vSphere blockieren. Lesen Sie hierzu unsere Anleitung [zur Zugriffseinstellungen für vCenter](../zugriffsregeln-fuer-vcenter-aendern/).

Nach der Änderung der Zugangseinstellungen, wenn diese "eingeschränkt"wurde, müssen natürlich die gleichen Login-IPs auf das Ziel-vDC wie auf die ursprüngliche Infrastruktur angewandt werden.

##### **Benutzer von Managed Bare Metal**

Im Lebenszyklus der ursprünglichen Infrastruktur kann eine Nutzerliste für Geschäftszwecke oder organisatorische Erfordernisse erstellt worden sein. Erstellen Sie diese entsprechend der Konfiguration des Ziel-vDC erneut und weisen Sie ihnen die entsprechenden Rechte zu.

Lesen Sie hierzu unsere Anleitungen, [um die Rechte eines Benutzers](../die-rechte-eines-nutzers-aendern/) zu ändern, [das Passwort eines Benutzers zu ändern und ](../aenderung-des-nutzerpassworts/)einem Benutzer eine E-Mail zuzuweisen[ ](../e-mail-mit-nutzerprofil-verbinden/).

##### **Key Management Server (KMS)**

Wenn virtuelle Maschinen durch Verschlüsselung geschützt sind und dies eine Voraussetzung für das Ziel-vDC ist, muss der Verschlüsselungskontext auf dem Ziel neu erstellt werden.

Lesen Sie unsere Anleitung zur [Aktivierung der Verschlüsselung von virtuellen Maschinen](../vm-encrypt/), um das KMS auf dem Ziel-vDC zu aktivieren.

#### Netzwerk

##### **vRack**

> [!warning]
>
> VMnetwork aus derselben Region können nicht in einem vRack untereinander verbunden werden.
>

Im Rahmen einer Migration können Sie Ihre Managed Bare Metal Dienste mit demselben vRack verbinden. Lesen Sie unsere [Anleitung zur Verwendung der Private Cloud in einem vRack](../vrack-essentials/).

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

**Automatisierungsanleitungen:** Powercli "Get-ResourcePool" Powercli-Bestellapplet, um die Informationen aus der Liste geteilter Ressourcen zu sammeln, und das New-ResourcePool Bestellapplet, um die Liste geteilter Ressourcen im Ziel-vDC neu zu erstellen.

##### **Datastore-Cluster**

Wenn Datastore-Cluster in der ursprünglichen Infrastruktur vorhanden sind, kann es zur Migration notwendig sein, diese Datastore-Cluster auf dem Ziel-vDC neu zu erstellen, wenn die gleiche Struktur und das SDRS erforderlich sind.

Hier eine Liste der zu berücksichtigenden Elemente:

- SDRS Automatisierungslevel
- SDRS, I/O Bereich, Regel, Strategie, Evakuierungsparameter für VMs
- SDRS Affinitätsregeln
- Änderungen der SDRS VMs

##### **Netzwerke**

Die Migration beinhaltet die Reproduktion der virtuellen Portgruppen von vRack auf das Ziel-vDC, um die Konsistenz des VMs-Netzwerks zu gewährleisten. Werden vRack VLANs auf dem vRack der ursprünglichen Infrastruktur verwendet, können sie verwendet werden, um die Domain L2 auf das Ziel vDC zu erweitern, um einen gestuften Migrationsplan zu ermöglichen. Weitere Informationen finden Sie in unserer Anleitung zur Verwendung der [Private Cloud in einem vRack](../vrack-essentials/).

Hier eine Liste der zu berücksichtigenden Elemente:

- Typ des VLAN der Portgroups
- Sicherheitseinstellungen
- Teaming und Failover Einstellungen
- Zuweisung der Netzwerkressourcen des Kunden

Weitere Informationen finden Sie in der Dokumentation von VMware:
<br>- >- [Teaming & Failover](https://docs.vmware.com/en/VMware-vSphere/6.0/com.vmware.vsphere.hostclient.doc/GUID-BB8EC262-5F85-4F42-AFC5-5FED456E2C11.html)

**Automatisierungsanleitungen:** Das Powercli-Bestellapplet "Export-VDPortGroup" kann verteilte virtuelle Portgroups-Informationen abrufen, die dann mit dem "New-VDPortgroup -BackupPath"-Bestellapplet in den Ziel-Distributed Switch importiert werden können.

##### **OVHcloud Veeam Backup**

Wenn die von OVHcloud bereitgestellte Veeam-Anwendung derzeit verwendet wird, um die VMs auf der ursprünglichen Infrastruktur zu sichern, muss die Backup-Konfiguration für die VMs nach der Migration im Ziel-vDC neu erstellt werden.

Hier eine Liste der zu berücksichtigenden Elemente:

- Liste der VMs wird gesichert
- Backup-Einstellungen

Lesen Sie unsere Anleitung, [um Veeam Managed Backup zu aktivieren und zu verwenden](https://docs.ovh.com/gb/en/managed-bare-metal/veeam-backup-as-a-service/).

**Automatisierungsanleitungen:** Die OVHcloud API liefert Informationen zu jedem VM-Backup über:

> [!api]
>
> @api {GET} /dedicatedCloud/{serviceName}/datacenter/{datacenterId}/vm/{vmId}
>

Der Abschnitt "Backup"der zurückgegebenen json-Datei enthält Informationen zur aktuellen Backup-Konfiguration.

#### Logistische Organisation

Aus organisatorischen Gründen können virtuelle Maschinen, Hosts oder Datastores in Ordnern angelegt worden sein.

Wenn diese Organisation immer notwendig ist, müssen Sie sie im Ziel-vDC neu erstellen.

#### VM

Es gibt mehrere Methoden, um VMs auf ein neues vDC zu migrieren.

Wenn sich Ihre Quelle auf Intel-Hosts befindet, können Sie die VMs mit vMotion auf das neue VDC migrieren.

Wenn Ihre Quelle sich auf AMD Hosts befindet, schließen Sie diese aus und verwenden Sie die Cold Migration.

## Weiterführende Informationen

Kommen Sie zu unserer User Community auf dem <https://community.ovh.com/en/>
