---
author: rothja
ms.service: media-services
ms.topic: include
ms.date: 09/16/2020
ms.author: jroth
ms.openlocfilehash: f4bdd5332b3c10cf375c7c04ede25137328714a2
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106073299"
---
>[!NOTE]
>Open een ndersteuningsticket voor resources die niet zijn opgelost om te vragen om een toename van de quota. Maak geen extra Azure Media Services-accounts in een poging om de limieten te verhogen.

| Resource | Limiet | 
| --- | --- | 
| Azure Media Services-accounts in één abonnement | 25 (vast) |
| Gereserveerde media-eenheden per Media Services-account |25 (S1)<br/>10 (S2, S3)<sup>1</sup> | 
| Taken per Media Services-account | 50.000<sup>2</sup> |
| Gekoppelde taken per taak | 30 (vast) |
| Assets per Media Services-account | 1.000.000|
| Activa per taak | 50 |
| Activa per taak | 100 |
| Unieke locators die aan één activum tegelijk zijn gekoppeld | 5<sup>4</sup> |
| Live-kanalen per Media Services-account |5|
| Programma's met de status Gestopt per kanaal |50|
| Programma's met de status Wordt uitgevoerd per kanaal |3|
| Streaming-eindpunten die zijn gestopt of worden uitgevoerd per Media Services-account|2|
| Streaming-eenheden per streaming-eindpunt |10 |
| Opslagaccounts | 100<sup>5</sup> (opgelost) |
| Beleidsregels | 1,000,000<sup>6</sup> |
| Bestandsgrootte| In sommige scenario's is er een limiet voor de maximale bestandsgrootte die wordt ondersteund voor verwerking in Media Services.<sup>7</sup> |

<sup>1</sup>Als u het type wijzigt, bijvoorbeeld van S2 naar S1, worden de limieten voor maximale gereserveerde eenheden opnieuw ingesteld.

<sup>2</sup>Deze waarde omvat taken in de wachtrij, voltooide taken, actieve taken en geannuleerde taken. Deze bevat geen verwijderde taken. U kunt de oude taken verwijderen met **IJob.Delete** of de HTTP-aanvraag **DELETE**.

Vanaf 1 april 2017 worden alle taakrecords in uw account die ouder zijn dan 90 dagen automatisch verwijderd, samen met de bijbehorende records. Automatische verwijdering vindt ook plaats als het totaalaantal records onder de maximale quota ligt. Als u de taak en taakgegevens wilt archiveren, gebruikt u de code die wordt beschreven in [Assets beheren met de Media Services .NET-SDK](../articles/media-services/previous/media-services-dotnet-manage-entities.md).

<sup>3</sup>Bij het maken van een aanvraag om taakentiteiten weer te geven, worden maximaal 1000 entiteiten per aanvraag geretourneerd. Als u ingediende taken wilt bijhouden, gebruikt u de query's voor overslaan of bewaren die zijn beschreven in [Query-opties van het OData-systeem](/previous-versions/dynamicscrm-2015/developers-guide/gg309461(v=crm.7)).

<sup>4</sup>Locators zijn niet ontworpen om toegangsbeheer per gebruiker te regelen. Gebruik DRM-oplossingen (Digital Rights Management) als u afzonderlijke gebruikers verschillende toegangsrechten wilt geven. Raadpleeg [Uw inhoud beveiligen met Azure Media Services](../articles/media-services/latest/drm-content-protection-concept.md) voor meer informatie.

<sup>5</sup>De opslagaccounts moeten deel uitmaken van hetzelfde Azure-abonnement.

<sup>6</sup>Er is een limiet van 1.000.000 beleidsregels voor verschillend Media Services-beleid. Een voorbeeld is voor het locator-beleid of ContentKeyAuthorizationPolicy. 

>[!NOTE]
> Gebruik dezelfde beleids-id als u altijd dezelfde dagen en toegangsmachtigingen gebruikt. Raadpleeg [Assets beheren met de Media Services .NET-SDK](../articles/media-services/previous/media-services-dotnet-manage-entities.md#limit-access-policies) voor meer informatie en een voorbeeld.

<sup>7</sup>De maximale grootte die wordt ondersteund voor één blob is momenteel tot 5 TB in Azure Blob Storage. Aanvullende limieten zijn van toepassing in Media Services op basis van VM-grootten die worden gebruikt door de service. De groottelimieten zijn van toepassing op de bestanden die u upload en ook de bestanden die worden gegenereerd als resultaat van Media Services-verwerking (coderen of analyseren). Als uw bronbestand groter is dan 260 GB, mislukt uw taak waarschijnlijk. 

De volgende tablel laat de limieten zijn van de gereserveerde media-eenheden S1, S2 en S3. Als uw bronbestand groter is dan de limieten die zijn gedefinieerd in de tabel, mislukt de coderingstaak waarschijnlijk. As u 4K-resolutiebronnen van een lange duur codeert, moet u gereserveerde media-eenheden van S3 gebruiken om de benodigde prestaties te halen. Als u 4k-inhoud heeft die groter is dan het limiet van 260 GB voor gereserveerde media-eenheden in S3, opent u een ondersteuningsticket.

|Type gereserveerde media-eenheid    |Maximale invoergrootte (GB)|
|---|---|
|S1 |    26|
|S2    | 60|
|S3    |260|
