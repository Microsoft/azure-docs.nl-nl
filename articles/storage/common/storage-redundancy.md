---
title: De gegevensredundantie
titleSuffix: Azure Storage
description: Inzicht in gegevens redundantie in Azure Storage. Gegevens in uw Microsoft Azure Storage worden gerepliceerd voor duurzaamheid en hoge beschikbaarheid.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 04/16/2021
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 3665421ddbdd9cf079ff4aab9377fc9164a1599c
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107575357"
---
# <a name="azure-storage-redundancy"></a>Azure Storage-redundantie

Azure Storage slaat altijd meerdere kopieën van uw gegevens op, zodat deze worden beschermd tegen geplande en ongeplande gebeurtenissen, waaronder tijdelijke hardwarestoringen, netwerk- of stroomstoringen en grote natuurrampen. Redundantie zorgt ervoor dat uw opslagaccount voldoet aan de beschikbaarheids- en duurzaamheidsdoelen, zelfs bij storingen.

Wanneer u besluit welke redundantieoptie het beste is voor uw scenario, moet u rekening houden met de balans tussen lagere kosten en hogere beschikbaarheid. De factoren die u helpen bepalen welke redundantieoptie u moet kiezen, zijn onder andere:  

- Hoe uw gegevens worden gerepliceerd in de primaire regio
- Of uw gegevens worden gerepliceerd naar een tweede regio die geografisch ver van de primaire regio ligt, ter bescherming tegen regionale rampen
- Of uw toepassing leestoegang vereist tot de gerepliceerde gegevens in de secundaire regio als de primaire regio om een of andere reden niet meer beschikbaar is

## <a name="redundancy-in-the-primary-region"></a>Redundantie in de primaire regio

Gegevens in een Azure Storage worden altijd drie keer gerepliceerd in de primaire regio. Azure Storage biedt twee opties voor het repliceren van uw gegevens in de primaire regio:

- **Met lokaal redundante opslag (LRS)** worden uw gegevens drie keer synchroon gekopieerd binnen één fysieke locatie in de primaire regio. LRS is de minst dure replicatieoptie, maar wordt niet aanbevolen voor toepassingen die hoge beschikbaarheid vereisen.
- **Zone-redundante opslag (ZRS)** kopieert uw gegevens synchroon naar drie Azure-beschikbaarheidszones in de primaire regio. Voor toepassingen waarvoor hoge beschikbaarheid is vereist, raadt Microsoft aan om ZRS in de primaire regio te gebruiken en ook te repliceren naar een secundaire regio.

> [!NOTE]
> Microsoft raadt aan om ZRS te gebruiken in de primaire regio voor Azure Data Lake Storage Gen2 workloads.

### <a name="locally-redundant-storage"></a>Lokaal redundante opslag

Met lokaal redundante opslag (LRS) worden uw gegevens drie keer gerepliceerd binnen één datacenter in de primaire regio. LRS biedt ten minste 99,99999999999% (11 negens) duurzaamheid van objecten gedurende een bepaald jaar.

LRS is de voordeligste redundantieoptie en biedt de minste duurzaamheid in vergelijking met andere opties. LRS beschermt uw gegevens tegen serverrek- en schijffouten. Als er echter een noodstroom optreedt, zoals brand of overstromingen in het datacenter, kunnen alle replica's van een opslagaccount met LRS verloren gaan of kunnen ze niet worden teruggeplaatst. Om dit risico te beperken, raadt Microsoft aan om [zone-redundante](#zone-redundant-storage) opslag (ZRS), [geografisch redundante](#geo-redundant-storage) opslag (GRS) of [geografisch zone-redundante](#geo-zone-redundant-storage) opslag (GZRS) te gebruiken.

Een schrijfaanvraag voor een opslagaccount dat LRS gebruikt, gebeurt synchroon. De schrijfbewerking wordt pas succesvol uitgevoerd nadat de gegevens naar alle drie de replica's zijn geschreven.

In het volgende diagram ziet u hoe uw gegevens worden gerepliceerd binnen één datacenter met LRS:

:::image type="content" source="media/storage-redundancy/locally-redundant-storage.png" alt-text="Diagram waarin wordt weergegeven hoe gegevens worden gerepliceerd in één datacenter met LRS":::

LRS is een goede keuze voor de volgende scenario's:

- Als uw toepassing gegevens op slaat die eenvoudig kunnen worden gereconstrueerd als er gegevensverlies optreedt, kunt u kiezen voor LRS.
- Als uw toepassing is beperkt tot het repliceren van gegevens alleen binnen een land of regio vanwege vereisten voor gegevensbeheer, kunt u kiezen voor LRS. In sommige gevallen kunnen de gekoppelde regio's waarin de gegevens geo-gerepliceerd zijn zich in een ander land of een andere regio. Zie Azure-regio's voor meer informatie over [gekoppelde regio's.](https://azure.microsoft.com/regions/)

### <a name="zone-redundant-storage"></a>Zone-redundante opslag

Met zone-redundante opslag (ZRS) worden uw Azure Storage synchroon gerepliceerd naar drie Azure-beschikbaarheidszones in de primaire regio. Elke beschikbaarheidszone is een afzonderlijke fysieke locatie met onafhankelijke voeding, koeling en netwerken. ZRS biedt duurzaamheid voor Azure Storage gegevensobjecten van ten minste 99,9999999999% (12 negens) gedurende een bepaald jaar.

Met ZRS zijn uw gegevens nog steeds toegankelijk voor lees- en schrijfbewerkingen, zelfs als een zone niet meer beschikbaar is. Als een zone niet meer beschikbaar is, voert Azure netwerkupdates uit, zoals dns-re-pointing. Deze updates kunnen van invloed zijn op uw toepassing als u toegang hebt tot gegevens voordat de updates zijn voltooid. Volg bij het ontwerpen van toepassingen voor ZRS de procedures voor de afhandeling van tijdelijke fouten, waaronder het implementeren van beleid voor opnieuw proberen met exponentieel afschaf.

Een schrijfaanvraag naar een opslagaccount dat ZRS gebruikt, gebeurt synchroon. De schrijfbewerking retourneert pas nadat de gegevens naar alle replica's in de drie beschikbaarheidszones zijn geschreven.

Microsoft raadt aan om ZRS in de primaire regio te gebruiken voor scenario's waarvoor consistentie, duurzaamheid en hoge beschikbaarheid is vereist. ZRS wordt ook aanbevolen voor het beperken van de replicatie van gegevens naar binnen een land of regio om te voldoen aan de vereisten voor gegevensbeheer.

In het volgende diagram ziet u hoe uw gegevens worden gerepliceerd in beschikbaarheidszones in de primaire regio met ZRS:

:::image type="content" source="media/storage-redundancy/zone-redundant-storage.png" alt-text="Diagram waarin wordt weergegeven hoe gegevens worden gerepliceerd in de primaire regio met ZRS":::

ZRS biedt uitstekende prestaties, lage latentie en tolerantie voor uw gegevens als deze tijdelijk niet beschikbaar zijn. ZRS zelf beschermt uw gegevens echter mogelijk niet tegen een regionale ramp waarbij meerdere zones permanent worden getroffen. Voor bescherming tegen regionale rampen raadt Microsoft aan om [geografisch zone-redundante](#geo-zone-redundant-storage) opslag (GZRS) te gebruiken, die gebruikmaakt van ZRS in de primaire regio en uw gegevens ook geo-repliceert naar een secundaire regio.

In de volgende tabel ziet u welke typen opslagaccounts ondersteuning bieden voor ZRS in welke regio's:

| Type opslagaccount | Ondersteunde regio’s | Ondersteunde services |
|--|--|--|
| Algemeen gebruik v2<sup>1</sup> | (Afrika) Zuid-Afrika - noord<br /> (Azië en Stille Oceaan) Azië - zuidoost<br /> (Azië en Stille Oceaan) Australië - oost<br /> (Azië en Stille Oceaan) Japan - oost<br /> (Canada) Canada - centraal<br /> (Europa) Europa - noord<br /> (Europa) Europa - west<br /> (Europa) Frankrijk - centraal<br /> (Europa) Duitsland - west-centraal<br /> (Europa) VK - zuid<br /> (Zuid-Amerika) Brazilië - zuid<br /> (VS) VS - centraal<br /> (US) US - oost<br /> (VS) VS - oost 2<br /> (VS) VS - zuid-centraal<br /> (VS) VS - west<br /> (VS) VS - west 2 | Blok-blobs<br /> Pagina-blobs<sup>2</sup><br /> Bestands shares (standaard)<br /> Tables<br /> Wachtrijen<br /> |
| BlockBlobStorage<sup>1</sup> | Azië - zuidoost<br /> Australië - oost<br /> Europa - noord<br /> Europa - west<br /> Frankrijk - centraal <br /> Japan East<br /> Verenigd Koninkrijk Zuid <br /> US - oost <br /> US - oost 2 <br /> US - west 2| Alleen Premium-blok-blobs |
| FileStorage | Azië - zuidoost<br /> Australië - oost<br /> Europa - noord<br /> Europa - west<br /> Frankrijk - centraal <br /> Japan East<br /> Verenigd Koninkrijk Zuid <br /> US - oost <br /> US - oost 2 <br /> US - west 2 | Alleen Premium-bestands shares |

<sup>1</sup> De archieflaag wordt momenteel niet ondersteund voor ZRS-accounts.<br />
<sup>2</sup> Opslagaccounts die beheerde Azure-schijven voor virtuele machines bevatten, gebruiken altijd LRS. Niet-mande azure-schijven moeten ook gebruikmaken van LRS. Het is mogelijk om een opslagaccount te maken voor niet-mande Azure-schijven die gebruik maken van GRS, maar dit wordt niet aanbevolen vanwege mogelijke problemen met consistentie ten opzichte van asynchrone geo-replicatie. Beheerde en niet-beheerde schijven bieden geen ondersteuning voor ZRS of GZRS. Zie Prijzen voor Azure Managed Disks voor meer informatie over [beheerde schijven.](https://azure.microsoft.com/pricing/details/managed-disks/)

Zie Servicesondersteuning per regio **in** Wat zijn Azure-beschikbaarheidszones? voor informatie over welke [regio's ondersteuning bieden voor ZRS.](../../availability-zones/az-overview.md)

## <a name="redundancy-in-a-secondary-region"></a>Redundantie in een secundaire regio

Voor toepassingen die hoge beschikbaarheid vereisen, kunt u ervoor kiezen om de gegevens in uw opslagaccount te kopiëren naar een secundaire regio die honderden kilometers van de primaire regio is verwijderd. Als uw opslagaccount wordt gekopieerd naar een secundaire regio, zijn uw gegevens duurzaam, zelfs in het geval van een volledige regionale storing of een noodgeval waarin de primaire regio niet kan worden hersteld.

Wanneer u een opslagaccount maakt, selecteert u de primaire regio voor het account. De gekoppelde secundaire regio wordt bepaald op basis van de primaire regio en kan niet worden gewijzigd. Zie Azure-regio's voor meer informatie over regio's die worden ondersteund [door Azure.](https://azure.microsoft.com/global-infrastructure/regions/)

Azure Storage biedt twee opties voor het kopiëren van uw gegevens naar een secundaire regio:

- **Geografisch redundante opslag (GRS)**: uw gegevens worden drie keer synchroon gekopieerd binnen één fysieke locatie in de primaire regio met LRS. Vervolgens worden uw gegevens asynchroon gekopieerd naar één fysieke locatie in de secundaire regio. Binnen de secundaire regio worden uw gegevens synchroon drie keer gekopieerd met behulp van LRS.
- **Geografisch zone-redundante opslag (GZRS)** kopieert uw gegevens synchroon naar drie Azure-beschikbaarheidszones in de primaire regio met behulp van ZRS. Vervolgens worden uw gegevens asynchroon gekopieerd naar één fysieke locatie in de secundaire regio. Binnen de secundaire regio worden uw gegevens synchroon drie keer gekopieerd met behulp van LRS.

> [!NOTE]
> Het belangrijkste verschil tussen GRS en GZRS is hoe gegevens worden gerepliceerd in de primaire regio. Binnen de secundaire regio worden gegevens altijd drie keer synchroon gerepliceerd met behulp van LRS. LRS in de secundaire regio beschermt uw gegevens tegen hardwarefouten.

Met GRS of GZRS zijn de gegevens in de secundaire regio niet beschikbaar voor lees- of schrijftoegang, tenzij er een failover naar de secundaire regio is. Voor leestoegang tot de secundaire regio configureert u uw opslagaccount voor het gebruik van geografisch redundante opslag met leestoegang (RA-GRS) of geografisch zone-redundante opslag met leestoegang (RA-GZRS). Zie Leestoegang tot gegevens in de secundaire [regio voor meer informatie.](#read-access-to-data-in-the-secondary-region)

Als de primaire regio niet meer beschikbaar is, kunt u ervoor kiezen om een fail over te brengen naar de secundaire regio. Nadat de failover is voltooid, wordt de secundaire regio de primaire regio en kunt u opnieuw gegevens lezen en schrijven. Zie Herstel na noodherstel en failover van opslagaccount voor meer informatie over het maken van een failover naar de [secundaire regio.](storage-disaster-recovery-guidance.md)

> [!IMPORTANT]
> Omdat gegevens asynchroon naar de secundaire regio worden gerepliceerd, kan een fout die van invloed is op de primaire regio leiden tot gegevensverlies als de primaire regio niet kan worden hersteld. Het interval tussen de meest recente schrijf schrijft naar de primaire regio en de laatste schrijf naar de secundaire regio staat bekend als het recovery point objective (RPO). De RPO geeft het tijdstip aan waarop gegevens kunnen worden hersteld. Azure Storage heeft doorgaans een RPO van minder dan 15 minuten, hoewel er momenteel geen SLA is voor hoe lang het duurt om gegevens naar de secundaire regio te repliceren.

### <a name="geo-redundant-storage"></a>Geografisch redundante opslag

Geografisch redundante opslag (GRS): uw gegevens worden drie keer synchroon gekopieerd binnen één fysieke locatie in de primaire regio met LRS. Vervolgens worden uw gegevens asynchroon gekopieerd naar één fysieke locatie in een secundaire regio die honderden kilometers verwijderd is van de primaire regio. GRS biedt duurzaamheid voor Azure Storage gegevensobjecten van ten minste 99,999999999999% (16 negens) gedurende een bepaald jaar.

Een schrijfbewerking wordt eerst vastgelegd op de primaire locatie en gerepliceerd met behulp van LRS. De update wordt vervolgens asynchroon gerepliceerd naar de secundaire regio. Wanneer gegevens naar de secundaire locatie worden geschreven, worden deze ook gerepliceerd binnen die locatie met behulp van LRS.

In het volgende diagram ziet u hoe uw gegevens worden gerepliceerd met GRS of RA-GRS:

:::image type="content" source="media/storage-redundancy/geo-redundant-storage.png" alt-text="Diagram waarin wordt weergegeven hoe gegevens worden gerepliceerd met GRS of RA-GRS":::

### <a name="geo-zone-redundant-storage"></a>Geografisch zone-redundante opslag

Geografisch zone-redundante opslag (GZRS) combineert de hoge beschikbaarheid die wordt geboden door redundantie in beschikbaarheidszones met bescherming tegen regionale uitval die wordt geboden door geo-replicatie. Gegevens in een GZRS-opslagaccount worden gekopieerd naar drie [Azure-beschikbaarheidszones](../../availability-zones/az-overview.md) in de primaire regio en ook gerepliceerd naar een secundaire geografische regio ter bescherming tegen regionale rampen. Microsoft raadt u aan GZRS te gebruiken voor toepassingen waarvoor maximale consistentie, duurzaamheid en beschikbaarheid, uitstekende prestaties en tolerantie voor herstel na noodherstel zijn vereist.

Met een GZRS-opslagaccount kunt u gegevens blijven lezen en schrijven als een beschikbaarheidszone niet meer beschikbaar is of onherkenbaar is. Daarnaast zijn uw gegevens duurzaam in het geval van een volledige regionale storing of een noodgeval waarbij de primaire regio niet kan worden hersteld. GZRS is ontworpen om ten minste 99,999999999999999% (16 negens) duurzaamheid van objecten in een bepaald jaar te bieden.

In het volgende diagram ziet u hoe uw gegevens worden gerepliceerd met GZRS of RA-GZRS:

:::image type="content" source="media/storage-redundancy/geo-zone-redundant-storage.png" alt-text="Diagram waarin wordt weergegeven hoe gegevens worden gerepliceerd met GZRS of RA-GZRS":::

Alleen v2-opslagaccounts voor algemeen gebruik ondersteunen GZRS en RA-GZRS. Zie [Overzicht van Azure-opslagaccounts](storage-account-overview.md) voor meer informatie over de typen opslagaccounts. GZRS en RA-GZRS ondersteunen blok-blobs, pagina-blobs (met uitzondering van VHD-schijven), bestanden, tabellen en wachtrijen.

GZRS en RA-GZRS worden ondersteund in de volgende regio's:

- (Afrika) Zuid-Afrika - noord
- (Azië en Stille Oceaan) Azië - oost
- (Azië en Stille Oceaan) Azië - zuidoost
- (Azië en Stille Oceaan) Australië - oost
- (Azië en Stille Oceaan) India - centraal
- (Azië en Stille Oceaan) Japan - oost
- (Azië en Stille Oceaan) Korea - centraal
- (Canada) Canada - centraal
- (Europa) Europa - noord
- (Europa) Europa - west
- (Europa) Frankrijk - centraal
- (Europa) Duitsland - west-centraal
- (Europa) Noorwegen - oost
- (Europa) Zwitserland - noord
- (Europa) VK - zuid
- (Midden-Oosten) VAE - noord
- (Zuid-Amerika) Brazilië - zuid
- (VS) VS - centraal
- (US) US - oost
- (VS) VS - oost 2
- (VS) VS - noord-centraal
- (VS) VS - zuid-centraal
- (VS) VS - west
- (VS) VS - west 2

Zie prijsinformatie voor [Blobs,](https://azure.microsoft.com/pricing/details/storage/blobs) [Files,](https://azure.microsoft.com/pricing/details/storage/files/) [Queues](https://azure.microsoft.com/pricing/details/storage/queues/)en Tables voor meer informatie over [prijzen.](https://azure.microsoft.com/pricing/details/storage/tables/)

## <a name="read-access-to-data-in-the-secondary-region"></a>Leestoegang tot gegevens in de secundaire regio

Geografisch redundante opslag (met GRS of GZRS) repliceert uw gegevens naar een andere fysieke locatie in de secundaire regio om u te beschermen tegen regionale uitval. Deze gegevens kunnen echter alleen worden gelezen als de klant of Microsoft een failover van de primaire naar de secundaire regio initieert. Wanneer u leestoegang tot de secundaire regio inschakelen, kunnen uw gegevens te allen tijde worden gelezen, ook in een situatie waarin de primaire regio niet meer beschikbaar is. Voor leestoegang tot de secundaire regio moet u geografisch redundante opslag met leestoegang (RA-GRS) of geografisch zone-redundante opslag met leestoegang (RA-GZRS) inschakelen.

> [!NOTE]
> Azure Files biedt geen ondersteuning voor geografisch redundante opslag met leestoegang (RA-GRS) en geografisch zone-redundante opslag met leestoegang (RA-GZRS).

### <a name="design-your-applications-for-read-access-to-the-secondary"></a>Uw toepassingen ontwerpen voor leestoegang tot de secundaire

Als uw opslagaccount is geconfigureerd voor leestoegang tot de secundaire regio, kunt u uw toepassingen zo ontwerpen dat deze naadloos worden verplaatst naar het lezen van gegevens uit de secundaire regio als de primaire regio om een of andere reden niet meer beschikbaar is. 

De secundaire regio is beschikbaar voor leestoegang nadat u RA-GRS of RA-GZRS hebt ingeschakeld, zodat u uw toepassing van tevoren kunt testen om er zeker van te zijn dat deze correct wordt gelezen van de secundaire regio in het geval van een storing. Zie [Geo-redundantie](geo-redundant-design.md)gebruiken om toepassingen met hoge beschikbaarheid te ontwerpen voor meer informatie over het ontwerpen van uw toepassingen voor hoge beschikbaarheid.

Wanneer leestoegang tot de secundaire is ingeschakeld, kan uw toepassing worden gelezen vanaf het secundaire eindpunt en vanaf het primaire eindpunt. Het secundaire eindpunt wordt aan het achtervoegsel *–secondary* aan de accountnaam toevoegen. Als uw primaire eindpunt voor Blob Storage bijvoorbeeld is, is `myaccount.blob.core.windows.net` het secundaire eindpunt `myaccount-secondary.blob.core.windows.net` . De accounttoegangssleutels voor uw opslagaccount zijn hetzelfde voor zowel de primaire als de secundaire eindpunten.

### <a name="check-the-last-sync-time-property"></a>De eigenschap Laatst gesynchroniseerd controleren

Omdat gegevens asynchroon naar de secundaire regio worden gerepliceerd, ligt de secundaire regio vaak achter de primaire regio. Als er een fout in de primaire regio gebeurt, is de kans groot dat alle schrijf schrijf naar de primaire regio nog niet naar de secundaire regio zijn gerepliceerd.

Om te bepalen welke schrijfbewerkingen naar de secundaire regio zijn gerepliceerd, kan uw toepassing de eigenschap **Laatste** synchronisatietijd voor uw opslagaccount controleren. Alle schrijfbewerkingen die vóór de laatste synchronisatietijd naar de primaire regio zijn geschreven, zijn gerepliceerd naar de secundaire regio, wat betekent dat ze beschikbaar zijn om te worden gelezen vanaf de secundaire regio. Schrijfbewerkingen die na de laatste synchronisatietijd naar de primaire regio worden geschreven, zijn mogelijk wel of niet gerepliceerd naar de secundaire regio, wat betekent dat ze mogelijk niet beschikbaar zijn voor leesbewerkingen.

U kunt de waarde van de eigenschap **Laatste** synchronisatietijd opvragen met behulp van Azure PowerShell, Azure CLI of een van de Azure Storage clientbibliotheken. De **eigenschap Laatste synchronisatietijd** is een GMT-datum/tijd-waarde. Zie De eigenschap Laatste synchronisatietijd voor een [opslagaccount controleren voor meer informatie.](last-sync-time-get.md)

## <a name="summary-of-redundancy-options"></a>Samenvatting van redundantieopties

De tabellen in de volgende secties geven een overzicht van de redundantieopties die beschikbaar zijn voor Azure Storage

### <a name="durability-and-availability-parameters"></a>Parameters voor duurzaamheid en beschikbaarheid

In de volgende tabel worden de belangrijkste parameters voor elke redundantieoptie beschreven:

| Parameter | LRS | ZRS | GRS/RA-GRS | GZRS/RA-GZRS |
|:-|:-|:-|:-|:-|
| Duurzaamheid van objecten in procenten gedurende een bepaald jaar | ten minste 99,9999999999% (11 negens) | ten minste 99,99999999999% (12 negens) | ten minste 99,999999999999999% (16 negens) | ten minste 99,99999999999999% (16 negens) |
| Beschikbaarheid voor leesaanvragen | Ten minste 99,9% (99% voor de cool-toegangslaag) | Ten minste 99,9% (99% voor de cool-toegangslaag) | Ten minste 99,9% (99% voor cool-toegangslaag) voor GRS<br /><br />Ten minste 99,99% (99,9% voor cool-toegangslaag) voor RA-GRS | Ten minste 99,9% (99% voor cool-toegangslaag) voor GZRS<br /><br />Ten minste 99,99% (99,9% voor cool-toegangslaag) voor RA-GZRS |
| Beschikbaarheid voor schrijfaanvragen | Ten minste 99,9% (99% voor de cool-toegangslaag) | Ten minste 99,9% (99% voor de cool-toegangslaag) | Ten minste 99,9% (99% voor de cool-toegangslaag) | Ten minste 99,9% (99% voor de cool-toegangslaag) |
| Aantal kopieën van gegevens op afzonderlijke knooppunten | Drie kopieën binnen één regio | Drie kopieën in afzonderlijke beschikbaarheidszones binnen één regio | Zes kopieën in totaal, waaronder drie in de primaire regio en drie in de secundaire regio | Zes kopieën in totaal, waaronder drie in afzonderlijke beschikbaarheidszones in de primaire regio en drie lokaal redundante kopieën in de secundaire regio |

### <a name="durability-and-availability-by-outage-scenario"></a>Duurzaamheid en beschikbaarheid per storingsscenario

De volgende tabel geeft aan of uw gegevens duurzaam zijn en beschikbaar zijn in een bepaald scenario, afhankelijk van welk type redundantie van kracht is voor uw opslagaccount:

| Scenario met uitval | LRS | ZRS | GRS/RA-GRS | GZRS/RA-GZRS |
|:-|:-|:-|:-|:-|
| Een knooppunt in een datacenter is niet meer beschikbaar | Ja | Ja | Ja | Ja |
| Een volledig datacenter (zonaal of niet-zonaal) is niet meer beschikbaar | Nee | Ja | Ja<sup>1</sup> | Yes |
| Een storing in de hele regio treedt op in de primaire regio | Nee | Nee | Ja<sup>1</sup> | Ja<sup>1</sup> |
| Leestoegang tot de secundaire regio is beschikbaar als de primaire regio niet meer beschikbaar is | Nee | Nee | Ja (met RA-GRS) | Ja (met RA-GZRS) |

<sup>1</sup> Failover van account is vereist om de beschikbaarheid van schrijfgegevens te herstellen als de primaire regio niet meer beschikbaar is. Zie Herstel na noodherstel en [failover van opslagaccount voor meer informatie.](storage-disaster-recovery-guidance.md)

### <a name="supported-azure-storage-services"></a>Ondersteunde Azure Storage services

In de volgende tabel ziet u welke redundantieopties worden ondersteund door elke Azure Storage service.

| LRS | ZRS | GRS/RA-GRS | GZRS/RA-GZRS |
|:-|:-|:-|:-|
| Blob Storage<br />Queue Storage<br />Table Storage<br />Azure Files<br />Beheerde Azure-schijven | Blob Storage<br />Queue Storage<br />Table Storage<br />Azure Files | Blob Storage<br />Queue Storage<br />Table Storage<br />Azure Files<br /> | Blob Storage<br />Queue Storage<br />Table Storage<br />Azure Files<br /> |

### <a name="supported-storage-account-types"></a>Ondersteunde typen opslagaccounts

In de volgende tabel ziet u welke redundantieopties door elk type opslagaccount worden ondersteund. Zie Overzicht van opslagaccounts voor informatie [over opslagaccounttypen.](storage-account-overview.md)

| LRS | ZRS | GRS/RA-GRS | GZRS/RA-GZRS |
|:-|:-|:-|:-|
| Algemeen gebruik v2<br /> Algemeen gebruik v1<br /> BlockBlobStorage<br /> BlobStorage<br /> FileStorage | Algemeen gebruik v2<br /> BlockBlobStorage<br /> FileStorage | Algemeen gebruik v2<br /> Algemeen gebruik v1<br /> BlobStorage | Algemeen gebruik v2 |

Alle gegevens voor alle opslagaccounts worden gekopieerd volgens de redundantieoptie voor het opslagaccount. Objecten, waaronder blok-blobs, toevoegen-blobs, pagina-blobs, wachtrijen, tabellen en bestanden, worden gekopieerd. Gegevens in alle lagen, inclusief de archieflaag, worden gekopieerd. Zie Azure [Blob-opslag: hot-, cool-](../blobs/storage-blob-storage-tiers.md)en archieftoegangslagen voor meer informatie over blob-lagen.

Zie prijzen voor Azure Storage prijsinformatie voor [Azure Storage redundantieoptie.](https://azure.microsoft.com/pricing/details/storage/)

> [!NOTE]
> Azure Premium Disk Storage momenteel alleen lokaal redundante opslag (LRS). Blok-blobopslagaccounts ondersteunen lokaal redundante opslag (LRS) en zone-redundante opslag (ZRS) in bepaalde regio's.

## <a name="data-integrity"></a>Gegevensintegriteit

Azure Storage controleert regelmatig de integriteit van gegevens die zijn opgeslagen met cyclische redundantiecontroles (CRC's). Als beschadiging van gegevens wordt gedetecteerd, wordt deze hersteld met behulp van redundante gegevens. Azure Storage berekent ook controlesums voor al het netwerkverkeer om beschadiging van gegevenspakketten te detecteren bij het opslaan of ophalen van gegevens.

## <a name="see-also"></a>Zie ook

- [Controleer de eigenschap Laatste synchronisatietijd voor een opslagaccount](last-sync-time-get.md)
- [De redundantieoptie voor een opslagaccount wijzigen](redundancy-migration.md)
- [Geo-redundantie gebruiken om toepassingen met hoge beschikbare gegevens te ontwerpen](geo-redundant-design.md)
- [Herstel na noodgeval en failover van opslagaccount](storage-disaster-recovery-guidance.md)
