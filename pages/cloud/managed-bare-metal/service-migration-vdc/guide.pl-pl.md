---
title: Migracja infrastruktury do nowej usługi vDC
excerpt: Dowiedz się, jak zarządzać wszystkimi aspektami migracji infrastruktury do vDC
slug: vdc-migration
section: Pierwsze kroki
order: 6
hidden: true
---

> [!primary]
> Tłumaczenie zostało wygenerowane automatycznie przez system naszego partnera SYSTRAN. W niektórych przypadkach mogą wystąpić nieprecyzyjne sformułowania, na przykład w tłumaczeniu nazw przycisków lub szczegółów technicznych. W przypadku jakichkolwiek wątpliwości zalecamy zapoznanie się z angielską/francuską wersją przewodnika. Jeśli chcesz przyczynić się do ulepszenia tłumaczenia, kliknij przycisk „Zaproponuj zmianę” na tej stronie.
> 

**Ostatnia aktualizacja z dnia 16 grudnia 2020 r**

> [!warning]
>
> Ścieżka migracji vDC nie jest jeszcze dostępna, ponieważ trwają aktualizacje i prace konserwacyjne. Ostrzegamy, jak tylko migracja będzie możliwa.
>

## Wprowadzenie

Migracja infrastruktury do nowej usługi vDC wiąże się z dwoma aspektami:

* Kontekst Managed Bare Metal OVHcloud, w tym obsługa klienta.
* Kontekst VMware, który obejmuje cały eco-system VMware, w samej infrastrukturze.

**Niniejszy przewodnik wyjaśnia, jak włączyć wszystkie aspekty migracji istniejącej infrastruktury OVHcloud do nowej sieci vDC.**

## Wymagania początkowe

* Infrastruktura Dedicated Cloud
* Dostęp do [Panelu klienta OVHcloud](https://www.ovh.com/auth/?action=gotomanager&from=https://www.ovh.pl/&ovhSubsidiary=pl){.external} w sekcji `Bare Metal Cloud`{.action}, a następnie `Managed Bare Metal`{.action}.

## W praktyce

Niniejszy przewodnik wykorzystuje pojęcia **infrastruktury docelowej** i docelowej** vDC**.

### Kontekst OVHcloud

#### Bezpieczeństwo

##### **Kontekst logowania do panelu Managed Bare Metal**

Aby zalogować się do platformy VMware, możesz zablokować domyślny dostęp do vSphere. W tym celu zapoznaj się z naszym przewodnikiem dotyczącym [polityki dostępu do vCenter](../zmiana-polityki-dostepu-do-vcenter/).

W związku ze zmianą polityki dostępu, jeśli została ona przekształcona w "ograniczony", należy oczywiście zastosować te same IP połączenia dla docelowego vDC, co dla pierwotnej infrastruktury.

##### **Użytkownicy Managed Bare Metal**

W ramach cyklu życia pierwotnej infrastruktury, lista użytkowników może zostać stworzona dla potrzeb biznesowych lub organizacyjnych. W związku z tym należy utworzyć je ponownie w docelowym vDC i przypisać im odpowiednie uprawnienia, w zależności od konfiguracji docelowego vDC.

W tym celu zapoznaj się z naszymi przewodnikami, które [zmienią uprawnienia użytkownika](../zmiana-uprawnien-uzytkownika/), [zmienią hasło użytkownika](../aktualizacja-hasla-uzytkownika/) i [przypiszą e-mail użytkownikowi](../powiazanie-adresu-e-mail-z-uzytkownikiem/).

##### **Key Management Server (KMS)**

Jeśli wirtualne maszyny są chronione szyfrowaniem i jest to warunek wstępny docelowego vDC, konieczne będzie odtworzenie sytuacji szyfrowania.

Zapoznaj się z naszym przewodnikiem dotyczącym [aktywacji szyfrowania wirtualnych](../szyfrowanie-vm/) maszyn, aby włączyć KMS w docelowym vDC.

#### Sieć

##### **vRack**

> [!warning]
>
> VMnetwork zlokalizowane w tym samym regionie nie będą mogły być podłączone do sieci vRack.
>

W ramach migracji możesz połączyć usługi Managed Bare Metal w ramach tej samej sieci vRack. Zapoznaj się z naszym przewodnikiem dotyczącym [korzystania z Private Cloud w ramach usługi vRack](../vrack-essentials/).

### Kontekst VMware

#### Konfiguracja klastra

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

**Wskazówki dotyczące automatyzacji:** Aplikacja poleceń Powercli "Get-ResourcePool" umożliwia zgromadzenie informacji z listy współdzielonych zasobów oraz apletu poleceń "New-ResourcePool", aby odtworzyć listę współdzielonych zasobów w docelowym vDC.

##### **Klastry datastore**

Jeśli klastry datastore są obecne w pierwotnej infrastrukturze, migracja może wymagać odtworzenia tych klastrów datastore w docelowym vDC, jeśli wymagany jest ten sam poziom struktury i SDRS.

Poniżej lista elementów, które należy wziąć pod uwagę:

- Poziom automatyzacji SDRS
- Przestrzeń SDRS, I/O, reguła, strategia, parametry ewakuacji wirtualnych maszyn
- Reguły powinowactwa/anty-powinowactwa SDRS
- Zmiany dla VM SDRS

##### **Sieć**

Migracja wiąże się z utworzeniem grup wirtualnych portów vRack na docelowym vDC, aby zapewnić spójność sieci VM. Jeśli w sieci vRack wykorzystywane są sieci vRack Oryginalnej infrastruktury vRack, mogą one zostać użyte do rozszerzenia domeny L2 na docelową vDC, aby umożliwić bardziej rozłożony plan migracji. Więcej informacji znajdziesz w przewodniku Korzystanie z [prywatnej chmury w sieci vRack](../vrack-essentials/).

Poniżej lista elementów, które należy wziąć pod uwagę:

- Typ sieci VLAN dla portgroups
- Paramètres de sécurité
- Parametry Teaming i Failover
- Przydział zasobów sieciowych klienta

Więcej informacji znajdziesz w dokumentacji VMware:
<br>- >- [Teaming & Failover](https://docs.vmware.com/en/VMware-vSphere/6.0/com.vmware.vsphere.hostclient.doc/GUID-BB8EC262-5F85-4F42-AFC5-5FED456E2C11.html)

**Wskazówki dotyczące automatyzacji:** Aplikacja poleceń Powercli "Export-VDPortGroup" może pobierać informacje o rozproszonych wirtualnych portgroups, które mogą być następnie importowane do docelowego Distributed Switch za pomocą apletu poleceń "New-VDPortgroup -BackupPath".

##### **OVHcloud Veeam Backup**

Jeśli aplikacja Veeam dostarczana przez OVHcloud jest aktualnie używana do tworzenia kopii zapasowych wirtualnych maszyn w oryginalnej infrastrukturze, po przeprowadzeniu migracji konieczne będzie odtworzenie konfiguracji kopii zapasowej wirtualnych maszyn do docelowego vDC.

Poniżej lista elementów, które należy wziąć pod uwagę:

- Lista maszyn w trakcie tworzenia kopii zapasowej
- Parametry kopii zapasowej

Zapoznaj się z naszym przewodnikiem dotyczącym [aktywacji i korzystania z usługi Veeam Backup Managed](https://docs.ovh.com/gb/en/managed-bare-metal/veeam-backup-as-a-service/).

**Wskazówki dotyczące automatyzacji:** API OVHcloud dostarcza informacje związane z każdą kopią zapasową VM poprzez:

> [!api]
>
> @api {GET} /dedicatedCloud/{serviceName}/datacenter/{datacenterId}/vm/{vmId}
>

Sekcja "kopii zapasowej" zwróconego pliku json zawiera informacje na temat aktualnej konfiguracji kopii zapasowej.

#### Organizacja

Ze względów organizacyjnych wirtualne maszyny, hosty lub datastores mogły zostać umieszczone w katalogach.

Jeśli taka organizacja jest nadal konieczna, należy ją ponownie utworzyć w docelowym vDC.

#### VM

Istnieje kilka metod migracji wirtualnych maszyn do nowego vDC.

Jeśli Twoje źródło znajduje się na hostach Intel, możesz użyć vMotion do migracji wirtualnych maszyn do nowego VDC.

Jeśli Twoje źródło znajduje się na hostach AMD, wyłącz je i użyj migracji na zimno.

## Sprawdź również

Dołącz do społeczności naszych użytkowników na <https://community.ovh.com/en/>.
