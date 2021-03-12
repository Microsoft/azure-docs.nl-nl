---
title: Tijd synchronisatie voor Windows-Vm's in azure
description: Tijd synchronisatie voor virtuele Windows-machines.
author: cynthn
ms.service: virtual-machines
ms.collection: windows
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 09/17/2018
ms.author: cynthn
ms.openlocfilehash: 85abc77757d31f3b1054a0670ea3f65a4fcb2e52
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102555955"
---
# <a name="time-sync-for-windows-vms-in-azure"></a>Tijd synchronisatie voor Windows-Vm's in azure

Tijd synchronisatie is belang rijk voor beveiliging en gebeurtenis correlatie. Soms wordt deze gebruikt voor de implementatie van gedistribueerde trans acties. Nauw keurigheid van de tijd tussen meerdere computer systemen wordt bereikt via synchronisatie. Synchronisatie kan worden beïnvloed door meerdere dingen, zoals opnieuw opstarten en netwerk verkeer tussen de tijd bron en de computer die de tijd ophaalt. 

Azure wordt nu ondersteund door een infra structuur waarop Windows Server 2016 wordt uitgevoerd. Windows Server 2016 heeft verbeterde algoritmen gebruikt voor het corrigeren van tijd en voor waarde dat de lokale klok moet worden gesynchroniseerd met UTC.  Windows Server 2016 heeft ook de VMICTimeSync-service verbeterd die bepaalt hoe Vm's met de host worden gesynchroniseerd voor nauw keurige tijd. Verbeteringen bevatten meer nauw keurige initiële tijd bij het starten van de VM of het terugzetten van de VM en de interrupt-latentie correctie voor de voor beelden die worden geleverd aan Windows Time (W32Time). 


>[!NOTE]
>Bekijk deze [overzichts video op hoog niveau](https://aka.ms/WS2016TimeVideo)voor een snel overzicht van de Windows Time-service.
>
> Zie voor meer informatie [nauw keurige tijd voor Windows Server 2016](/windows-server/networking/windows-time-service/accurate-time). 

## <a name="overview"></a>Overzicht

Nauw keurigheid van de klok van een computer wordt aangegeven hoe dicht de computer klok is tot de UTC-tijd (Coordinated Universal Time). UTC wordt gedefinieerd door een multi nationaal voor beeld van nauw keurige atomische klokken die in 300 jaar slechts voor één seconde kunnen worden uitgeschakeld. Maar voor het lezen van UTC is echter gespecialiseerde hardware vereist. Tijd servers worden in plaats daarvan gesynchroniseerd naar UTC en zijn toegankelijk vanaf andere computers om te voorzien in schaal baarheid en robuustheid. Elke computer beschikt over een time-synchronisatie service die weet op welke tijd servers moet worden gebruikt en controleert periodiek of de computer klok moet worden gecorrigeerd en de tijd indien nodig wordt aangepast. 

Azure-hosts worden gesynchroniseerd met interne micro soft-tijd servers die hun tijd van micro soft-apparaten met stratum 1 innemen, met GPS-antennes. Virtuele machines in azure kunnen afhankelijk zijn van hun host om de nauw keurige tijd (*hosttijd*) op te geven voor de virtuele machine, of de VM kan rechtstreeks tijd ophalen van een tijd server of een combi natie van beide. 

De interactie van virtuele machines met de host kan ook van invloed zijn op de klok. Tijdens het [geheugen behoud](../maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot)van het onderhoud worden vm's gedurende Maxi maal 30 seconden onderbroken. Voordat het onderhoud begint, wordt de VM-klok bijvoorbeeld 10:00:00 uur en de laatste 28 seconden weer gegeven. Nadat de VM is hervat, wordt de klok op de VM nog steeds 10:00:00 uur weer gegeven. Dit is 28 seconden. Als u dit wilt corrigeren, wordt door de VMICTimeSync-service gecontroleerd wat er gebeurt op de host en wordt u gevraagd of er wijzigingen moeten worden aangebracht op de Vm's om te compenseren.

De VMICTimeSync-service werkt in een van de voor beelden of de synchronisatie modus en heeft alleen invloed op de klok vooruit. In de voorbeeld modus, waarvoor W32Time moet worden uitgevoerd, controleert de VMICTimeSync-service elke 5 seconden op de host en biedt deze tijd voorbeelden naar W32Time. Ongeveer elke 30 seconden neemt de W32Time-service het meest recente tijd voorbeeld en gebruikt deze om de klok van de gast te beïnvloeden. De synchronisatie modus wordt geactiveerd als een gast is hervat of als de klok van een gast meer dan vijf seconden achter de klok van de host overschrijdt. In gevallen waarin de W32Time-service op de juiste wijze wordt uitgevoerd, mag het laatste geval nooit plaatsvinden.

Zonder tijd synchronisatie werkt de klok op de VM over fouten. Wanneer er slechts één virtuele machine is, is het effect mogelijk niet significant, tenzij de werk belasting zeer nauw keurige keurige vereist. Maar in de meeste gevallen hebben we meerdere, onderling verbonden Vm's die gebruikmaken van tijd voor het bijhouden van trans acties en de tijd moeten consistent zijn gedurende de hele implementatie. Wanneer de tijd tussen Vm's afwijkt, ziet u de volgende effecten:

- De verificatie mislukt. Beveiligings protocollen zoals Kerberos of een certificaat afhankelijke technologie zijn afhankelijk van de tijd die consistent is op de systemen. 
- Het is heel moeilijk om erachter te komen wat er is gebeurd in een systeem als Logboeken (of andere gegevens) niet op tijd overeenkomen. Dezelfde gebeurtenis zou er op verschillende tijdstippen uit kunnen zien, waardoor de correlatie moeilijk is.
- Als de klok is uitgeschakeld, kan de factuur onjuist worden berekend.

De beste resultaten voor Windows-implementaties worden bereikt met behulp van Windows Server 2016 als het gast besturingssysteem. Dit zorgt ervoor dat u de meest recente verbeteringen in tijd synchronisatie kunt gebruiken.

## <a name="configuration-options"></a>Configuratie-opties

Er zijn drie opties voor het configureren van tijd synchronisatie voor uw Windows-Vm's die worden gehost in Azure:

- Host-tijd en time.windows.com. Dit is de standaard configuratie die wordt gebruikt in azure Marketplace-installatie kopieën.
- Alleen host.
- Gebruik een andere, externe tijd server met of zonder behulp van de hosttijd.


### <a name="use-the-default"></a>De standaard waarde gebruiken

Standaard worden installatie kopieën van het Windows-besturings systeem geconfigureerd voor W32Time om te synchroniseren vanaf twee bronnen: 

- De NtpClient-provider, die informatie uit time.windows.com haalt.
- De VMICTimeSync-service wordt gebruikt om de hosttijd te communiceren met de Vm's en om correcties aan te brengen nadat de virtuele machine is onderbroken voor onderhoud. Azure-hosts gebruiken micro soft-systemen met stratum 1 om nauw keurige tijd te hand haven.

W32Time zou de tijd provider liever in de volgende volg orde van prioriteit: stratum-niveau, hoofd vertraging, basis sprei ding, tijd verschuiving. In de meeste gevallen zal W32Time op een Azure-VM de voor keur geven aan de hosttijd als gevolg van de evaluatie. Dit zou doen om beide tijd bronnen te vergelijken. 

Voor computers die lid zijn van een domein, stelt het domein zelf tijd synchronisatie hiërarchie in, maar het forest-hoofd moet nog steeds tijd in beslag nemen en de volgende overwegingen blijven waar.


### <a name="host-only"></a>Alleen host 

Omdat time.windows.com een open bare NTP-server is, is het synchroniseren van de tijd vereist dat verkeer via internet wordt verzonden, wat kan leiden tot een negatieve invloed op de kwaliteit van de tijd synchronisatie. Wanneer u time.windows.com verwijdert door over te scha kelen naar een alleen-host, kunnen de synchronisatie resultaten soms worden verbeterd.

Overschakelen naar een alleen-host-tijd synchronisatie is zinvol als u tijd synchronisatie problemen ondervindt met de standaard configuratie. Probeer de alleen-host-synchronisatie uit om te zien of dit de tijd synchronisatie op VM zou verbeteren. 

### <a name="external-time-server"></a>Externe tijd server

Als u specifieke tijd synchronisatie vereisten hebt, is er ook een optie voor het gebruik van externe tijd servers. Externe tijd servers kunnen specifieke tijd opgeven, wat nuttig kan zijn voor test scenario's, waardoor de tijd uniform is met computers die worden gehost in niet-micro soft-data centers, of een schrikkel seconde op een speciale manier kunnen verwerken.

U kunt externe servers combi neren met de VMICTimeSync-service en VMICTimeProvider om resultaten te leveren die vergelijkbaar zijn met de standaard configuratie. 

## <a name="check-your-configuration"></a>Controleer de configuratie


Controleer of de provider van de NtpClient-tijd is geconfigureerd voor het gebruik van expliciete NTP-servers (NTP) of synchronisatie van domein tijd (NT5DS).

```
w32tm /dumpreg /subkey:Parameters | findstr /i "type"
```

Als de virtuele machine NTP gebruikt, ziet u de volgende uitvoer:

```
Value Name                 Value Type          Value Data
Type                       REG_SZ              NTP
```

Ga als volgt te werk om te zien welke tijd server de timer voor de NtpClient gebruikt voor een opdracht prompt met verhoogde bevoegdheid:

```
w32tm /dumpreg /subkey:Parameters | findstr /i "ntpserver"
```

Als de virtuele machine de standaard waarde gebruikt, ziet de uitvoer er als volgt uit:

```
NtpServer                  REG_SZ              time.windows.com,0x8
```


Om te zien welke tijd provider momenteel wordt gebruikt.

```
w32tm /query /source
```


Dit is de uitvoer die u kunt zien en wat het zou betekenen:
    
- **time.Windows.com** : in de standaard configuratie W32Time zou tijd van time.Windows.com ophalen. De synchronisatie kwaliteit van de tijd is afhankelijk van de verbinding met internet en wordt beïnvloed door de vertragingen van het pakket. Dit is de gebruikelijke uitvoer die u op een fysieke machine zou krijgen.
- **Synchronisatie provider voor IC-tijd voor VM**  : de virtuele machine wordt gesynchroniseerd met de tijd van de host. Dit is de gebruikelijke uitvoer van een virtuele machine die wordt uitgevoerd op Azure. 
- *Uw domein server* : de huidige computer bevindt zich in een domein en het domein definieert de tijd synchronisatie hiërarchie.
- *Een andere server* -W32Time is expliciet geconfigureerd om de tijd op te halen van een andere server. De duur van de synchronisatie van tijd is afhankelijk van de kwaliteit van de server.
- De **lokale CMOS-klok** klok is niet gesynchroniseerd. U kunt deze uitvoer ophalen als W32Time onvoldoende tijd heeft om te starten nadat de computer opnieuw is opgestart of als alle geconfigureerde tijd bronnen niet beschikbaar zijn.


## <a name="opt-in-for-host-only-time-sync"></a>Opt-in voor alleen host-tijd synchronisatie

Azure is voortdurend bezig met het verbeteren van de tijd synchronisatie op hosts en kan garanderen dat alle tijd synchronisatie-infra structuur Co is in Data Centers van micro soft. Als u tijd synchronisatie problemen hebt met de standaard instellingen die time.windows.com gebruiken als de primaire tijd bron, kunt u de volgende opdrachten gebruiken om u aan te melden voor alleen host-tijd synchronisatie.

Markeer de VMIC-provider als ingeschakeld. 

```
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\TimeProviders\VMICTimeProvider /v Enabled /t REG_DWORD /d 1 /f
```

Markeer de NTPClient-provider als uitgeschakeld.

```
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\TimeProviders\NtpClient /v Enabled /t REG_DWORD /d 0 /f
```

Start de W32Time-service opnieuw.

```
net stop w32time && net start w32time
```


## <a name="windows-server-2012-and-r2-vms"></a>Virtuele machines met Windows Server 2012 en R2 

Windows Server 2012 en Windows Server 2012 R2 hebben verschillende standaard instellingen voor tijd synchronisatie. De W32Time wordt standaard geconfigureerd op een manier die de voor keur geeft aan een lage overhead van de service tot een exacte periode. 

Als u de implementaties van Windows Server 2012 en 2012 R2 wilt verplaatsen om de nieuwere standaard waarden te gebruiken die de voor keur geven, kunt u de volgende instellingen Toep assen.

Werk de W32Time-poll-en update-intervallen bij zodat deze overeenkomen met de instellingen van Windows Server 2016.

```
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\Config /v MinPollInterval /t REG_DWORD /d 6 /f
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\Config /v MaxPollInterval /t REG_DWORD /d 10 /f
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\Config /v UpdateInterval /t REG_DWORD /d 100 /f
w32tm /config /update
```

Voor W32Time om de nieuwe poll intervallen te kunnen gebruiken, moet de NtpServers worden gemarkeerd als gebruiken. Als servers zijn voorzien van een aantekening met 0x1 bitflag masker, zou dit mechanisme negeren en wordt in plaats daarvan SpecialPollInterval gebruikt. Zorg ervoor dat de opgegeven NTP-servers de vlag 0x8 gebruiken of helemaal geen vlag hebben:

Controleer welke vlaggen worden gebruikt voor de gebruikte NTP-servers.

```
w32tm /dumpreg /subkey:Parameters | findstr /i "ntpserver"
```

## <a name="next-steps"></a>Volgende stappen

Hieronder vindt u koppelingen naar meer informatie over de tijd synchronisatie:

- [Hulpprogramma’s en instellingen voor de Windows Time-service](/windows-server/networking/windows-time-service/windows-time-service-tools-and-settings)
- [Verbeteringen in Windows Server 2016 ](/windows-server/networking/windows-time-service/windows-server-2016-improvements)
- [Nauw keurige tijd voor Windows Server 2016](/windows-server/networking/windows-time-service/accurate-time)
- [Ondersteunings grens voor het configureren van de Windows Time-service voor omgevingen met hoge nauw keurigheid](/windows-server/networking/windows-time-service/support-boundary)
