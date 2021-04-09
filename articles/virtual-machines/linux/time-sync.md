---
title: Tijd synchronisatie voor virtuele Linux-machines in azure
description: Tijd synchronisatie voor virtuele Linux-machines.
services: virtual-machines
documentationcenter: ''
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/20/2020
ms.author: cynthn
ms.openlocfilehash: 18c8570a8066985cab5263c4779787062dc32d75
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102552640"
---
# <a name="time-sync-for-linux-vms-in-azure"></a>Tijd synchronisatie voor virtuele Linux-machines in azure

Tijd synchronisatie is belang rijk voor beveiliging en gebeurtenis correlatie. Soms wordt deze gebruikt voor de implementatie van gedistribueerde trans acties. Nauw keurigheid van de tijd tussen meerdere computer systemen wordt bereikt via synchronisatie. Synchronisatie kan worden beïnvloed door meerdere dingen, zoals opnieuw opstarten en netwerk verkeer tussen de tijd bron en de computer die de tijd ophaalt. 

Azure wordt ondersteund door een infra structuur waarop Windows Server 2016 wordt uitgevoerd. Windows Server 2016 heeft verbeterde algoritmen gebruikt voor het corrigeren van tijd en voor waarde dat de lokale klok moet worden gesynchroniseerd met UTC.  De functie Windows Server 2016 nauw keurige tijd is aanzienlijk verbeterd hoe de VMICTimeSync-service die virtuele machines met de host bestuurt voor nauw keurige tijd. Verbeteringen bevatten meer nauw keurige initiële tijd bij het starten van de VM of het herstellen van VM'S en de onderbreking van de latentie. 

> [!NOTE]
> Bekijk deze [overzichts video op hoog niveau](https://aka.ms/WS2016TimeVideo)voor een snel overzicht van de Windows Time-service.
>
> Zie voor meer informatie [nauw keurige tijd voor Windows Server 2016](/windows-server/networking/windows-time-service/accurate-time). 

## <a name="overview"></a>Overzicht

Nauw keurigheid van de klok van een computer wordt aangegeven hoe dicht de computer klok is tot de UTC-tijd (Coordinated Universal Time). UTC wordt gedefinieerd door een multi nationaal voor beeld van nauw keurige atomische klokken die in 300 jaar slechts voor één seconde kunnen worden uitgeschakeld. Maar voor het lezen van UTC is echter gespecialiseerde hardware vereist. Tijd servers worden in plaats daarvan gesynchroniseerd naar UTC en zijn toegankelijk vanaf andere computers om te voorzien in schaal baarheid en robuustheid. Elke computer beschikt over een time-synchronisatie service die weet op welke tijd servers moet worden gebruikt en controleert periodiek of de computer klok moet worden gecorrigeerd en de tijd indien nodig wordt aangepast. 

Azure-hosts worden gesynchroniseerd met interne micro soft-tijd servers die hun tijd van micro soft-apparaten met stratum 1 innemen, met GPS-antennes. Virtuele machines in azure kunnen afhankelijk zijn van hun host om de nauw keurige tijd (*hosttijd*) op te geven voor de virtuele machine, of de VM kan rechtstreeks tijd ophalen van een tijd server of een combi natie van beide. 

Op zelfstandige hardware leest het Linux-besturings systeem alleen de hardware-klok van de host bij het opstarten. Daarna wordt de klok onderhouden met behulp van de interrupt-timer in de Linux-kernel. In deze configuratie wordt de klok na verloop van tijd overgeschakeld. In nieuwere Linux-distributies in azure kunnen Vm's de VMICTimeSync-provider gebruiken, opgenomen in de LIS (Linux Integration Services), om regel matig een query uit te lopen op klok updates van de host.

De interactie van virtuele machines met de host kan ook van invloed zijn op de klok. Tijdens het [geheugen behoud](../maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot)van het onderhoud worden vm's gedurende Maxi maal 30 seconden onderbroken. Voordat het onderhoud begint, wordt de VM-klok bijvoorbeeld 10:00:00 uur en de laatste 28 seconden weer gegeven. Nadat de VM is hervat, wordt de klok op de VM nog steeds 10:00:00 uur weer gegeven. Dit is 28 seconden. Als u dit wilt corrigeren, wordt door de VMICTimeSync-service gecontroleerd wat er gebeurt op de host en wordt u gevraagd of er wijzigingen moeten worden aangebracht op de Vm's om te compenseren.

Zonder tijd synchronisatie werkt de klok op de VM over fouten. Wanneer er slechts één virtuele machine is, is het effect mogelijk niet significant, tenzij de werk belasting zeer nauw keurige keurige vereist. Maar in de meeste gevallen hebben we meerdere, onderling verbonden Vm's die gebruikmaken van tijd voor het bijhouden van trans acties en de tijd moeten consistent zijn gedurende de hele implementatie. Wanneer de tijd tussen Vm's afwijkt, ziet u de volgende effecten:

- De verificatie mislukt. Beveiligings protocollen zoals Kerberos of een certificaat afhankelijke technologie zijn afhankelijk van de tijd die consistent is op de systemen.
- Het is heel moeilijk om erachter te komen wat er is gebeurd in een systeem als Logboeken (of andere gegevens) niet op tijd overeenkomen. Dezelfde gebeurtenis zou er op verschillende tijdstippen uit kunnen zien, waardoor de correlatie moeilijk is.
- Als de klok is uitgeschakeld, kan de factuur onjuist worden berekend.



## <a name="configuration-options"></a>Configuratie-opties

Er zijn in het algemeen drie manieren om tijd synchronisatie te configureren voor uw virtuele Linux-machines die worden gehost in Azure:

- De standaard configuratie voor installatie kopieën van Azure Marketplace maakt gebruik van de NTP-tijd en de VMICTimeSync-host. 
- Alleen host met behulp van VMICTimeSync.
- Gebruik een andere, externe tijd server met of zonder gebruik te maken van VMICTimeSync-hosttijd.


### <a name="use-the-default"></a>De standaard waarde gebruiken

De meeste installatie kopieën van Azure Marketplace voor Linux zijn standaard geconfigureerd om te synchroniseren vanaf twee bronnen: 

- NTP als primair, waarmee tijd wordt opgehaald van een NTP-server. Ubuntu 16,04 LTS Marketplace-installatie kopieën gebruiken bijvoorbeeld **NTP.Ubuntu.com**.
- De VMICTimeSync-service als secundair, wordt gebruikt om de hosttijd te communiceren met de Vm's en om correcties aan te brengen nadat de virtuele machine voor onderhoud is onderbroken. Azure-hosts gebruiken micro soft-systemen met stratum 1 om nauw keurige tijd te hand haven.

In nieuwere Linux-distributies biedt de VMICTimeSync-service een NTP-klok bron (Time Protocol), maar eerdere distributies bieden deze klok bron mogelijk niet aan en vallen terug op NTP om tijd te halen van de host.

Voer de opdracht uit om te controleren of NTP correct synchroniseert `ntpq -p` .

### <a name="host-only"></a>Alleen host 

Omdat NTP-servers, zoals time.windows.com en ntp.ubuntu.com, openbaar zijn, is het verzenden van verkeer via internet vereist voor de synchronisatie tijd. Verschillende pakket vertragingen kunnen een negatieve invloed hebben op de kwaliteit van de tijd synchronisatie. Als u NTP verwijdert door te scha kelen naar de alleen-host synchronisatie, kunnen de synchronisatie resultaten soms worden verbeterd.

Overschakelen naar een alleen-host-tijd synchronisatie is zinvol als u tijd synchronisatie problemen ondervindt met de standaard configuratie. Probeer de alleen-host synchronisatie uit om te zien of dit de tijd synchronisatie op uw virtuele machine zou verbeteren. 

### <a name="external-time-server"></a>Externe tijd server

Als u specifieke tijd synchronisatie vereisten hebt, is er ook een optie voor het gebruik van externe tijd servers. Externe tijd servers kunnen specifieke tijd opgeven, wat nuttig kan zijn voor test scenario's, waardoor de tijd uniform is met computers die worden gehost in niet-micro soft-data centers, of een schrikkel seconde op een speciale manier kunnen verwerken.

U kunt een externe tijd server combi neren met de VMICTimeSync-service om resultaten te leveren die vergelijkbaar zijn met de standaard configuratie. Het combi neren van een externe tijd server met VMICTimeSync is de beste optie voor het oplossen van problemen die kunnen optreden wanneer Vm's voor onderhoud worden onderbroken. 

## <a name="tools-and-resources"></a>Hulpprogramma's en informatiebronnen

Er zijn enkele basis opdrachten voor het controleren van de synchronisatie configuratie van uw tijd. Documentatie voor Linux-distributie bevat meer informatie over de beste manier om tijd synchronisatie voor die distributie te configureren.

### <a name="integration-services"></a>Integratieservices

Controleer of de integratie service (hv_utils) is geladen.

```bash
lsmod | grep hv_utils
```
Er wordt een fout weer gegeven die er ongeveer als volgt uitziet:

```
hv_utils               24418  0
hv_vmbus              397185  7 hv_balloon,hyperv_keyboard,hv_netvsc,hid_hyperv,hv_utils,hyperv_fb,hv_storvsc
```

Controleer of de Hyper-V Integration Services-daemon wordt uitgevoerd.

```bash
ps -ef | grep hv
```

Er wordt een fout weer gegeven die er ongeveer als volgt uitziet:

```
root        229      2  0 17:52 ?        00:00:00 [hv_vmbus_con]
root        391      2  0 17:52 ?        00:00:00 [hv_balloon]
```


### <a name="check-for-ptp-clock-source"></a>Controleren op PTP-Clock-bron

Met nieuwere versies van Linux is een timer-klok bron (Precision Time Protocol) beschikbaar als onderdeel van de VMICTimeSync-provider. In oudere versies van Red Hat Enterprise Linux of CentOS 7. x de [Linux-integratie Services](https://github.com/LIS/lis-next) kunnen worden gedownload en gebruikt om het bijgewerkte stuur programma te installeren. Wanneer de PTP-Clock-bron beschikbaar is, is het Linux-apparaat de vorm/dev/PTP *x*. 

Bekijk welke PTP-timer bronnen beschikbaar zijn.

```bash
ls /sys/class/ptp
```

In dit voor beeld wordt de geretourneerde waarde *ptp0*, zodat we deze gebruiken om de naam van de klok te controleren. Als u het apparaat wilt controleren, controleert u de naam van de klok.

```bash
cat /sys/class/ptp/ptp0/clock_name
```

Dit moet worden geretourneerd `hyperv` .

### <a name="chrony"></a>chrony

In Ubuntu 19,10 en hoger, Red Hat Enterprise Linux en CentOS 8. x, [chrony](https://chrony.tuxfamily.org/) is geconfigureerd voor het gebruik van een PTP-bron klok. In plaats van chrony maakt oudere linux-releases gebruik van de Network Time Protocol daemon (ntpd), die geen PTP-bronnen ondersteunt. Voor het inschakelen van PTP in deze releases moet chrony hand matig worden geïnstalleerd en geconfigureerd (in chrony. conf) met behulp van de volgende code:

```bash
refclock PHC /dev/ptp0 poll 3 dpoll -2 offset 0
```

Zie [tijd synchronisatie](https://ubuntu.com/server/docs/network-ntp)voor meer informatie over Ubuntu en ntp.

Zie [NTP configureren](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/system_administrators_guide/ch-configuring_ntp_using_ntpd#s1-Configure_NTP)voor meer informatie over Red Hat en ntp. 

Zie [using chrony](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/system_administrators_guide/ch-configuring_ntp_using_the_chrony_suite#sect-Using_chrony)(Engelstalig) voor meer informatie over chrony.

Als zowel de chrony-als de VMICTimeSync-bron tegelijk zijn ingeschakeld, kunt u een als voor **keur** markeren, waarmee de andere bron wordt ingesteld als back-up. Omdat de NTP-Services de klok voor grote scheefheden, behalve na een lange periode, niet bijwerken, zal de VMICTimeSync de klok van onderbroken VM-gebeurtenissen veel sneller worden hersteld dan op NTP gebaseerde hulpprogram ma's.

Chronyd versnelt of vertraagt standaard de systeem klok om tijds drift te verhelpen. Als de drift te groot wordt, chrony de drift niet oplossen. Om dit op te lossen, `makestep` kan de para meter in **/etc/chrony.conf** worden gewijzigd om een tijd synchronisatie af te dwingen als de waarde van de drift de opgegeven drempel overschrijdt.

 ```bash
makestep 1.0 -1
```

Hier dwingt chrony een tijd update af als de drift groter is dan 1 seconde. Als u de wijzigingen wilt Toep assen, moet u de chronyd-service opnieuw starten:

```bash
systemctl restart chronyd
```

### <a name="systemd"></a>systemd 

In SUSE-en Ubuntu-releases vóór 19,10 wordt tijd synchronisatie geconfigureerd met behulp van [systemed](https://www.freedesktop.org/wiki/Software/systemd/). Zie [tijd synchronisatie](https://help.ubuntu.com/lts/serverguide/NTP.html)voor meer informatie over Ubuntu. Zie voor meer informatie over SUSE sectie 4.5.8 in [SuSE Linux Enterprise Server 12 SP3-Release opmerkingen](https://www.suse.com/releasenotes/x86_64/SUSE-SLES/12-SP3/#InfraPackArch.ArchIndependent.SystemsManagement).

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie [nauw keurige tijd voor Windows Server 2016](/windows-server/networking/windows-time-service/accurate-time).


