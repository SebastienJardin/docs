---
title: Przeniesienie infrastruktury do Managed Bare Metal
excerpt: Dowiedz się, jak zarządzać wszystkimi aspektami migracji infrastruktury do rozwiązania Managed Bare Metal
slug: sddc-migration
section: Pierwsze kroki
order: 5
---

> [!primary]
> Tłumaczenie zostało wygenerowane automatycznie przez system naszego partnera SYSTRAN. W niektórych przypadkach mogą wystąpić nieprecyzyjne sformułowania, na przykład w tłumaczeniu nazw przycisków lub szczegółów technicznych. W przypadku jakichkolwiek wątpliwości zalecamy zapoznanie się z angielską/francuską wersją przewodnika. Jeśli chcesz przyczynić się do ulepszenia tłumaczenia, kliknij przycisk „Zaproponuj zmianę” na tej stronie.
> 

**Ostatnia aktualizacja z dnia 24-11-2020**

## Wprowadzenie

Migracja usługi Managed Bare Metal obejmuje dwa aspekty:

* Kontekst Managed Bare Metal OVHcloud, w tym obsługa klienta.
* Kontekst VMware, który obejmuje cały eco-system VMware, w samej infrastrukturze.

**Dowiedz się, jak włączyć wszystkie aspekty związane z migracją do usługi Managed Bare Metal**

> [!primary]
>
> Jeśli zdecydujesz się na przeniesienie infrastruktury do nowej sieci vDC, zapoznaj się z [tym przewodnikiem](https://docs.ovh.com/gb/en/managed-bare-metal/vdc-migration/).
>

## Wymagania początkowe

* Dostęp do [Panelu klienta OVHcloud](https://www.ovh.com/auth/?action=gotomanager&from=https://www.ovh.pl/&ovhSubsidiary=pl){.external} w sekcji `Bare Metal Cloud`{.action}, a następnie `Managed Bare Metal`{.action}.
* Posiadanie produktu [Managed Bare Metal](https://www.ovhcloud.com/pl/managed-bare-metal/){.external}.

## W praktyce

W tym przewodniku omówimy pojęcia **infrastruktury** i docelowej **sieci Bare Metal**.

### Kontekst Managed Bare Metal

#### Bezpieczeństwo

##### **Kontekst logowania do panelu Managed Bare Metal**

Aby zalogować się do platformy VMware, możesz zablokować domyślny dostęp do vSphere. W tym celu zapoznaj się z naszym przewodnikiem dotyczącym [polityki dostępu do vCenter](../zmiana-polityki-dostepu-do-vcenter/).

W związku ze zmianą polityki dostępu, jeśli została ona "ograniczona", należy oczywiście zastosować te same adresy IP połączenia na docelowym Managed Bare Metal, co w infrastrukturze źródłowej.

##### **Użytkownicy Managed Bare Metal**

W ramach cyklu życia pierwotnej infrastruktury, lista użytkowników może zostać stworzona dla potrzeb biznesowych lub organizacyjnych. W związku z tym należy je utworzyć ponownie na docelowym Managed Bare Metal i przypisać im odpowiednie uprawnienia, w zależności od konfiguracji docelowego Managed Bare Metal.

W tym celu zapoznaj się z naszymi przewodnikami, które [zmienią uprawnienia użytkownika](../zmiana-uprawnien-uzytkownika/), [zmienią hasło użytkownika](../aktualizacja-hasla-uzytkownika/) i [przypiszą e-mail użytkownikowi](../powiazanie-adresu-e-mail-z-uzytkownikiem/).

##### **KMS**

Jeśli wirtualne maszyny są chronione szyfrowaniem i jest to warunek wstępny dla docelowego Managed Bare Metal, konieczne będzie odtworzenie sytuacji szyfrowania.

Zapoznaj się z naszym przewodnikiem dotyczącym [aktywacji szyfrowania wirtualnych](../szyfrowanie-vm/) maszyn, aby włączyć KMS w docelowym Managed Bare Metal.

#### Sieć

##### **vRack**

> [!warning]
>
> VMnetwork zlokalizowane w tym samym regionie nie będą mogły być podłączone do sieci vRack.
>

W ramach migracji możesz połączyć usługi Managed Bare Metal w ramach tej samej sieci vRack. Zapoznaj się z naszym przewodnikiem dotyczącym [korzystania z Private Cloud w ramach usługi vRack](../vrack-essentials/).

##### **Sieć publiczna**

> [!warning]
>
> Jeśli Twoja oferta Private Cloud/PCC jest wcześniejsza niż 2016, skontaktuj się z zespołem pomocy, aby sprawdzić, jakie wymagania są niezbędne.
>

Jeśli publiczne adresy IP przypisane do infrastruktury źródłowej są niezbędne do docelowego Managed Bare Metal, konieczne będzie ich przeniesienie.

Zapoznaj się z naszym przewodnikiem dotyczącym [migracji bloków IP między dwoma usługami Managed Bare Metal](../dodawanie-bloku-ip/#przenoszenie-bloku-ip-miedzy-dwiema-uslugami-managed-bare-metal).

W poniższym filmie dowiesz się, jak przenieść blok adresów IP między dwoma usługami Hosted Private Cloud. Metoda ta ma również zastosowanie do rozwiązania Managed Bare Metal.

<iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/Gemao3Fd7rI" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

### Kontekst VMware

#### Konfiguracja klastrów

##### **Konfiguracja VMware HA**

Migracja wymaga zmiany konfiguracji VMware High Availability (HA), w tym kolejności i priorytetu uruchamiania. Zapoznaj się [z przewodnikiem dotyczącym konfiguracji](../vmware-ha-high-availability/) usługi.

Poniżej lista elementów, które należy wziąć pod uwagę:

- Ustawienia monitoringu hosta
- Regulacje monitoringu VM
- Kontrola dostępu
- Zaawansowane opcje HA
- Wymiana VM

**Wskazówki dotyczące automatyzacji:**
Aplikacja poleceń Powercli "Get-Cluster" zawiera informacje o parametrach konfiguracji HA i DRS, które mogą być zastosowane do klastra docelowego za pomocą apletu poleceń "Set-Cluster".

##### **Konfiguracja VMware DRS**

Migracja wiąże się z rekonfiguracją funkcji VMware DRS (Distributed Resource Scheduler), w szczególności z regułami afiliacji lub anty-powinowactwa dla grup hostów i wirtualnych maszyn. Zapoznaj się z naszym przewodnikiem dotyczącym [konfiguracji VMware DRS](../vmware-drs-distributed-ressource-scheduler-new/).

Poniżej lista elementów, które należy wziąć pod uwagę:

- Poziom automatyzacji
- Grupy VM / hostów
- Reguły powinowactwa / blokady powinowactwa wirtualnych maszyn / hostów
- Wymiana wirtualnych maszyn

**Wskazówki dotyczące automatyzacji:** [Ten wątek dyskusyjny społeczności VMware](https://communities.vmware.com/t5/VMware-PowerCLI-Discussions/Backup-Restore-DRS-VM-affinity-anti-affinity-rules-can-these-be/td-p/733981/page/2) zawiera szczegółowe informacje na temat możliwości eksportu i importu reguł powinowactwa za pośrednictwem powercli.

##### **Pule zasobów**

Migracja wymaga rekonstrukcji puli zasobów, w tym rezerwacji, współdzielenia i wirtualnych aplikacji. Dotyczy to również vApps i każdej konfiguracji uruchamiania określonej w vApps.

Więcej informacji znajdziesz w dokumentacji [VMware służącej do zarządzania pulami zasobów](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.resmgmt.doc/GUID-60077B40-66FF-4625-934A-641703ED7601.html){.external}.

Poniżej lista elementów, które należy wziąć pod uwagę:

- Parametry zasobów współdzielonych CPU/pamięci
- Rezerwacja CPU/pamięci
- Skalowalna opcja CPU/Memory
- Limity CPU/pamięci

**Wskazówki dotyczące automatyzacji:** Aplikacja poleceń Powercli "Get-ResourcePool" umożliwia zgromadzenie informacji z listy współdzielonych zasobów oraz apletu poleceń "New-ResourcePool", aby odtworzyć listę współdzielonych zasobów w docelowym Managed Bare Metal.

##### **Klastry datastore**

Jeśli klastry datastore są obecne w pierwotnej infrastrukturze, migracja może wymagać regeneracji tych klastrów datastore na docelowym Managed Bare Metal, jeśli wymagany jest ten sam poziom struktury i SDRS.

Poniżej lista elementów, które należy wziąć pod uwagę:

- Poziom automatyzacji SDRS
- Przestrzeń SDRS, I/O, reguła, strategia, parametry ewakuacji wirtualnych maszyn
- Reguły powinowactwa/anty-powinowactwa SDRS
- Zmiany dla VM SDRS

##### **Sieć**

Migracja wiąże się z utworzeniem grup wirtualnych portów vRack na docelowym Managed Bare Metal, aby zapewnić spójność sieci VM. Jeśli w sieci vRack wykorzystywane są sieci vRack Oryginalnej infrastruktury, mogą być one wykorzystywane do rozszerzenia domeny L2 na docelowy Managed Bare Metal, aby umożliwić bardziej rozłożony plan migracji. Więcej informacji znajdziesz w przewodniku Korzystanie z [prywatnej chmury w sieci vRack](../vrack-essentials/).

Poniżej lista elementów, które należy wziąć pod uwagę:

- Typ sieci VLAN dla portgroups
- Paramètres de sécurité
- Parametry Teaming i Failover
- Przydział zasobów sieciowych klienta

Więcej informacji znajdziesz w dokumentacji VMware:
<br>- >- [Teaming & Failover](https://docs.vmware.com/en/VMware-vSphere/6.0/com.vmware.vsphere.hostclient.doc/GUID-BB8EC262-5F85-4F42-AFC5-5FED456E2C11.html)

**Wskazówki dotyczące automatyzacji:** Aplikacja poleceń Powercli "Export-VDPortGroup" może pobierać informacje o rozproszonych wirtualnych portgroups, które mogą być następnie importowane do docelowego Distributed Switch za pomocą apletu poleceń "New-VDPortgroup -BackupPath".

##### **OVHcloud Veeam Backup**

Jeśli aplikacja Veeam dostarczana przez OVHcloud jest aktualnie używana do tworzenia kopii zapasowych wirtualnych maszyn w oryginalnej infrastrukturze, po przeprowadzeniu migracji konieczne będzie odtworzenie konfiguracji kopii zapasowej wirtualnych maszyn w docelowym Managed Bare Metal.

Poniżej lista elementów, które należy wziąć pod uwagę:

- Lista maszyn w trakcie tworzenia kopii zapasowej
- Parametry kopii zapasowej

Zapoznaj się z naszym przewodnikiem dotyczącym [aktywacji i korzystania z usługi Veeam Backup Managed](https://docs.ovh.com/gb/en/managed-bare-metal/veeam-backup-as-a-service/).

**Wskazówki dotyczące automatyzacji:** API OVHcloud dostarcza informacje związane z każdą kopią zapasową VM poprzez:

> \[!api]
>
> @api {GET} /dedicatedCloud/{serviceName}/datacenter/{datacenterId}/vm/{vmId}
>

Sekcja "kopii zapasowej" zwróconego pliku json zawiera informacje na temat aktualnej konfiguracji kopii zapasowej.

#### Organizacja inwentaryzacji

Ze względów organizacyjnych wirtualne maszyny, hosty lub datastore mogą być umieszczane w katalogach.

Jeśli ta organizacja jest nadal konieczne, trzeba będzie utworzyć ją ponownie w docelowym Managed Bare Metal.

**Wskazówki dotyczące automatyzacji:** Użyj aplikacji poleceń Powercli "Get-Folder", aby zebrać informacje o folderze i aplecie poleceń "New-Folder", aby odtworzyć folder na docelowym Managed Bare Metal.

#### VM

Istnieje kilka sposobów migracji wirtualnych maszyn z jednej infrastruktury na inną. Możesz przeprowadzić migrację za pomocą rozwiązania Veeam i Veeam Replication.

Konieczne są następujące elementy:

* licencje SPLA (na podstawowej infrastrukturze i docelowym Managed Bare Metal);
* licencji Veeam;
* adres IP dostępny w infrastrukturze źródłowej oraz docelowym Managed Bare Metal;
* wirtualnej maszyny [Veeam Backup & Replication](../../storage/veeam-backup-replication/) w oryginalnej infrastrukturze;
* [Zezwól wirtualnej maszynie Veeam Backup & Replication na połączenie](../autoryzacja-IP-ktore-moga-laczyc-sie-z-vCenter/) z oryginalnym i docelowym vCenter.

Zapoznaj się z naszym przewodnikiem dotyczącym [uruchomienia Veeam Backup & Replication](../../storage/veeam-backup-replication/).

W poniższym filmie szczegółowo opisano, jak skonfigurować Hosted Private Cloud za pomocą rozwiązania Veeam Backup & Replication. Metoda ta ma również zastosowanie do rozwiązania Managed Bare Metal.

<iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/f8ufrsP4PQw" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>


<br>W poniższym filmie szczegółowo omówiono, jak przeprowadzić replikację Twojej infrastruktury Hosted Private Cloud przy użyciu rozwiązania Veeam Backup & Replication. Ten tutorial dotyczy również rozwiązania Managed Bare Metal.

<iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/NqNtKrJSH8w" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>


<br>Zapoznaj się również [z dokumentacją Veeam](https://www.veeam.com/veeam_backup_10_0_user_guide_vsphere_pg.pdf){.external}.


## Sprawdź również

Dołącz do społeczności naszych użytkowników na stronie<https://community.ovh.com/en/>.
