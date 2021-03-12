---
title: Overzicht van gereserveerde media-eenheden | Microsoft Docs
description: Dit artikel bevat een overzicht van het schalen van media verwerking met Azure Media Services.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2021
ms.author: inhenkel
ms.openlocfilehash: d0692996c27f969ffc90078db2ddcc849ee15ab1
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2021
ms.locfileid: "103012714"
---
# <a name="media-reserved-units"></a>Door media gereserveerde eenheden

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Met Azure Media Services kunt u media verwerking schalen door gereserveerde media-eenheden (MRUs) te beheren. Een MRU biedt extra computer capaciteit die is vereist voor het coderen van media. Het aantal MRUs bepaalt de snelheid waarmee uw media taken worden verwerkt en hoeveel media taken gelijktijdig kunnen worden verwerkt in een account. Als uw account bijvoorbeeld vijf MRUs heeft en er taken zijn die moeten worden verwerkt, dan kunnen vijf media taken gelijktijdig worden uitgevoerd. Alle resterende taken worden in de wachtrij geplaatst en kunnen worden opgehaald om opeenvolgend te worden verwerkt wanneer een actieve taak is voltooid. Elke MRU die u inricht, resulteert in een capaciteits reservering, maar biedt geen speciale resource. Tijdens een extreem hoge vraag is het niet mogelijk om al uw MRUss onmiddellijk te verwerken.

## <a name="choosing-between-different-reserved-unit-types"></a>Kiezen tussen verschillende typen gereserveerde eenheden

In de volgende tabel kunt u een beslissing nemen bij het kiezen van verschillende coderings snelheden.  Het toont de duur van de code ring voor een 7 minuut, 1080p video, afhankelijk van de gebruikte MRU.

|RU-type|Scenario|Voorbeeld resultaten voor de 7 min 1080p-video |
|---|---|---|
| **S1**|Code ring met één bitsnelheid. <br/>Bestanden in SD of onder oplossingen, niet tijd gevoelig, lage kosten.|De code ring voor een MP4-bestand van de SD-resolutie met één bitsnelheid met ' H264 single bitrate SD 16x9 ' duurt circa 7 minuten.|
| **S2**|Code ring met één bitrate en meerdere bitrates.<br/>Normaal gebruik voor zowel SD-als HD-code ring.|De code ring met de voor instelling ' H264 single bitrate 720p ' duurt circa 6 minuten.<br/><br/>De code ring met de voor instelling ' H264 multiple bitrate 720p ' duurt ongeveer 12 minuten.|
| **S3**|Code ring met één bitrate en meerdere bitrates.<br/>Volledige HD-en 4.000 video-oplossingen. Tijd gevoelige, snellere aflever code.|De code ring met de voor instelling ' H264 single bitrate 1080p ' duurt ongeveer drie minuten.<br/><br/>De code ring met de voor instelling ' H264 multiple bitrate 1080p ' duurt ongeveer acht minuten.|

> [!NOTE]
> Als u de MRU niet voorbereidt voor uw account, worden uw media taken verwerkt met de prestaties van een MRU-voor-S1 en worden de taken sequentieel opgehaald. Er is geen verwerkings capaciteit gereserveerd, dus de wacht tijd tussen het volt ooien van een taak en de volgende begint, is afhankelijk van de beschik baarheid van resources in het systeem.

## <a name="considerations"></a>Overwegingen

* Voor audio analyse en video analyse taken die worden geactiveerd door Media Services v3 of Video Indexer, wordt het inrichten van het account met tien S3-eenheden nadrukkelijk aanbevolen. Als u meer dan 10 S3 MRUs nodig hebt, kunt u een ondersteunings ticket openen met behulp van de [Azure Portal](https://portal.azure.com/).
* Voor het coderen van taken die geen MRUs hebben, is er geen bovengrens voor de tijd die uw taken in de wachtrij status kunnen best Eden. er wordt Maxi maal één taak tegelijk uitgevoerd.

## <a name="billing"></a>Billing

Er worden kosten in rekening gebracht op basis van het aantal minuten dat de gereserveerde media-eenheden in uw account zijn ingericht. Dit gebeurt onafhankelijk van het feit of er taken worden uitgevoerd in uw account. Zie de sectie Veelgestelde vragen op de pagina met prijzen voor [Media Services](https://azure.microsoft.com/pricing/details/media-services/) voor een gedetailleerde uitleg.

## <a name="quotas-and-limitations"></a>Quota en beperkingen

Zie [quota's en beperkingen](media-services-quotas-and-limitations.md)voor meer informatie over quota en beperkingen en het openen van een ondersteunings ticket.

## <a name="next-steps"></a>Volgende stappen

Probeer Media verwerking te schalen met een van deze technologieën:

[.Net](media-services-dotnet-encoding-units.md) 
 [Portal](media-services-portal-scale-media-processing.md) 
 [Rest](/rest/api/media/operations/encodingreservedunittype) 
 [Java](https://github.com/rnrneverdies/azure-sdk-for-media-services-java-samples) 
 [Php](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)