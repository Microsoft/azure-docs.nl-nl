---
title: Aandachtspunten bij het gebruik van Video Indexer op schaal-Azure
titleSuffix: Azure Media Services
description: In dit onderwerp wordt uitgelegd wat u moet overwegen wanneer u Video Indexer op schaal gebruikt.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: how-to
ms.date: 11/13/2020
ms.author: juliako
ms.openlocfilehash: b955c0f494b757fd29c400194ef8b11314a89a03
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96483607"
---
# <a name="things-to-consider-when-using-video-indexer-at-scale"></a>Aandachtspunten bij het gebruik van Video Indexer op schaal

Wanneer u Azure Media Services video indexer gebruikt om Video's te indexeren en uw archief met Video's groeit, kunt u overwegen om te schalen. 

In dit artikel vindt u antwoorden op vragen zoals:

* Moeten er technologische beperkingen in rekening worden gebracht?
* Is er een slimme en efficiënte manier om dit te doen?
* Kan ik voor komen dat er veel geld in het proces wordt besteed?

Het artikel bevat zes aanbevolen procedures voor het gebruik van Video Indexer op schaal.

## <a name="when-uploading-videos-consider-using-a-url-over-byte-array"></a>Overweeg het gebruik van een URL via een byte matrix bij het uploaden van Video's

Video Indexer biedt u de keuze om Video's te uploaden van URL of rechtstreeks door het bestand te verzenden als een byte matrix, maar dit is mogelijk met enkele beperkingen. Zie [overwegingen en beperkingen uploaden](upload-index-videos.md#uploading-considerations-and-limitations) voor meer informatie.

Ten eerste is de bestands grootte beperkt. De grootte van het byte matrix bestand is beperkt tot 2 GB, vergeleken met de maximale upload grootte van 30 GB tijdens het gebruik van URL.

Bekijk ten tweede enkele van de problemen die van invloed kunnen zijn op uw prestaties en daarom de mogelijkheid om te schalen:

* Het verzenden van bestanden met meerdere onderdelen betekent een hoge afhankelijkheid van uw netwerk. 
* betrouw baarheid van de service, 
* mogelijkheden 
* upload snelheid, 
* verloren pakketten ergens in het World Wide Web.

:::image type="content" source="./media/considerations-when-use-at-scale/first-consideration.png" alt-text="Eerste overweging voor het gebruik van Video Indexer op schaal":::

Wanneer u Video's met behulp van een URL uploadt, hoeft u alleen maar een pad naar de locatie van een media bestand op te geven en Video Indexer de rest te verzorgen (Zie het `videoUrl` veld in de [video](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Upload-Video?&pattern=upload) -API voor uploaden).

> [!TIP]
> Gebruik de `videoUrl` optionele para meter van de upload video-API.

Bekijk [dit voor](upload-index-videos.md#code-sample)beeld voor een voor beeld van het uploaden van Video's met behulp van URL. U kunt [AzCopy](../../storage/common/storage-use-azcopy-v10.md) ook gebruiken om een snelle en betrouw bare manier om uw inhoud op te halen uit een opslag account van waaruit u deze kunt verzenden naar video indexer met behulp van [SAS-URL](../../storage/common/storage-sas-overview.md).

## <a name="increase-media-reserved-units-if-needed"></a>Verg root gereserveerde media-eenheden, indien nodig

Normaal gesp roken hoeft u niet veel reken kracht te gebruiken wanneer u begint met het gebruik van Video Indexer. Wanneer u begint met een groter archief van de Video's die u wilt indexeren en u wilt dat het proces zich in een tempo bevindt dat aan uw use-case voldoet, moet u het gebruik van Video Indexer opschalen. Daarom moet u nadenken over het verhogen van het aantal reken resources dat u gebruikt als de huidige hoeveelheid computer capaciteit niet voldoende is.

Als u in Azure Media Services een grotere reken kracht en parallel Lise ring wilt, moet u aandacht best Eden aan [gereserveerde](../latest/concept-media-reserved-units.md)media-eenheden (RUs). Het RUs is de reken eenheden waarmee de para meters voor uw media verwerkings taken worden bepaald. Het aantal RUs is van invloed op het aantal media taken dat gelijktijdig in elk account kan worden verwerkt en het type bepaalt de snelheid van de verwerking en één video kan meer dan één RU vereisen als de index is complex. Wanneer uw RUs bezig is, worden nieuwe taken in een wachtrij bewaard totdat een andere resource beschikbaar is.

Video Indexer biedt een systeem voor automatisch schalen dat RUs omlaag draait wanneer minder verwerkings capaciteit nodig is, en om te voor komen dat er resources worden gebruikt die deel uitmaken van de tijd die u in spoed uren hebt (tot volledig gebruik van al uw RUs). U kunt deze functionaliteit inschakelen door [automatisch schalen](manage-account-connected-to-azure.md#autoscale-reserved-units) in te scha kelen in de account instellingen of via [Update-betaalde accounts-Azure-Media-Services-API](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Paid-Account-Azure-Media-Services?&pattern=update).

:::image type="content" source="./media/considerations-when-use-at-scale/second-consideration.jpg" alt-text="Tweede overweging voor het gebruik van Video Indexer op schaal":::

:::image type="content" source="./media/considerations-when-use-at-scale/reserved-units.jpg" alt-text="Gereserveerde AMS-eenheden":::

Als u het indexeren wilt minimaliseren en een lage door Voer, is het raadzaam om te beginnen met 10 RUs van het type S3. Als u later omhoog schaalt om meer inhoud of hogere gelijktijdigheid te ondersteunen en u meer resources nodig hebt, kunt u [contact met ons opnemen via het ondersteunings systeem](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) (alleen op betaalde accounts) om u te vragen om meer RUs-toewijzing.

## <a name="respect-throttling"></a>Naleving beperken

Video Indexer is gebouwd om te voorzien in indexering op schaal en wanneer u het meeste uit het bereik wilt halen, moet u ook rekening houden met de mogelijkheden van het systeem en uw integratie dienovereenkomstig ontwerpen. U wilt geen upload aanvragen voor een batch-video verzenden om te ontdekken dat sommige films niet zijn geüpload en u ontvangt een HTTP 429-respons code (te veel aanvragen). Dit kan gebeuren als gevolg van het feit dat u meer aanvragen hebt verzonden dan het [aantal films per minuut dat wij ondersteunen](upload-index-videos.md#uploading-considerations-and-limitations). Video Indexer een `retry-after` header toevoegt in het HTTP-antwoord, geeft de header aan wanneer u de volgende poging moet proberen. Zorg ervoor dat u dit respecteert voordat u de volgende aanvraag uitvoert.

:::image type="content" source="./media/considerations-when-use-at-scale/respect-throttling.jpg" alt-text="Uw integratie goed ontwerpen, naleving beperken":::

## <a name="use-callback-url"></a>Call back-URL gebruiken

We raden u aan om de status van uw aanvraag voortdurend van de tweede te controleren, maar u kunt ook een [call back-URL](upload-index-videos.md#callbackurl)toevoegen en wachten tot video indexer u bij te werken. Zodra er status wijzigingen in uw Upload aanvraag zijn, ontvangt u een melding naar de URL die u hebt opgegeven.

U kunt een call back-URL toevoegen als een van de para meters van de [Upload video-API](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Upload-Video?&pattern=upload). Bekijk de code voorbeelden in [github opslag plaats](https://github.com/Azure-Samples/media-services-video-indexer/tree/master/). 

Voor call back-URL kunt u ook Azure Functions gebruiken, een op een serverloos, op gebeurtenis gebaseerd platform dat kan worden geactiveerd door HTTP en een volgende stroom implementeert.

### <a name="callback-url-definition"></a>URL-definitie voor call back

[!INCLUDE [callback url](./includes/callback-url.md)]

## <a name="use-the-right-indexing-parameters-for-you"></a>Gebruik de juiste indexerings parameters voor u

Bij het nemen van beslissingen met betrekking tot het gebruik van Video Indexer op schaal, raadpleegt u hoe u deze optimaal kunt benutten met de juiste para meters voor uw behoeften. Denk na over uw use-case door verschillende para meters te definiëren die u geld besparen en het indexerings proces voor uw Video's sneller maakt.

Lees deze korte [documentatie](upload-index-videos.md)voordat u uw video uploadt en indexeert. Controleer de [indexingPreset](upload-index-videos.md#indexingpreset) en [streamingPreset](upload-index-videos.md#streamingpreset) om een beter beeld te krijgen van wat uw mogelijkheden zijn.

Stel de voor instelling niet in op streamen als u niet van plan bent om de video te bekijken, geen video inzichten te indexeren als u alleen audio inzichten nodig hebt.

## <a name="index-in-optimal-resolution-not-highest-resolution"></a>Index in optimale resolutie, niet de hoogste resolutie

Misschien vraagt u zich af welke video kwaliteit u nodig hebt voor het indexeren van uw Video's? 

In veel gevallen heeft het indexeren van prestaties bijna geen verschillen tussen HD-Video's en 4.000 Video's. Uiteindelijk krijgt u bijna dezelfde inzichten met hetzelfde vertrouwen. Hoe hoger de kwaliteit van de film die u uploadt, des te hoger de bestands grootte en dit leidt tot een hogere computer kracht en tijd die nodig is voor het uploaden van de video.

Voor de functie gezichts detectie kan een hogere resolutie bijvoorbeeld helpen bij het scenario waarbij er veel kleine, maar contextuele belang rijke gezichten zijn. Er wordt echter wel een kwadratische toename in runtime geleverd en een verhoogd risico op valse positieven.

Daarom raden we u aan om te controleren of u de juiste resultaten krijgt voor uw use-case en om deze eerst lokaal te testen. Upload dezelfde video in 720P en in 4 KB en vergelijkt de inzichten die u krijgt.

## <a name="next-steps"></a>Volgende stappen

[De Azure Video Indexer-uitvoer controleren die door de API is geproduceerd](video-indexer-output-json-v2.md)