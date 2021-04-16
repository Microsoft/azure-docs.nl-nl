---
title: Dingen om rekening mee te houden wanneer u Video Indexer schaal gebruikt - Azure
titleSuffix: Azure Media Services
description: In dit onderwerp wordt uitgelegd waar u rekening mee moet houden wanneer u Video Indexer schaal gebruikt.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: how-to
ms.date: 11/13/2020
ms.author: juliako
ms.openlocfilehash: f941d81df670f017d24a7c5011c55fcc4f082605
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531578"
---
# <a name="things-to-consider-when-using-video-indexer-at-scale"></a>Dingen om rekening mee te houden bij Video Indexer op schaal

Wanneer u Azure Media Services Video Indexer gebruikt om video's te indexeren en uw archief van video's groeit, kunt u overwegen om te schalen. 

In dit artikel vindt u antwoorden op vragen als:

* Zijn er technologische beperkingen waar ik rekening mee moet houden?
* Is er een slimme en efficiënte manier om dit te doen?
* Kan ik voorkomen dat ik te veel geld in het proces besteed?

Het artikel bevat zes best practices voor het gebruik van Video Indexer schaal.

## <a name="when-uploading-videos-consider-using-a-url-over-byte-array"></a>Overweeg bij het uploaden van video's het gebruik van een URL via een byte-matrix

Video Indexer u de keuze hebt om video's te uploaden vanuit een URL of rechtstreeks door het bestand als een byte-matrix te verzenden, heeft de laatste een aantal beperkingen. Zie Overwegingen en beperkingen [voor uploaden voor meer informatie.](upload-index-videos.md#uploading-considerations-and-limitations)

Ten eerste gelden er beperkingen voor de bestandsgrootte. De grootte van het byte-matrixbestand is beperkt tot 2 GB vergeleken met de limiet voor de uploadgrootte van 30 GB tijdens het gebruik van DE URL.

Houd ten tweede rekening met enkele van de problemen die van invloed kunnen zijn op uw prestaties en dus op de mogelijkheid om te schalen:

* Het verzenden van bestanden met behulp van meerdere delen betekent een hoge afhankelijkheid van uw netwerk, 
* servicebetrouwbaarheid, 
* Connectiviteit 
* uploadsnelheid, 
* verloren pakketten ergens op het wereldwijde web.

:::image type="content" source="./media/considerations-when-use-at-scale/first-consideration.png" alt-text="Eerste overweging voor het gebruik Video Indexer schaal":::

Wanneer u video's uploadt met behulp van een URL, hoeft u alleen maar een pad op te geven naar de locatie van een mediabestand en Video Indexer zorgt voor de rest (zie het veld in de API voor het uploaden `videoUrl` [van video's).](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Upload-Video)

> [!TIP]
> Gebruik de `videoUrl` optionele parameter van de VIDEO-API uploaden.

Bekijk dit voorbeeld voor een voorbeeld van het uploaden van video's met [url.](upload-index-videos.md#code-sample) U kunt [AzCopy](../../storage/common/storage-use-azcopy-v10.md) ook gebruiken voor een snelle en betrouwbare manier om uw inhoud naar een opslagaccount te verzenden van waaruit u deze kunt verzenden naar Video Indexer met behulp van [sas-URL.](../../storage/common/storage-sas-overview.md)

## <a name="increase-media-reserved-units-if-needed"></a>Gereserveerde media-eenheden verhogen indien nodig

Normaal gesproken hebt u in de proof-of-conceptfase, wanneer u net begint met Video Indexer, niet veel rekenkracht nodig. Wanneer u een groter archief met video's hebt dat u moet indexeren en u wilt dat het proces in een tempo is dat past bij uw use-case, moet u uw gebruik van de Video Indexer. Daarom moet u nadenken over het verhogen van het aantal rekenbronnen dat u gebruikt als de huidige hoeveelheid rekenkracht net niet voldoende is.

In Azure Media Services, wanneer u de rekenkracht en parallellisering wilt vergroten, [](../latest/concept-media-reserved-units.md)moet u aandacht besteden aan gereserveerde media-eenheden (RUs). De EENHEDEN zijn de rekeneenheden die de parameters voor uw mediaverwerkingstaken bepalen. Het aantal RU's is van invloed op het aantal mediataken dat gelijktijdig in elk account kan worden verwerkt. Het type ervan bepaalt de verwerkingssnelheid en voor één video is mogelijk meer dan één RU vereist als het indexeren complex is. Wanneer uw RUs bezet zijn, worden nieuwe taken in een wachtrij geplaatst totdat er een andere resource beschikbaar is.

Om efficiënt te kunnen werken en om te voorkomen dat resources een deel van de tijd inactief blijven, biedt Video Indexer een systeem voor automatisch schalen dat DE's omlaag laat draaien wanneer er minder verwerking nodig is en DE's actief wordt wanneer u zich in de drukke uren (tot volledig gebruik van al uw RE's) belandt. U kunt deze functionaliteit inschakelen [door](manage-account-connected-to-azure.md#autoscale-reserved-units) automatisch schalen in te schakelen in de accountinstellingen of door [Update-Paid-Account-Azure-Media-Services API te gebruiken.](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Update-Paid-Account-Azure-Media-Services)

:::image type="content" source="./media/considerations-when-use-at-scale/second-consideration.jpg" alt-text="Tweede overweging voor het gebruik Video Indexer schaal":::

:::image type="content" source="./media/considerations-when-use-at-scale/reserved-units.jpg" alt-text="Gereserveerde AMS-eenheden":::

Als u de duur van de indexering en lage doorvoer wilt minimaliseren, raden we u aan om te beginnen met 10 RUs van het type S3. Als u later omhoog schaalt om meer inhoud of een hogere gelijktijdigheid te ondersteunen en u meer resources nodig hebt om dit te doen, kunt u [contact](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) met ons opnemen via het ondersteuningssysteem (alleen voor betaalde accounts) om te vragen om meer RUs-toewijzing.

## <a name="respect-throttling"></a>Beperking respecteren

Video Indexer is gebouwd voor het op schaal indexeren. Wanneer u er het meeste uit wilt halen, moet u ook rekening houden met de mogelijkheden van het systeem en uw integratie dienovereenkomstig ontwerpen. U wilt geen uploadaanvraag verzenden voor een batch met video's om te ontdekken dat sommige films niet zijn geüpload en u een HTTP 429-responscode ontvangt (te veel aanvragen). Dit kan gebeuren vanwege het feit dat u meer aanvragen hebt verzonden dan de limiet voor het aantal [films per minuut dat wordt ondersteund.](upload-index-videos.md#uploading-considerations-and-limitations) Video Indexer een header `retry-after` in het HTTP-antwoord toevoegt, geeft de header aan wanneer u de volgende poging moet doen. Zorg ervoor dat u deze respecteert voordat u uw volgende aanvraag probeert.

:::image type="content" source="./media/considerations-when-use-at-scale/respect-throttling.jpg" alt-text="Ontwerp uw integratie goed, respecteer beperking":::

## <a name="use-callback-url"></a>Callback-URL gebruiken

We raden u aan om in plaats van de status van uw aanvraag voortdurend te peilen vanaf het moment dat u de uploadaanvraag hebt verzonden, een [callback-URL](upload-index-videos.md#callbackurl)toe te voegen en te wachten tot Video Indexer u hebt bijgewerkt. Zodra er een statuswijziging in uw uploadaanvraag is, ontvangt u een POST-melding naar de URL die u hebt opgegeven.

U kunt een callback-URL toevoegen als een van de parameters van de [API voor het uploaden van video's.](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Upload-Video) Bekijk de codevoorbeelden in [de GitHub-opslagplaats](https://github.com/Azure-Samples/media-services-video-indexer/tree/master/). 

Voor callback-URL kunt u ook Azure Functions, een serverloos gebeurtenisgestuurd platform dat door HTTP kan worden geactiveerd en een volgende stroom kan implementeren.

### <a name="callback-url-definition"></a>callBack-URL-definitie

[!INCLUDE [callback url](./includes/callback-url.md)]

## <a name="use-the-right-indexing-parameters-for-you"></a>Gebruik de juiste indexeringsparameters voor u

Wanneer u beslissingen neemt met betrekking tot het Video Indexer op schaal, bekijkt u hoe u er het meeste uit kunt halen met de juiste parameters voor uw behoeften. Denk na over uw use-case, door verschillende parameters te definiëren, kunt u geld besparen en het indexeringsproces voor uw video's sneller maken.

Lees deze korte documentatie voordat u [](upload-index-videos.md)uw video uploadt en indexeert, de [indexingPreset](upload-index-videos.md#indexingpreset) en [streamingPreset](upload-index-videos.md#streamingpreset) om een beter beeld te krijgen van uw opties.

Stel de voorinstelling bijvoorbeeld niet in op streaming als u niet van plan bent om de video te bekijken, indexeert geen video-inzichten als u alleen audio-inzichten nodig hebt.

## <a name="index-in-optimal-resolution-not-highest-resolution"></a>Index in optimale resolutie, niet de hoogste resolutie

U vraagt zich misschien af welke videokwaliteit u nodig hebt voor het indexeren van uw video's? 

In veel gevallen heeft het indexeren van prestaties bijna geen verschil tussen HD-video's (720P) en 4.000 video's. Uiteindelijk krijgt u bijna dezelfde inzichten met dezelfde betrouwbaarheid. Hoe hoger de kwaliteit van de film die u uploadt, hoe groter de bestandsgrootte, en dit leidt tot meer rekenkracht en tijd die nodig is om de video te uploaden.

Voor de functie gezichtsdetectie kan een hogere resolutie bijvoorbeeld helpen bij het scenario waarin zich veel kleine, maar contextueel belangrijke gezichten voorkomen. Dit heeft echter te maken met een kwadratische toename van de runtime en een verhoogd risico op fout-positieven.

Daarom raden we u aan om te controleren of u de juiste resultaten voor uw use-case krijgt en om deze eerst lokaal te testen. Upload dezelfde video in 720P en in 4K en vergelijk de inzichten die u krijgt.

## <a name="next-steps"></a>Volgende stappen

[De Uitvoer van Azure Video Indexer geproduceerd door DE API bekijken](video-indexer-output-json-v2.md)