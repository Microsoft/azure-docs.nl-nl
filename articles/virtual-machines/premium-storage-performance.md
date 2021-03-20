---
title: 'Azure Premium Storage: ontwerp voor hoge prestaties'
description: Ontwerp krachtige toepassingen met Azure Premium SSD Managed disks. Premium Storage biedt schijf ondersteuning met hoge prestaties en lage latentie voor I/O-intensieve workloads die worden uitgevoerd op Azure Virtual Machines.
author: roygara
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 10/05/2020
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 7e93c659ad58db8d82e68380ab6a0855af27e1bf
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98882379"
---
# <a name="azure-premium-storage-design-for-high-performance"></a>Azure Premium-opslag: ontwerpen voor hoge prestaties

Dit artikel bevat richt lijnen voor het bouwen van toepassingen met hoge prestaties met behulp van Azure Premium Storage. U kunt de instructies in dit document gebruiken in combi natie met best practices voor prestaties die van toepassing zijn op technologieën die door uw toepassing worden gebruikt. Ter illustratie van de richt lijnen hebben we SQL Server uitgevoerd op Premium Storage als voor beeld in dit document gebruikt.

Hoewel we prestatie scenario's voor de opslaglaag in dit artikel adresseren, moet u de toepassingslaag optimaliseren. Als u bijvoorbeeld als host fungeert voor een share point-Farm in azure Premium Storage, kunt u de SQL Server-voor beelden in dit artikel gebruiken om de database server te optimaliseren. Daarnaast optimaliseert u de webserver en toepassings server van de share point-farm om de meeste prestaties te verkrijgen.

Dit artikel helpt u bij het beantwoorden van de volgende veelgestelde vragen over het optimaliseren van de prestaties van toepassingen op Azure Premium Storage.

* Hoe kunt u de prestaties van uw toepassingen meten?  
* Waarom worden de verwachte hoge prestaties niet weer gegeven?  
* Welke factoren zijn van invloed op de prestaties van uw toepassing op Premium Storage?  
* Hoe beïnvloeden deze factoren de prestaties van uw toepassing op Premium Storage?  
* Hoe optimaliseert u het aantal IOPS, de band breedte en de latentie?  

We hebben deze richt lijnen specifiek voor Premium Storage gegeven, omdat werk belastingen die worden uitgevoerd op Premium Storage uiterst prestaties gevoelig zijn. Er zijn voor beelden, waar van toepassing. U kunt ook enkele van deze richt lijnen Toep assen op toepassingen die worden uitgevoerd op IaaS-Vm's met standaard opslag schijven.

> [!NOTE]
> Soms is het een probleem met de prestaties van een schijf. In deze situaties moet u de [netwerk prestaties](../virtual-network/virtual-network-optimize-network-bandwidth.md)optimaliseren.
>
> Als u een bench Mark-schijf wilt maken, raadpleegt u onze artikelen over de benchmarking van een schijf:
>
> * Voor Linux: [Bench Mark your application on Azure Disk Storage](./disks-benchmarks.md)
> * Voor Windows: [benchmarking van een schijf](./disks-benchmarks.md).
>
> Als uw virtuele machine versneld netwerken ondersteunt, moet u ervoor zorgen dat deze is ingeschakeld. Als deze optie niet is ingeschakeld, kunt u deze inschakelen op al geïmplementeerde Vm's in [Windows](../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms) en [Linux](../virtual-network/create-vm-accelerated-networking-cli.md#enable-accelerated-networking-on-existing-vms).

Voordat u begint, lees dan eerst de [Select a Azure-schijf type voor IaaS vm's](disks-types.md) en [schaalbaarheids doelen voor Premium Premium Storage-pagina-Blob-opslag accounts](../storage/blobs/scalability-targets-premium-page-blobs.md).

## <a name="application-performance-indicators"></a>Prestatie-indica toren voor toepassingen

We bepalen of een toepassing goed werkt of geen gebruikmaakt van prestatie-indica toren zoals, hoe snel een toepassing een gebruikers aanvraag verwerkt, hoeveel gegevens een toepassing per aanvraag verwerkt, hoeveel aanvragen een toepassings verwerking in een specifieke periode is, hoe lang een gebruiker moet wachten om een antwoord te ontvangen nadat ze hun aanvraag hebben ingediend. De technische voor waarden voor deze prestatie-indica toren zijn, IOPS, door Voer of band breedte en latentie.

In deze sectie bespreken we de algemene prestatie-indica toren in de context van Premium Storage. In de volgende sectie worden toepassings vereisten verzameld. u leert hoe u deze prestatie-indica toren voor uw toepassing meet. Later in het optimaliseren van de prestaties van de toepassing leert u meer over de factoren die van invloed zijn op deze prestatie-indica toren en aanbevelingen om ze te optimaliseren.

## <a name="iops"></a>IOPS

IOPS of invoer/uitvoer-bewerkingen per seconde is het aantal aanvragen dat uw toepassing in één seconde naar de opslag schijven verzendt. Een invoer-en uitvoer bewerking kan worden gelezen of geschreven, opeenvolgend of wille keurig. Toepassingen voor online Trans Action processing (OLTP), zoals een online retail website, moeten direct veel gelijktijdige gebruikers aanvragen verwerken. De gebruikers aanvragen worden intensieve database transacties ingevoegd en bijgewerkt, die de toepassing snel moet verwerken. Daarom vereisen OLTP-toepassingen zeer hoge IOPS. Dergelijke toepassingen verwerken miljoenen kleine en wille keurige i/o-aanvragen. Als u een dergelijke toepassing hebt, moet u de toepassings infrastructuur ontwerpen om te optimaliseren voor IOPS. In de volgende sectie worden de prestaties van de *toepassing geoptimaliseerd*, worden alle factoren beschreven waarmee u rekening moet houden om hoge IOPS te verkrijgen.

Wanneer u een premium opslagschijf koppelt aan uw schaalbare VM, biedt Azure u een gegarandeerd aantal IOPS op basis van de schijfspecificatie. Een P50-schijf is bijvoorbeeld goed voor 7500 IOPS. Voor elke schaalbare VM geldt ook een specifieke IOPS-limiet, afhankelijk van de grootte van de VM. Een standaard GS5 VM heeft bijvoorbeeld 80.000 IOPS-limiet.

## <a name="throughput"></a>Doorvoer

Door Voer of band breedte is de hoeveelheid gegevens die uw toepassing naar de opslag schijven in een opgegeven interval verzendt. Als uw toepassing invoer/uitvoer-bewerkingen uitvoert met grote i/o-eenheids grootten, is een hoge door Voer vereist. Data Warehouse-toepassingen geven vaak scan intensieve bewerkingen uit die toegang hebben tot grote delen van gegevens tegelijk en voeren vaak bulk bewerkingen uit. Met andere woorden, deze toepassingen vereisen een hogere door voer. Als u een dergelijke toepassing hebt, moet u de infra structuur zodanig ontwerpen dat deze kan worden geoptimaliseerd voor door voer. In de volgende sectie worden de factoren besproken die u moet afstemmen om dit te verfijnen.

Wanneer u een Premium-opslag schijf koppelt aan een grootschalige virtuele machine, wordt de door Voer van Azure ingericht volgens die schijf specificatie. Een P50-schijf biedt bijvoorbeeld een schijfdoorvoer van250 MB per seconde. Voor elke schaalbare VM geldt ook een specifieke doorvoerlimiet, afhankelijk van de grootte van de VM. Een standaard GS5 VM heeft bijvoorbeeld een maximale doorvoer van 2000 MB per seconde.

Er is een relatie tussen door Voer en IOPS, zoals wordt weer gegeven in de onderstaande formule.

![Relatie van IOPS en door Voer](linux/media/premium-storage-performance/image1.png)

Daarom is het belang rijk om de optimale door Voer en IOPS waarden te bepalen die uw toepassing nodig heeft. Als u probeert een optimalisatie uit te proberen, wordt dit ook beïnvloed. In een latere sectie worden de prestaties van de *toepassing geoptimaliseerd*. we bespreken nu meer informatie over het optimaliseren van IOPS en door voer.

## <a name="latency"></a>Latentie

Latentie is de tijd die een toepassing nodig heeft om één aanvraag te ontvangen, deze te verzenden naar de opslag schijven en het antwoord naar de client te verzenden. Dit is een kritieke maat regel voor de prestaties van een toepassing naast IOPS en door voer. De latentie van een Premium-opslag schijf is de tijd die nodig is om de gegevens voor een aanvraag op te halen en deze terug te sturen naar uw toepassing. Premium Storage biedt consistente lage latenties. Premium-schijven zijn ontworpen om latentie in milliseconden voor de meeste i/o-bewerkingen te bieden. Als u opslaan in cache opslag op Premium Storage-schijven inschakelt, kunt u veel minder lees latentie krijgen. In de volgende sectie wordt beschreven hoe u de prestaties van de *toepassing kunt optimaliseren*.

Wanneer u uw toepassing optimaliseert om meer IOPS en door voer te krijgen, is dit van invloed op de latentie van uw toepassing. Na het afstemmen van de prestaties van de toepassing evalueert u altijd de latentie van de toepassing om onverwacht gedrag van hoge latentie te voor komen.

De volgende besturings vlak bewerkingen op Managed Disks kunnen een verplaatsing van de schijf van de ene opslag locatie naar de andere omvatten. Dit wordt ingedeeld via achtergrond kopieën van gegevens die enkele uren kunnen duren, meestal minder dan 24 uur, afhankelijk van de hoeveelheid gegevens op de schijven. Gedurende die tijd kan uw toepassing hoger zijn dan de gebruikelijke lees latentie, omdat sommige Lees bewerkingen worden omgeleid naar de oorspronkelijke locatie en langer kunnen duren. Er is geen invloed op de schrijf latentie tijdens deze periode.

- Werk het opslag type bij.
- Ontkoppel en koppel een schijf van de ene VM naar een andere.
- Een beheerde schijf maken op basis van een VHD.
- Een beheerde schijf maken op basis van een moment opname.
- Niet-beheerde schijven converteren naar Managed disks.

## <a name="performance-application-checklist-for-disks"></a>Controle lijst voor prestatie toepassingen voor schijven

Bij de eerste stap bij het ontwerpen van toepassingen met hoge prestaties die worden uitgevoerd op Azure Premium Storage worden de prestatie vereisten van uw toepassing beter beschreven. Nadat u de prestatie vereisten hebt verzameld, kunt u uw toepassing optimaliseren om optimaal te kunnen profiteren van de prestaties.

In de vorige sectie hebben we de algemene prestatie-indica toren, IOPS, door Voer en latentie uitgelegd. U moet bepalen welke van deze prestatie-indica toren essentieel zijn voor uw toepassing om de gewenste gebruikers ervaring te bieden. Hoge IOPS is bijvoorbeeld belang rijk voor OLTP-toepassingen die miljoenen trans acties in een seconde verwerken. Overwegende dat hoge door voer essentieel is voor Data Warehouse-toepassingen die grote hoeveel heden gegevens in een seconde verwerken. Extreem lage latentie is essentieel voor realtime toepassingen zoals live video streaming websites.

Meet vervolgens de maximale prestatie vereisten van uw toepassing gedurende de levens duur. Gebruik de voorbeeld controlelijst hieronder als begin. De maximale prestatie vereisten vastleggen tijdens de peri Oden normaal, piek en buiten kantoor uren. Door vereisten voor alle werkbelasting niveaus te identificeren, kunt u de algehele prestatie vereisten van uw toepassing bepalen. De normale werk belasting van een e-commerce website is bijvoorbeeld de trans acties die worden uitgevoerd gedurende de meeste dagen in een jaar. De piek belasting van de website is de trans acties die worden uitgevoerd tijdens vakantie dagen of speciale verkoop gebeurtenissen. De piek werk belasting is doorgaans voor een beperkte periode van kracht, maar kan uw toepassing wel twee of meer keer de normale werking laten schalen. Ontdek de vereisten voor 50 percentiel, 90 percentiel en 99 percentiel. Dit helpt bij het filteren van uitschieters in de prestatie vereisten en u kunt zich richten op het optimaliseren van de juiste waarden.

## <a name="application-performance-requirements-checklist"></a>Controle lijst voor toepassings prestaties

| **Prestatievereisten** | **50 percentiel** | **90 percentiel** | **99 percentiel** |
| --- | --- | --- | --- |
| Met maximaal Trans acties per seconde | | | |
| % Lees bewerkingen | | | |
| % Schrijf bewerkingen | | | |
| % Wille keurige bewerkingen | | | |
| % Sequentiële bewerkingen | | | |
| Grootte van IO-aanvraag | | | |
| Gemiddelde door Voer | | | |
| Met maximaal Doorvoer | | | |
| Haal. Latentie | | | |
| Gemiddelde latentie | | | |
| Met maximaal CPU | | | |
| Gemiddelde CPU | | | |
| Met maximaal Geheugen | | | |
| Gemiddeld geheugen | | | |
| Wachtrij diepte | | | |

> [!NOTE]
> U kunt overwegen deze nummers te schalen op basis van de verwachte toekomstige groei van uw toepassing. Het is een goed idee om de groei vooraf te plannen, omdat het lastig is om de infra structuur voor het verbeteren van de prestaties later te wijzigen.

Als u een bestaande toepassing hebt en wilt overstappen op Premium Storage, moet u eerst de controle lijst voor de bestaande toepassing bouwen. Vervolgens bouwt u een prototype van uw toepassing op Premium Storage en ontwerpt u de toepassing op basis van de richt lijnen die worden beschreven bij het optimaliseren van de *prestaties van toepassingen* in een latere sectie van dit document. In het volgende artikel worden de hulpprogram ma's beschreven die u kunt gebruiken voor het verzamelen van de prestatie metingen.

### <a name="counters-to-measure-application-performance-requirements"></a>Tellers voor het meten van de prestatie vereisten voor toepassingen

De beste manier om prestatie vereisten van uw toepassing te meten, is het gebruik van hulpprogram ma's voor prestatie controle die worden meegeleverd met het besturings systeem van de server. U kunt PerfMon voor Windows en iostat gebruiken voor Linux gebruiken. Deze hulpprogram ma's vastleggen tellers die overeenkomen met elke meting die wordt uitgelegd in de bovenstaande sectie. U moet de waarden van deze prestatie meter items vastleggen wanneer uw toepassing de werk belasting van de normale, piek en buiten kantoor uren uitvoert.

De prestatie meter items zijn beschikbaar voor processor, geheugen en, elke logische schijf en fysieke schijf van uw server. Wanneer u Premium Storage-schijven met een virtuele machine gebruikt, zijn de fysieke-schijf tellers voor elke Premium-opslag schijf en worden voor elk volume dat op de Premium Storage-schijven wordt gemaakt, items voor logische schijven opgenomen. U moet de waarden vastleggen voor de schijven waarop de workload van uw toepassing wordt gehost. Als er een toewijzing is tussen logische en fysieke schijven, kunt u verwijzen naar fysieke-schijf items; Raadpleeg anders de items van de logische schijf. In Linux genereert de iostat gebruiken-opdracht een rapport voor CPU-en schijf gebruik. Het rapport schijf gebruik bevat statistieken per fysiek apparaat of partitie. Als u een database server hebt met gegevens en logboeken op afzonderlijke schijven, verzamelt u deze gegevens voor beide schijven. In de onderstaande tabel worden de items voor schijven, processors en geheugen beschreven:

| Prestatiemeteritem | Beschrijving | Controle | Iostat gebruiken |
| --- | --- | --- | --- |
| **IOPS of trans acties per seconde** |Het aantal I/O-aanvragen dat per seconde aan de opslag schijf is uitgegeven. |Lees bewerkingen per seconde <br> Schrijf bewerkingen per seconde |TPS <br> r/s <br> w/s |
| **Lees-en schrijf bewerkingen op schijf** |% van lees-en schrijf bewerkingen die op de schijf worden uitgevoerd. |Percentage schijf leestijd <br> Percentage schrijf tijd schijf |r/s <br> w/s |
| **Doorvoer** |Hoeveelheid gegevens die per seconde moet worden gelezen van of wegge schreven naar de schijf. |Gelezen bytes per seconde <br> Geschreven bytes per seconde |kB_read/s <br> kB_wrtn/s |
| **Latentie** |Totale tijd voor het volt ooien van een schijf-i/o-aanvraag. |Gemiddeld aantal seconden/Lees bewerkingen schijf <br> Gemiddeld aantal seconden per schrijf bewerking schijf |koper <br> svctm |
| **I/o-grootte** |De grootte van I/O-aanvragen van problemen met de opslag schijven. |Gemiddeld aantal gelezen bytes per schijf <br> Gemiddeld aantal geschreven bytes per schijf |avgrq-Sz |
| **Wachtrij diepte** |Aantal openstaande I/O-aanvragen dat wacht op het lezen van of schrijven naar de opslag schijf. |Huidige wachtrij lengte voor de schijf |avgqu-Sz |
| **Max. geheugen** |Hoeveelheid geheugen die nodig is om de toepassing probleemloos uit te voeren |Percentage toegewezen bytes in gebruik |Vmstat gebruiken |
| **Max. CPU** |Hoeveelheid CPU vereist om toepassing probleemloos uit te voeren |Percentage processor tijd |% util |

Meer informatie over [iostat gebruiken](https://linux.die.net/man/1/iostat) en [perfmon](/windows/win32/perfctrs/performance-counters-portal).



## <a name="optimize-application-performance"></a>Prestaties van de toepassing optimaliseren

De belangrijkste factoren die van invloed zijn op de prestaties van een toepassing die wordt uitgevoerd op Premium Storage, zijn aard van de i/o-aanvragen, de VM-grootte, de schijf grootte, het aantal schijven, het opslaan van schijven, meerdere threads en de wachtrij diepte. U kunt enkele van deze factoren beheren met knoppen van het systeem. De meeste toepassingen bieden u mogelijk geen optie om de i/o-grootte en de wachtrij diepte rechtstreeks te wijzigen. Als u bijvoorbeeld SQL Server gebruikt, kunt u niet de i/o-grootte en de wachtrij diepte kiezen. SQL Server kiest de optimale IO-grootte en wachtrij diepte waarden om de meeste prestaties te verkrijgen. Het is belang rijk om inzicht te krijgen in de gevolgen van beide typen factoren voor de prestaties van uw toepassing, zodat u de juiste resources kunt inrichten om te voldoen aan de prestatie behoeften.

In deze sectie raadpleegt u de controle lijst voor toepassings vereisten die u hebt gemaakt om te bepalen hoeveel u nodig hebt om de prestaties van uw toepassingen te optimaliseren. Op basis hiervan kunt u bepalen welke factoren uit deze sectie u wilt afstemmen. Als u de effecten van elke factor van de prestaties van uw toepassing wilt Witness, voert u hulpprogram ma's voor benchmarking uit in de installatie van uw toepassing. Raadpleeg het artikel over benchmarking, gekoppeld aan het einde, voor stappen voor het uitvoeren van algemene benchmarking-hulpprogram ma's op Windows-en Linux-Vm's.

### <a name="optimize-iops-throughput-and-latency-at-a-glance"></a>IOPS, door Voer en latentie in één oogopslag optimaliseren

De volgende tabel bevat een overzicht van de prestatie factoren en de stappen die nodig zijn om IOPS, door Voer en latentie te optimaliseren. De secties die volgen op deze samen vatting geven een beschrijving van elke factor is veel meer.

Zie [grootten voor virtuele machines in azure](sizes.md)voor meer informatie over VM-grootten en over de IOPS, door Voer en latentie die beschikbaar zijn voor elk type virtuele machine.

| | **IOPS** | **Doorvoer** | **Latentie** |
| --- | --- | --- | --- |
| **Voorbeeld scenario** |De OLTP-toepassing voor ondernemingen vereist zeer hoge trans acties per seconde. |Bedrijfs gegevens magazijn toepassing verwerkt grote hoeveel heden gegevens. |Bijna realtime toepassingen die directe reacties op aanvragen van gebruikers vereisen, zoals online gaming. |
| **Prestatie factoren** | &nbsp; | &nbsp; | &nbsp; |
| **I/o-grootte** |Bij een kleinere IO-grootte wordt een hogere IOPS als resultaat verkregen. |Grotere IO-grootte om een hogere door voer te leveren. | &nbsp;|
| **VM-grootte** |Gebruik een VM-grootte die IOPS groter is dan de vereiste van uw toepassing. |Gebruik een VM-grootte met een doorvoer limiet van meer dan uw toepassings vereiste. |Gebruik een VM-grootte die groter is dan de vereiste schaal limieten. |
| **Schijf grootte** |Gebruik een schijf grootte met IOPS die groter is dan de vereiste van uw toepassing. |Gebruik een schijf grootte met een doorvoer limiet van meer dan uw toepassings vereiste. |Gebruik een schijf grootte die hoger is dan de vereiste voor uw toepassing. |
| **Schaal limieten voor VM'S en schijven** |De limiet voor IOPS van de gekozen VM-grootte moet groter zijn dan het totale aantal IOPS dat wordt aangestuurd door de opslag schijven die eraan zijn gekoppeld. |De doorvoer limiet van de gekozen VM-grootte moet groter zijn dan de totale door Voer die wordt aangestuurd door de Premium-opslag schijven die eraan zijn gekoppeld. |De schaal limieten van de gekozen VM-grootte moeten groter zijn dan de totale schaal limieten van gekoppelde Premium Storage-schijven. |
| **Schijf cache** |Schakel alleen-lezen cache op Premium-opslag schijven in met zware bewerkingen om meer Lees-IOPS te krijgen. | &nbsp; |Schakel alleen-lezen cache op Premium-opslag schijven met kant-en-klare bewerkingen uit om zeer weinig lees latentie te verkrijgen. |
| **Schijf-striping** |Gebruik meerdere schijven en strip deze samen om een gecombineerde hogere IOPS en doorvoer limiet te verkrijgen. De gecombineerde limiet per VM moet hoger zijn dan de gecombineerde limieten van gekoppelde Premium-schijven. | &nbsp; | &nbsp; |
| **Streepgrootte** |Kleinere Stripe-grootte voor wille keurig klein i/o-patroon dat wordt weer gegeven in OLTP-toepassingen. Gebruik bijvoorbeeld Stripe-grootte van 64 KB voor SQL Server OLTP-toepassing. |Grotere Stripe-grootte voor het sequentiële grote i/o-patroon dat wordt weer gegeven in Data Warehouse-toepassingen. Gebruik bijvoorbeeld 256 KB-Stripe-grootte voor SQL Server Data Warehouse-toepassing. | &nbsp; |
| **Multithreading** |Gebruik multi-threading om een hoger aantal aanvragen te pushen naar Premium Storage dat leidt tot hogere IOPS en door voer. U kunt bijvoorbeeld op SQL Server een hoge MAXDOP-waarde instellen om meer Cpu's toe te wijzen aan SQL Server. | &nbsp; | &nbsp; |
| **Wachtrij diepte** |Grotere wachtrij diepte levert hogere IOPS op. |Grotere wachtrij diepte levert een hogere door voer. |Een kleinere wachtrij diepte levert lagere latenties. |

## <a name="nature-of-io-requests"></a>Aard van i/o-aanvragen

Een i/o-aanvraag is een eenheid voor invoer/uitvoer die door uw toepassing wordt uitgevoerd. Het identificeren van de aard van de i/o-aanvragen, wille keurige of sequentiële, lees-of schrijf bewerkingen, kleine of grote, helpt u bij het bepalen van de prestatie vereisten van uw toepassing. Het is belang rijk om inzicht te krijgen in de aard van IO-aanvragen om de juiste beslissingen te nemen bij het ontwerpen van de infra structuur van uw toepassing. IOs moet gelijkmatig worden gedistribueerd om de beste prestaties mogelijk te maken.

I/o-grootte is een van de belang rijke factoren. De i/o-grootte is de grootte van de invoer-en uitvoer bewerkings aanvraag die wordt gegenereerd door uw toepassing. De i/o-grootte heeft een grote invloed op de prestaties, met name op de IOPS en band breedte die de toepassing kan krijgen. In de volgende formule ziet u de relatie tussen IOPS, i/o-grootte en band breedte/door voer.  
    ![Een diagram van de vergelijking van de I-P S maal de I/O-grootte is gelijk aan de door voer.](media/premium-storage-performance/image1.png)

Sommige toepassingen bieden u de mogelijkheid om hun IO-grootte te wijzigen, terwijl sommige toepassingen dat niet doen. SQL Server bepaalt bijvoorbeeld de optimale IO-grootte zelf, en biedt gebruikers geen knoppen om deze te wijzigen. Anderzijds biedt Oracle een para meter met de naam [DB- \_ blok \_ grootte](https://docs.oracle.com/cd/B19306_01/server.102/b14211/iodesign.htm#i28815) , waarmee u de I/O-aanvraag grootte van de Data Base kunt configureren.

Als u een toepassing gebruikt die u niet toestaat de i/o-grootte te wijzigen, gebruikt u de richt lijnen in dit artikel om de prestatie-KPI te optimaliseren die het meest relevant is voor uw toepassing. Bijvoorbeeld:

* Een OLTP-toepassing genereert miljoenen kleine en wille keurige i/o-aanvragen. Als u deze typen i/o-aanvragen wilt verwerken, moet u uw toepassings infrastructuur ontwerpen om hogere IOPS te verkrijgen.  
* Een toepassing voor gegevens opslag genereert grote en sequentiële IO-aanvragen. Als u deze typen i/o-aanvragen wilt verwerken, moet u uw toepassings infrastructuur ontwerpen om een hogere band breedte of door voer te krijgen.

Als u een toepassing gebruikt, waarmee u de i/o-grootte kunt wijzigen, gebruikt u deze vuist regel voor de i/o-grootte naast andere richt lijnen voor prestaties.

* Kleinere IO-grootte om meer IOPS te verkrijgen. Bijvoorbeeld 8 KB voor een OLTP-toepassing.  
* Grotere IO-grootte voor een hogere band breedte/door voer. Bijvoorbeeld 1024 KB voor een Data Warehouse-toepassing.

Hier volgt een voor beeld van hoe u de IOPS en de door Voer/band breedte kunt berekenen voor uw toepassing. Overweeg een toepassing met behulp van een P30-schijf. Het maximale aantal IOPS en de door Voer/band breedte van een P30-schijf kan worden gerealiseerd op respectievelijk 5000 IOPS en 200 MB per seconde. Als uw toepassing nu het maximale aantal IOPS van de P30-schijf vereist en u een kleinere IO-grootte gebruikt, zoals 8 KB, kan de resulterende band breedte Maxi maal 40 MB per seconde zijn. Als voor uw toepassing echter de maximale door Voer/band breedte van P30 schijf is vereist en u een grotere IO-grootte gebruikt, zoals 1024 KB, is de resulterende IOPS minder, 200 IOPS. Stel daarom de i/o-grootte zodanig in dat deze voldoet aan de limiet voor IOPS en door Voer/band breedte van uw toepassing. De volgende tabel bevat een overzicht van de verschillende i/o-grootten en de bijbehorende IOPS en door Voer voor een P30-schijf.

| Toepassings vereiste | I/O-grootte | IOPS | Door Voer/band breedte |
| --- | --- | --- | --- |
| Max. IOPS |8 kB |5\.000 |40 MB per seconde |
| Maximale door Voer |1024 KB |200 |200 MB per seconde |
| Maximale door Voer + hoge IOPS |64 kB |3.200 |200 MB per seconde |
| Maximale IOPS + hoge door Voer |32 kB |5\.000 |160 MB per seconde |

Als u IOPS en band breedte wilt ophalen die hoger is dan de maximum waarde van één Premium-opslag schijf, gebruikt u meerdere Premium-schijven die met elkaar zijn gesegmenteerd. U kunt bijvoorbeeld twee P30-schijven opstrepen om een gecombineerde IOPS van 10.000 IOPS te verkrijgen of een gecombineerde door Voer van 400 MB per seconde. Zoals uitgelegd in de volgende sectie, moet u een VM-grootte gebruiken die ondersteuning biedt voor de gecombineerde schijf-IOPS en-door voer.

> [!NOTE]
> Wanneer u meerdere IOPS of door Voer verhoogt, neemt de andere ook toe, moet u ervoor zorgen dat u de limieten voor door Voer of IOPS van de schijf of de virtuele machine niet bereikt wanneer een van beide wordt verhoogd.

Als u de gevolgen van de i/o-grootte voor de prestaties van de toepassing wilt Witness, kunt u benchmarking-hulpprogram ma's uitvoeren op uw VM en schijven. Maak meerdere test uitvoeringen en gebruik een andere i/o-grootte voor elke uitvoering om de impact te bekijken. Raadpleeg het artikel over benchmarking, gekoppeld aan het einde, voor meer informatie.

## <a name="high-scale-vm-sizes"></a>VM-grootten hoog schalen

Wanneer u begint met het ontwerpen van een toepassing, selecteert u een virtuele machine om uw toepassing te hosten. Premium Storage wordt geleverd met grootschalige VM-grootten die toepassingen kunnen uitvoeren die een hogere reken kracht vereisen en een hoge I/O-prestaties van de lokale schijf. Deze Vm's bieden snellere processors, een hogere geheugen-naar-kern-verhouding en een Solid-State station (SSD) voor de lokale schijf. Voor beelden van virtuele machines met een hoge schaal ondersteuning die Premium Storage zijn de virtuele machines uit de DS-en GS-serie.

Virtuele machines met een hoge schaal zijn beschikbaar in verschillende groottes met een verschillend aantal CPU-kernen, geheugen, besturings systeem en tijdelijke schijf grootte. Elke VM-grootte heeft ook een maximum aantal gegevens schijven dat u kunt koppelen aan de virtuele machine. Daarom is de gekozen VM-grootte van invloed op de hoeveelheid verwerking, het geheugen en de opslag capaciteit die beschikbaar is voor uw toepassing. Dit is ook van invloed op de reken-en opslag kosten. Hieronder ziet u bijvoorbeeld de specificaties van de grootste VM-grootte in een DS-reeks en een GS-serie:

| VM-grootte | CPU-kernen | Geheugen | VM-schijf grootten | Met maximaal gegevens schijven | Cachegrootte | IOPS | I/o-limieten van bandbreedte cache |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS14 |16 |112 GB |OS = 1023 GB <br> Lokale SSD = 224 GB |32 |576 GB |50.000 IOPS <br> 512 MB per seconde |4.000 IOPS en 33 MB per seconde |
| Standard_GS5 |32 |448 GB |OS = 1023 GB <br> Lokale SSD = 896 GB |64 |4224 GB |80.000 IOPS <br> 2.000 MB per seconde |5.000 IOPS en 50 MB per seconde |

Zie [grootten voor virtuele machines in azure](sizes.md) of als u een volledige lijst met alle beschik bare Azure VM-grootten wilt weer geven. Kies een VM-grootte die kan voldoen aan de gewenste prestatie vereisten voor de toepassing en deze kan worden geschaald. Daarnaast moet u rekening houden met de volgende belang rijke overwegingen bij het kiezen van VM-grootten.

*Schaal limieten*  
De maximale IOPS-limieten per VM en per schijf zijn verschillend en zijn onafhankelijk van elkaar. Zorg ervoor dat de toepassing IOPS behaalt binnen de limieten van de virtuele machine en de Premium-schijven die eraan zijn gekoppeld. Anders werkt de prestaties van de toepassing met vertraging.

Stel dat een toepassings vereiste een maximum van 4.000 IOPS is. U kunt dit doen door een P30-schijf in te richten op een DS1-VM. De P30-schijf kan Maxi maal 5.000 IOPS leveren. De DS1-VM is echter beperkt tot 3.200 IOPS. De prestaties van de toepassing worden hierdoor beperkt door de VM-limiet van 3.200 IOPS en de prestaties verslechteren. U kunt dit voor komen door een virtuele machine en schijf grootte te kiezen die voldoen aan de vereisten van de toepassing.

*Kosten van bewerking*  
In veel gevallen is het mogelijk dat uw totale kosten voor bewerking met behulp van Premium Storage lager zijn dan het gebruik van standaard opslag.

Denk bijvoorbeeld aan een toepassing die 16.000 IOPS vereist. Als u deze prestaties wilt verzorgen, hebt u een Standard \_ D14 Azure IaaS VM nodig, die een maximale IOPS van 16.000 kan bieden met behulp van 32 Standard Storage 1 TB-schijven. Elke standaard opslag schijf van 1 TB kan Maxi maal 500 IOPS beslaan. De geschatte kosten van deze virtuele machine per maand zijn $1.570. De maandelijkse kosten van 32 standaard opslag schijven zijn $1.638. De geschatte totale maandelijkse kosten worden $3.208.

Als u echter dezelfde toepassing op Premium Storage hebt gehost, hebt u een kleinere VM-grootte en minder Premium-opslag schijven nodig, waardoor de totale kosten worden verminderd. Een standaard \_ DS13-VM kan voldoen aan de 16.000-IOPS-vereiste met behulp van vier P30-schijven. De DS13-VM heeft een maximale IOPS van 25.600 en elke P30-schijf heeft een maximale IOPS van 5.000. Over het algemeen kan deze configuratie 5.000 x 4 = 20.000 IOPS behaalt. De geschatte kosten van deze virtuele machine per maand zijn $1.003. De maandelijkse kosten van vier P30 Premium-opslag schijven worden $544,34. De geschatte totale maandelijkse kosten worden $1.544.

Onderstaande tabel bevat een overzicht van de kosten analyse van dit scenario voor Standard en Premium Storage.

| &nbsp; | **Standard** | **Premium** |
| --- | --- | --- |
| **Kosten van VM per maand** |$1.570,58 (standaard \_ D14) |$1.003,66 (standaard \_ DS13) |
| **Kosten van schijven per maand** |$1.638,40 (32 x 1 TB schijven) |$544,34 (4 x P30 schijven) |
| **Totale kosten per maand** |$3.208,98 |$1.544,34 |

*Linux-distributies*  

Met Azure Premium Storage beschikt u over hetzelfde prestatie niveau voor Vm's met Windows en Linux. We ondersteunen veel soorten Linux-distributies en u kunt [hier](linux/endorsed-distros.md)de volledige lijst zien. Het is belang rijk te weten dat verschillende distributies beter geschikt zijn voor verschillende soorten workloads. U ziet verschillende prestatie niveaus, afhankelijk van de distributie waarop uw werk belasting wordt uitgevoerd. Test de Linux-distributies met uw toepassing en kies de versie die het beste werkt.

Wanneer u Linux met Premium Storage uitvoert, controleert u de meest recente updates over de vereiste Stuur Programma's om hoge prestaties te garanderen.

## <a name="premium-storage-disk-sizes"></a>Grootte van Premium-opslag schijven

Azure Premium Storage biedt tal van grootten, zodat u er een kunt kiezen die het beste bij uw behoeften past. Elke schijf grootte heeft een andere schaal limiet voor IOPS, band breedte en opslag. Kies de juiste Premium Storage schijf grootte, afhankelijk van de toepassings vereisten en de grootte van de VM met hoge schaal. In de volgende tabel ziet u de grootte van de schijven en de mogelijkheden ervan. P4-, P6-, P15-, P60-, P70-en P80-grootten worden momenteel alleen ondersteund voor Managed Disks.

[!INCLUDE [disk-storage-premium-ssd-sizes](../../includes/disk-storage-premium-ssd-sizes.md)]

Hoeveel schijven u kiest, is afhankelijk van de gekozen schijf grootte. U kunt één P50-schijf of meerdere P10-schijven gebruiken om te voldoen aan de vereisten van uw toepassing. Houd rekening met onderstaande aandachtspunten wanneer u de keuze maakt.

*Schaal limieten (IOPS en door Voer)*  
De limieten voor IOPS en door Voer van elke Premium-schijf variëren en zijn onafhankelijk van de schaal limieten van de virtuele machine. Zorg ervoor dat het totale aantal IOPS en door Voer van de schijven binnen de schaal limieten ligt van de gekozen VM-grootte.

Als een toepassings vereiste bijvoorbeeld Maxi maal 250 MB/sec. door Voer is en u een DS4-VM met één P30-schijf gebruikt. De DS4-VM kan Maxi maal 256 MB/sec. door voer geven. Eén P30-schijf heeft echter een doorvoer limiet van 200 MB/sec. De toepassing wordt hierdoor beperkt tot 200 MB/sec als gevolg van de schijf limiet. U kunt deze limiet verheffen door meer dan één gegevens schijf aan de virtuele machine in te richten of de grootte van de schijven aan te P40 of P50.

> [!NOTE]
> Lees bewerkingen die door de cache worden geleverd, zijn niet opgenomen in de schijf-IOPS en door Voer, en zijn daarom niet van toepassing op schijf limieten. Cache heeft afzonderlijke IOPS-en doorvoer limieten per VM.
>
> Aanvankelijk zijn uw lees-en schrijf bewerkingen bijvoorbeeld respectievelijk 60MB per seconde en 40MB per seconde. Na verloop van tijd wordt de cache opwarmd en worden meer en meer Lees bewerkingen van de cache gebruikt. Daarna kunt u een hogere schrijf doorvoer verkrijgen van de schijf.

*Aantal schijven*  
Bepaal het aantal schijven dat u nodig hebt door de toepassings vereisten te beoordelen. Elke VM-grootte heeft ook een limiet voor het aantal schijven dat u kunt koppelen aan de virtuele machine. Doorgaans is dit het aantal kernen. Zorg ervoor dat de grootte van de virtuele machine die u kiest, het benodigde aantal schijven kan ondersteunen.

Houd er rekening mee dat de Premium Storage schijven betere prestaties hebben ten opzichte van standaard opslag schijven. Als u uw toepassing migreert vanuit een Azure IaaS-VM met standaard opslag naar Premium Storage, hebt u waarschijnlijk minder Premium-schijven nodig om dezelfde of hogere prestaties voor uw toepassing te krijgen.

## <a name="disk-caching"></a>Schijfcaching

High Scale-Vm's die gebruikmaken van Azure Premium Storage, hebben een cache technologie met meerdere lagen met de naam BlobCache. BlobCache maakt gebruik van een combi natie van de host-RAM en lokale SSD voor caching. Deze cache is beschikbaar voor de Premium Storage permanente schijven en de lokale VM-schijven. Deze cache-instelling is standaard ingesteld op lezen/schrijven voor besturingssysteem schijven en alleen-lezen voor gegevens schijven die worden gehost op Premium Storage. Als schijf cache is ingeschakeld op de Premium Storage schijven, kunnen de virtuele machines met een hoge schaal extreem hoge prestatie niveaus hebben die de onderliggende schijf prestaties overschrijden.

> [!WARNING]
> Schijf cache gebruik wordt niet ondersteund voor schijven van 4 TiB en groter. Als er meerdere schijven aan uw virtuele machine zijn gekoppeld, ondersteunt elke schijf die kleiner is dan 4 TiB de cache.
>
> Wanneer u de cache-instelling van een Azure-schijf wijzigt, wordt de doel schijf losgekoppeld en opnieuw gekoppeld. Als de schijf van het besturings systeem is, wordt de VM opnieuw opgestart. Stop alle toepassingen en services die kunnen worden beïnvloed door deze onderbreking voordat u de schijfcache-instelling wijzigt. Het volgen van deze aanbevelingen kan leiden tot gegevens beschadiging.

Raadpleeg het blog bericht over [Azure Premium Storage](https://azure.microsoft.com/blog/azure-premium-storage-now-generally-available-2/) voor meer informatie over de werking van BlobCache.

Het is belang rijk om cache in te scha kelen op de juiste set schijven. Of u schijf cache gebruik moet inschakelen op een Premium-schijf of niet afhankelijk is van het werkbelasting patroon dat wordt verwerkt door de schijf. De volgende tabel bevat de standaard cache-instellingen voor besturings systeem-en gegevens schijven.

| **Schijftype** | **Standaard cache-instelling** |
| --- | --- |
| Besturingssysteemschijf |ReadWrite |
| Gegevensschijf |ReadOnly |

Hieronder vindt u de aanbevolen schijf cache-instellingen voor gegevens schijven,

| **Cache-instelling voor schijf** | **aanbeveling voor het gebruik van deze instelling** |
| --- | --- |
| Geen |Configureer host-cache als geen voor schijven met alleen-schrijven en write-zware. |
| ReadOnly |Configureer de host-cache als alleen-lezen voor de schijven met het kenmerk alleen-schrijven en lees-en schrijf bewerkingen. |
| ReadWrite |Configureer de host-cache als ReadWrite alleen als uw toepassing het schrijven van gegevens in de cache op de juiste wijze afhandelt naar permanente schijven als dat nodig is. |

*Kenmerk*  
Door het configureren van alleen-lezen cache op Premium Storage gegevens schijven, kunt u een lage lees latentie bereiken en zeer grote Lees-IOPS en door Voer voor uw toepassing verkrijgen. Dit kan twee oorzaken hebben:

1. Lees bewerkingen van de cache, die zich op het geheugen van de virtuele machine en lokale SSD bevindt, zijn veel sneller dan lees bewerkingen van de gegevens schijf, die zich in de Azure Blob-opslag bevindt.  
1. Premium Storage telt niet de Lees bewerkingen van de cache, naar de schijf-IOPS en door voer. Uw toepassing kan daarom een groter aantal IOPS en door Voer bezorgen.

*ReadWrite*  
Op de besturingssysteem schijven is ReadWrite-caching standaard ingeschakeld. We hebben onlangs ondersteuning toegevoegd voor ReadWrite-caching op gegevens schijven. Als u gebruik wilt maken van ReadWrite cache, moet u de juiste manier hebben om de gegevens van de cache naar permanente schijven te schrijven. SQL Server bijvoorbeeld het schrijven van gegevens in de cache naar de permanente opslag schijven. Het gebruik van de ReadWrite-cache met een toepassing die het persistent maken van de vereiste gegevens niet verwerkt, kan leiden tot verlies van gegevens, als de virtuele machine vastloopt.

*Geen*  
Op dit moment wordt **geen** ondersteund op gegevens schijven. Het wordt niet ondersteund op besturingssysteem schijven. Als u **geen** op een besturingssysteem schijf instelt, wordt deze intern overschreven en ingesteld op **alleen-lezen**.

Als voor beeld kunt u deze richt lijnen Toep assen op SQL Server die worden uitgevoerd op Premium Storage door het volgende te doen:

1. Alleen-lezen cache configureren op Premium-opslag schijven die fungeren als host voor gegevens bestanden.  
   a.  De snelle lees bewerkingen uit de cache verlaagt de SQL Server query tijd omdat gegevens pagina's veel sneller worden opgehaald uit de cache, vergeleken met rechtstreeks van de gegevens schijven.  
   b.  Als er Lees bewerkingen van de cache worden door gegeven, betekent dit dat er extra door Voer beschikbaar is van Premium-gegevens schijven. SQL Server kunt deze extra door Voer voor het ophalen van meer gegevens pagina's en andere bewerkingen, zoals back-up maken/herstellen, batch belastingen en indexen opnieuw samen stellen.  
1. Geen cache configureren op Premium-opslag schijven die de logboek bestanden hosten.  
   a.  Logboek bestanden hebben voornamelijk schrijf-zware bewerkingen. Daarom profiteren ze niet van de alleen-lezen cache.

## <a name="optimize-performance-on-linux-vms"></a>Prestaties op Linux Vm's optimaliseren

Voor alle Premium-Ssd's of Ultra disks kunt u ' obstakels ' voor bestands systemen op de schijf uitschakelen om de prestaties te verbeteren wanneer het bekend is dat er geen caches zijn die gegevens kunnen verliezen.  Als de cache van Azure disk is ingesteld op ReadOnly of geen, kunt u belemmeringen uitschakelen.  Maar als caching is ingesteld op ReadWrite, moeten belemmeringen ingeschakeld blijven om de schrijf duurzaamheid te garanderen.  Obstakels zijn doorgaans standaard ingeschakeld, maar u kunt belemmeringen uitschakelen met een van de volgende methoden, afhankelijk van het bestands systeem type:

* Gebruik voor **reiserFS** de optie blok keren = geen koppeling om belemmeringen uit te scha kelen.  Als u belemmeringen expliciet wilt inschakelen, gebruikt u ring = flush.
* Voor **ext3/ext4** gebruikt u de optie blok keren = 0 om belemmeringen uit te scha kelen.  Als u belemmeringen expliciet wilt inschakelen, gebruikt u blok = 1.
* Gebruik voor **xfs** de optie voor het koppelen van de ring om belemmeringen uit te scha kelen.  Gebruik een barrière om belemmeringen expliciet in te scha kelen.  Houd er rekening mee dat in latere versies van Linux-kernel het ontwerp van XFS-bestands systeem altijd zorgt voor duurzaamheid en het uitschakelen van belemmeringen geen effect heeft.  

## <a name="disk-striping"></a>Schijf striping

Wanneer een grootschalige virtuele machine is gekoppeld aan verschillende permanente schijven van Premium Storage, kunnen de schijven samen worden gestripd om hun IOPs, band breedte en opslag capaciteit samen te voegen.

In Windows kunt u opslag ruimten gebruiken om schijven samen te strepen. U moet één kolom configureren voor elke schijf in een groep. Anders kan de algehele prestaties van het striped volume lager zijn dan verwacht, vanwege een ongelijke distributie van verkeer op de schijven.

Belang rijk: het gebruik van Serverbeheer gebruikers interface kunt u het totale aantal kolommen instellen op 8 voor een striped volume. Wanneer u meer dan acht schijven koppelt, gebruikt u Power shell om het volume te maken. Met behulp van Power shell kunt u het aantal kolommen instellen dat gelijk is aan het aantal schijven. Als er bijvoorbeeld 16 schijven in één stripeset zijn ingesteld; Geef 16 kolommen op in de para meter *NumberOfColumns* van de Power shell-cmdlet *New-VirtualDisk* .

Gebruik in Linux het MDADM-hulp programma om schijven samen te strippen. Zie [Software RAID op Linux configureren](/previous-versions/azure/virtual-machines/linux/configure-raid)voor gedetailleerde stappen voor het verwijderen van schijven in Linux.

*Streepgrootte*  
Een belang rijke configuratie van schijf striping is de Stripe-grootte. De Stripe-grootte of blok grootte is het kleinste deel van de gegevens dat een toepassing op een striped volume kan adresseren. De Stripe-grootte die u configureert, is afhankelijk van het type toepassing en het bijbehorende aanvraag patroon. Als u de verkeerde Stripe-grootte kiest, kan dit leiden tot een onjuiste uitlijning van IO, wat leidt tot verminderde prestaties van uw toepassing.

Als een i/o-aanvraag die door uw toepassing is gegenereerd, groter is dan de schijf Stripe-grootte, wordt deze door het opslag systeem naar de grenzen van Stripe-eenheden op meer dan één schijf geschreven. Wanneer het tijd is om toegang te krijgen tot deze gegevens, moet er meer dan één Stripe-eenheid worden verzameld om de aanvraag te volt ooien. Het cumulatieve effect van dit gedrag kan leiden tot aanzienlijke prestatie vermindering. Aan de andere kant, als de IO-aanvraag grootte kleiner is dan de Stripe-grootte en als deze wille keurige aard is, kunnen de i/o-aanvragen op dezelfde schijf worden toegevoegd die een knel punt veroorzaken en uiteindelijk de i/o-prestaties verlagen.

Afhankelijk van het type werk belasting dat door uw toepassing wordt uitgevoerd, kiest u de juiste Stripe-grootte. Gebruik voor wille keurige kleine i/o-aanvragen een kleinere Stripe-grootte. Dat voor grote sequentiële IO-aanvragen een grotere Stripe-grootte gebruiken. Bekijk de aanbevelingen voor de Stripe-grootte voor de toepassing die u wilt uitvoeren op Premium Storage. Configureer voor SQL Server de Stripe-grootte van 64 KB voor OLTP-workloads en 256 KB voor werk belastingen voor gegevens opslag. Zie [Aanbevolen procedures voor de prestaties van SQL Server op virtuele machines van Azure voor](../azure-sql/virtual-machines/windows/performance-guidelines-best-practices.md#disks-guidance) meer informatie.

> [!NOTE]
> U kunt Maxi maal 32 Premium-opslag schijven samen voegen op een DS-serie VM en 64 Premium-opslag schijven op een GS-serie VM.

## <a name="multi-threading"></a>Meerdere threads

Azure heeft Premium Storage platform ontworpen om enorm parallel te zijn. Daarom behaalt een toepassing met meerdere threads veel hogere prestaties dan een toepassing met één thread. Een toepassing met meerdere threads splitst de taken over meerdere threads en verhoogt de efficiëntie van de uitvoering door gebruik te maken van de virtuele machine en schijf bronnen tot het maximum.

Als uw toepassing bijvoorbeeld wordt uitgevoerd op een virtuele machine met één kern met twee threads, kan de CPU scha kelen tussen de twee threads om efficiency te maken. Terwijl één thread wacht op een schijf-IO om te volt ooien, kan de CPU overschakelen naar de andere thread. Op deze manier kunnen twee threads meer dan één thread uitvoeren. Als de virtuele machine meer dan één kern heeft, wordt de uitvoerings tijd verder verlaagd, omdat elke kern gelijktijdig taken kan uitvoeren.

U kunt de manier waarop een externe toepassing gebruikmaakt van één threading of meerdere threads mogelijk niet wijzigen. SQL Server kan bijvoorbeeld multi-CPU en meerdere kernen verwerken. SQL Server beslist echter onder welke voor waarden een of meer threads worden gebruikt voor het verwerken van een query. Het kan query's uitvoeren en indexen bouwen met behulp van meerdere threads. Voor een query waarbij wordt samengevoegd bij het samen voegen van grote tabellen en het sorteren van gegevens voordat deze naar de gebruiker worden geretourneerd, gebruikt SQL Server waarschijnlijk meerdere threads. Een gebruiker kan echter niet bepalen of SQL Server een query uitvoert met behulp van een enkele thread of meerdere threads.

Er zijn configuratie-instellingen die u kunt wijzigen om deze multi-threading of parallelle verwerking van een toepassing te beïnvloeden. In het geval van SQL Server is het bijvoorbeeld de maximale mate van parallelle configuratie. Met deze instelling MAXDOP kunt u het maximum aantal processors configureren dat SQL Server kunnen gebruiken bij parallelle verwerking. U kunt MAXDOP configureren voor afzonderlijke query's of index bewerkingen. Dit is handig als u resources van uw systeem wilt verdelen over een prestatie kritieke toepassing.

Stel bijvoorbeeld dat uw toepassing gebruikmaakt van SQL Server een grote query en een index bewerking tegelijk uitvoert. We gaan ervan uit dat u wilt dat de index bewerking meer presteert vergeleken met de grote query. In een dergelijk geval kunt u de MAXDOP-waarde van de index bewerking hoger instellen dan de waarde voor MAXDOP voor de query. Op deze manier heeft SQL Server een groter aantal processors dat het kan gebruiken voor de index bewerking vergeleken met het aantal processors dat kan worden gebruikt voor de grote query. Houd er rekening mee dat u het aantal threads dat SQL Server gebruikt voor elke bewerking, niet beheert. U kunt het maximum aantal processors bepalen dat specifiek is voor meerdere threads.

Meer informatie over de [mate van parallelle](/previous-versions/sql/sql-server-2008-r2/ms188611(v=sql.105)) uitvoering in SQL Server. Lees deze instellingen die van invloed zijn op meerdere threads in uw toepassing en de bijbehorende configuraties om de prestaties te optimaliseren.

## <a name="queue-depth"></a>Wachtrij diepte

De wachtrij diepte of wachtrij lengte of wachtrij grootte is het aantal i/o-aanvragen in behandeling in het systeem. De waarde van de wachtrij diepte bepaalt het aantal i/o-bewerkingen dat uw toepassing kan uitlijnen, waardoor de opslag schijven zullen worden verwerkt. Dit is van invloed op alle drie de prestatie-indica toren van toepassingen die in dit artikel worden besproken, namelijk IOPS, door Voer en latentie.

De lengte van de wachtrij en meerdere threads zijn nauw verwant. De waarde van de wachtrij diepte geeft aan hoeveel multi-threading kan worden bereikt door de toepassing. Als de wachtrij diepte groot is, kan de toepassing meer bewerkingen gelijktijdig uitvoeren, met andere woorden, meer multi threading. Als de wachtrij diepte klein is, zelfs als de toepassing meerdere threads heeft, heeft deze niet genoeg aanvragen voor gelijktijdige uitvoering.

Normaal gesp roken is het niet toegestaan om de wachtrij diepte te wijzigen, omdat als dat niet het geval is, het meer schade zal opleveren dan goed. Toepassingen stellen de juiste waarde voor de wachtrij diepte in om de optimale prestaties te verkrijgen. Het is echter belang rijk dat u dit concept begrijpt, zodat u prestatie problemen met uw toepassing kunt oplossen. U kunt ook de effecten van de wachtrij diepte observeren door Bench Mark-hulpprogram ma's uit te voeren op uw systeem.

Sommige toepassingen bieden instellingen die van invloed zijn op de wachtrij diepte. Bijvoorbeeld, de instelling MAXDOP (maximale mate van parallellisme) in SQL Server beschreven in de vorige sectie. MAXDOP is een manier om de diepte van de wachtrij en meerdere threads te beïnvloeden, hoewel de waarde voor de wachtrij diepte van SQL Server niet rechtstreeks wordt gewijzigd.

*Hoge wachtrij diepte*  
Een hoge wachtrij diepte regelt meer bewerkingen op de schijf. De schijf kent de volgende aanvraag in de wachtrij van tevoren. Daarom kan de schijf bewerkingen vooraf plannen en ze in een optimale volg orde verwerken. Omdat de toepassing meer aanvragen naar de schijf verzendt, kan de schijf meer parallelle IOs-apparaten verwerken. Uiteindelijk kan de toepassing hogere IOPS behaalt. Omdat de toepassing meer aanvragen verwerkt, neemt de totale door Voer van de toepassing ook toe.

Normaal gesp roken kan een toepassing maximale door Voer bieden met 8-16 + uitmuntende IOs per gekoppelde schijf. Als er een wachtrij diepte is, pusht de toepassing niet genoeg IOs naar het systeem en wordt minder hoeveelheid in een bepaalde periode verwerkt. Met andere woorden, minder door voer.

Als u bijvoorbeeld in SQL Server de MAXDOP-waarde voor een query op ' 4 ' informeert SQL Server dat deze Maxi maal vier kernen kan gebruiken om de query uit te voeren. SQL Server bepaalt wat de beste waarde is voor de wachtrij diepte en het aantal kernen voor het uitvoeren van query's.

*Optimale wachtrij diepte*  
Een zeer hoge wachtrij diepte waarde heeft ook de nadelen. Als de waarde voor de wachtrij diepte te hoog is, probeert de toepassing zeer hoge IOPS te bezorgen. Tenzij de toepassing permanente schijven met voldoende ingerichte IOPS heeft, kan dit een negatieve invloed hebben op de latentie van de toepassing. In de volgende formule ziet u de relatie tussen IOPS, latentie en wachtrij diepte.  
    ![Een diagram van de vergelijking van de I O S-tijden van de k-waarde is gelijk aan de wachtrij diepte.](media/premium-storage-performance/image6.png)

U moet de wachtrij diepte niet configureren voor een hoge waarde, maar voor een optimale waarde, waardoor voldoende IOPS voor de toepassing kan worden geleverd zonder dat dit gevolgen heeft voor de latentie. Als de latentie van de toepassing bijvoorbeeld 1 milliseconde moet zijn, is de wachtrij diepte vereist voor het maken van 5.000 IOPS, wachtrij diepte = 5000 x 0,001 = 5.

*Wachtrij diepte voor striped volume*  
Voor een striped volume moet u een hoge wachtrij diepte hebben, zodat elke schijf afzonderlijk een piek wachtrij heeft. Denk bijvoorbeeld aan een toepassing die een wachtrij diepte van 2 duwt en er vier schijven in de Stripe zijn. De twee i/o-aanvragen gaan naar twee schijven en de resterende twee schijven worden niet-actief. Configureer daarom de wachtrij diepte zodanig dat alle schijven bezet kunnen zijn. Onderstaande formule laat zien hoe u de wachtrij diepte van striped volumes kunt bepalen.  
    ![Een diagram met de vergelijking Q D per schijf aantal kolommen per volume is gelijk aan Q D van het striped volume.](media/premium-storage-performance/image7.png)

## <a name="throttling"></a>Beperking

Azure Premium Storage voorziet in een opgegeven aantal IOPS en door Voer, afhankelijk van de grootte van de virtuele machine en de schijf groottes die u kiest. Telkens wanneer uw toepassing probeert te gaan van IOPS of door Voer boven deze limieten van wat de virtuele machine of schijf kan verwerken, Premium Storage deze beperking. Dit manifesteert in de vorm van verminderde prestaties in uw toepassing. Dit kan betekenen hogere latentie, lagere door Voer of lagere IOPS. Als Premium Storage niet wordt beperkt, kan uw toepassing volledig mislukken door te overschrijden wat de bronnen kunnen bereiken. Om prestatie problemen te voor komen vanwege beperking, moet u altijd voldoende resources inrichten voor uw toepassing. Houd rekening met wat we hebben besproken in de secties VM-grootten en schijf grootten hierboven. Benchmarking is de beste manier om erachter te komen welke resources u nodig hebt om uw toepassing te hosten.

## <a name="next-steps"></a>Volgende stappen

Als u een bench Mark-schijf wilt maken, raadpleegt u onze artikelen over de benchmarking van een schijf:

* Voor Linux: [Bench Mark your application on Azure Disk Storage](./disks-benchmarks.md)
* Voor Windows: [benchmarking van een schijf](./disks-benchmarks.md).

Meer informatie over de beschik bare schijf typen:

* Voor Linux: [Selecteer een schijf type](disks-types.md)
* Voor Windows: [Selecteer een schijf type](disks-types.md)

Lees voor SQL Server gebruikers artikelen over best practices voor prestaties voor SQL Server:

* [Aanbevolen procedures voor de prestaties van SQL Server in azure Virtual Machines](../azure-sql/virtual-machines/windows/performance-guidelines-best-practices.md)
* [Azure Premium Storage biedt de beste prestaties voor SQL Server in azure VM](https://cloudblogs.microsoft.com/sqlserver/2015/04/23/azure-premium-storage-provides-highest-performance-for-sql-server-in-azure-vm/)