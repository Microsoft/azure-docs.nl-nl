---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 10/26/2020
ms.author: inhenkel
ms.openlocfilehash: 59ff0ba854fa609e6d29f3473f662a89ab5f3dbc
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95564024"
---
> [!NOTE]
> Open een ndersteuningsticket voor resources die niet zijn opgelost om te vragen om een toename van de quota. Maak geen extra Azure Media Services-accounts in een poging om de limieten te verhogen.

### <a name="account-limits"></a>Accountlimieten

| Resource | Standaardlimiet |
| --- | --- |
| Media Services-accounts in één abonnement | 100 (vast) |

### <a name="asset-limits"></a>Assetlimieten

| Resource | Standaardlimiet |
| --- | --- |
| Assets per Media Services-account | 1.000.000|

### <a name="storage-media-limits"></a>Opslaglimieten (media)

| Resource | Standaardlimiet |
| --- | --- |
| Bestandsgrootte| In sommige scenario's is er een limiet voor de maximale bestandsgrootte die wordt ondersteund voor verwerking in Media Services. <sup>(1)</sup> |
| Opslagaccounts | 100<sup>(2)</sup> (opgelost) |

<sup>1</sup> De maximale grootte die wordt ondersteund voor één blob is momenteel 5 TB in Azure Blob Storage. Aanvullende limieten zijn van toepassing in Media Services op basis van VM-grootten die worden gebruikt door de service. De groottelimieten zijn van toepassing op de bestanden die u upload en ook de bestanden die worden gegenereerd als resultaat van Media Services-verwerking (coderen of analyseren). Als uw bronbestand groter is dan 260 GB, mislukt uw taak waarschijnlijk.

De volgende tablel laat de limieten zijn van de gereserveerde media-eenheden S1, S2 en S3. Als uw bronbestand groter is dan de limieten die zijn gedefinieerd in de tabel, mislukt de coderingstaak waarschijnlijk. As u 4K-resolutiebronnen van een lange duur codeert, moet u gereserveerde media-eenheden van S3 gebruiken om de benodigde prestaties te halen. Als u 4k-inhoud heeft die groter is dan het limiet van 260 GB voor gereserveerde media-eenheden in S3, opent u een ondersteuningsticket.

|Type gereserveerde media-eenheid|Maximale invoergrootte (GB)|
|---|---|
|S1 |    26|
|S2    | 60|
|S3    |260|

<sup>2</sup> De opslagaccounts moeten deel uitmaken van hetzelfde Azure-abonnement.

### <a name="jobs-encoding--analyzing-limits"></a>Limieten voor taken (codering en analyse)

| Resource | Standaardlimiet |
| --- | --- |
| Taken per Media Services-account | 500.000 <sup>(3)</sup> (vast)|
| Taakinvoer per taak | 50 (vast)|
| Taakuitvoer per taak | 20 (vast) |
| Transformaties per Media Services-account | 100 (vast)|
| Transformatie-uitvoer in een transformatie | 20 (vast) |
| Bestanden per taakinvoer|10 (vast)|

<sup>3</sup> Deze waarde omvat taken in de wachtrij, voltooide taken, actieve taken en geannuleerde taken. De waarde omvat geen verwijderde taken. 

Elke taakrecord in uw account die ouder is dan negentig dagen wordt automatisch verwijderd, ook als het totale aantal records lager is dan het maximale quotum. 

### <a name="live-streaming-limits"></a>Limieten voor live streamen

| Resource | Standaardlimiet |
| --- | --- |
| Livegebeurtenissen <sup>(4)</sup> per Media Services-account |5|
| Live-uitvoer per livegebeurtenis |3 <sup>(5)</sup> |
| Maximale duur van live-uitvoer | [Grootte van het DVR-venster](../articles/media-services/latest/live-event-cloud-dvr.md) |

<sup>4</sup> Zie [Vergelijkingen tussen en beperkingen voor typen livegebeurtenissen](../articles/media-services/latest/live-event-types-comparison.md) voor meer informatie over beperkingen van livegebeurtenissen.

<sup>5</sup> Live-uitvoer starten zodra ze zijn gemaakt en stoppen wanneer ze worden verwijderd.

### <a name="packaging--delivery-limits"></a>Verpakkings- en leveringslimieten

| Resource | Standaardlimiet |
| --- | --- |
| Streaming-eindpunten (gestopt of actief) per Media Services-account| 2 |
| Dynamisch-manifestfilters|100|
| Beleid voor streaming | 100 <sup>(6)</sup> |
| Unieke streaming-locators die aan één asset tegelijk zijn gekoppeld | 100<sup>(7)</sup> (vast) |

<sup>6</sup> Wanneer u een aangepast [streamingbeleid](/rest/api/media/streamingpolicies) gebruikt, moet u een beperkte set met dergelijke beleidsregels ontwerpen voor uw Media Service-account en deze opnieuw gebruiken voor de StreamingLocators wanneer dezelfde versleutelingsopties en protocollen nodig zijn. U hoeft geen nieuw streaming-beleid te maken voor elke streaming-locator.

<sup>7</sup> Streaming-locators zijn niet ontworpen om toegangsbeheer per gebruiker te regelen. Gebruik DRM-oplossingen (Digital Rights Management) als u afzonderlijke gebruikers verschillende toegangsrechten wilt geven.

### <a name="protection-limits"></a>Beschermingslimieten

| Resource | Standaardlimiet |
| --- | --- |
| Opties per inhoudssleutelbeleid | 30 |
| Licenties per maand voor elk van de DRM-typen voor de sleutelleveringsservice van Media Services per account|1.000.000|

### <a name="support-ticket"></a>Ondersteuningsticket

Als resources niet vast zijn, kunt u een [ondersteuningsticket](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) openen om te vragen of de quota kunnen worden verhoogd. Neem gedetailleerde informatie op in de aanvraag over de gewenste wijzigingen in het quotum, use-case-scenario's en de vereiste regio's. <br/>Maak **geen** extra Azure Media Services-accounts in een poging om de limieten te verhogen.