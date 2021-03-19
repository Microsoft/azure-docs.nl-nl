---
title: Een persoonlijk model met Video Indexer-API aanpassen
titleSuffix: Azure Media Services
description: Meer informatie over het aanpassen van een persoons model met de Video Indexer-API.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 01/14/2020
ms.author: kumud
ms.openlocfilehash: 09366dea1a0d77052b6f99e9f5ab52c270e341b4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87047012"
---
# <a name="customize-a-person-model-with-the-video-indexer-api"></a>Een persoonlijk model aanpassen met de Video Indexer-API

Video Indexer ondersteunt gezichts detectie en beroemdheden herkenning voor video-inhoud. De functie voor beroemdheden-herkenning gaat over 1.000.000-gezichten op basis van een gevraagde gegevens bron, zoals IMDB, Wikipedia en belangrijkste LinkedIn-gevolgen. Gezichten die niet worden herkend door de functie voor beroemdheden-herkenning, zijn gedetecteerd, maar hebben geen naam gekregen. Nadat u uw video naar Video Indexer hebt geüpload en de resultaten terug hebt opgehaald, kunt u teruggaan en de gezichten benoemen die niet is herkend. Zodra u een gezicht met een naam hebt gelabeld, worden het gezicht en de naam toegevoegd aan het persoonlijke model van uw account. Video Indexer zal dit gezicht vervolgens herkennen in uw toekomstige Video's en eerdere Video's.

U kunt de Video Indexer-API gebruiken om gezichten te bewerken die in een video zijn gedetecteerd, zoals beschreven in dit onderwerp. U kunt ook de Video Indexer website gebruiken, zoals beschreven in [persoonlijk model aanpassen met behulp van de video indexer-website](customize-person-model-with-api.md).

## <a name="managing-multiple-person-models"></a>Meerdere persoons modellen beheren

Video Indexer ondersteunt meerdere persoons modellen per account. Deze functie is momenteel alleen beschikbaar via de Video Indexer-Api's.

Als uw account verantwoordelijk is voor verschillende gebruiks scenario's, wilt u mogelijk meerdere persoons modellen per account maken. Als uw inhoud bijvoorbeeld is gerelateerd aan sport, kunt u vervolgens een afzonderlijk persoons model maken voor elke sport (voetbal, basketbal, voetbal, enzovoort).

Zodra een model is gemaakt, kunt u het gebruiken door de model-ID van een specifiek persoons model op te geven bij het uploaden/indexeren of indexeren van een video. Als u een nieuw gezicht voor een video bijwerkt, wordt het specifieke aangepaste model waarmee de video is gekoppeld, bijgewerkt.

Voor elk account geldt een limiet van 50 persoons modellen. Als u de ondersteuning voor meerdere persoons modellen niet nodig hebt, wijst u geen persoons model-ID toe aan uw video bij het uploaden/indexeren of opnieuw indexeren. In dit geval gebruikt Video Indexer het standaard aangepaste persoons model in uw account.

## <a name="create-a-new-person-model"></a>Een nieuw persoons model maken

Als u een nieuw persoons model wilt maken in het opgegeven account, gebruikt u de API [een persoons model maken](https://api-portal.videoindexer.ai/docs/services/operations/operations/Create-Person-Model?) .

Het antwoord bevat de naam en de gegenereerde model-ID van het persoons model dat u zojuist hebt gemaakt, in de indeling van het onderstaande voor beeld.

```json
{
    "id": "227654b4-912c-4b92-ba4f-641d488e3720",
    "name": "Example Person Model"
}
```

Vervolgens gebruikt u de **id-** waarde voor de para meter **personModelId** wanneer u [een video uploadt om een video te indexeren](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) of opnieuw te [indexeren](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?).

## <a name="delete-a-person-model"></a>Een persoons model verwijderen

Als u een aangepast persoons model wilt verwijderen uit het opgegeven account, gebruikt u de API voor het [verwijderen van een persoons model](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Person-Model?) .

Zodra het persoons model is verwijderd, blijft de index van uw huidige Video's die gebruikmaken van het verwijderde model ongewijzigd totdat u ze opnieuw indexeert. Wanneer u het indexeren opnieuw indexeert, worden de gezichten die in het verwijderde model werden genoemd, niet herkend door Video Indexer in uw huidige Video's die zijn geïndexeerd met het model, maar worden de gezichten nog steeds gedetecteerd. Uw huidige Video's die zijn geïndexeerd met het verwijderde model, gebruiken nu het standaard persoons model van uw account. Als gezichten van het verwijderde model ook worden genoemd in het standaard model van uw account, worden deze gezichten nog steeds herkend in de Video's.

Er is geen inhoud geretourneerd wanneer het persoons model is verwijderd.

## <a name="get-all-person-models"></a>Alle persoons modellen ophalen

Als u alle persoons modellen in het opgegeven account wilt ophalen, gebruikt u de API [een persoons model ophalen](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Person-Models?) .

Het antwoord bevat een lijst met alle persoons modellen in uw account (met inbegrip van het standaard persoons model in het opgegeven account) en elk van de namen en Id's volgens de indeling van het onderstaande voor beeld.

```json
[
    {
        "id": "59f9c326-b141-4515-abe7-7d822518571f",
        "name": "Default"
    }, 
    {
        "id": "9ef2632d-310a-4510-92e1-cc70ae0230d4",
        "name": "Test"
    }
]
```

U kunt kiezen welk model u wilt gebruiken voor een video door de `id` waarde van het persoons model voor de `personModelId` para meter te gebruiken bij het uploaden van [een video om](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) [een video](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?)te indexeren of opnieuw te indexeren.

## <a name="update-a-face"></a>Een gezicht bijwerken

Met deze opdracht kunt u een gezicht in uw video bijwerken met een naam met behulp van de ID van de video en de ID van het gezicht. Met deze actie wordt vervolgens het persoons model bijgewerkt waarmee de video is gekoppeld bij het uploaden/indexeren of opnieuw indexeren. Als er geen persoons model is toegewezen, wordt het standaard persoons model van de account bijgewerkt.

Het systeem herkent vervolgens de instanties van hetzelfde gezicht in uw andere huidige Video's die hetzelfde persoons model delen. De herkenning van het gezicht in uw andere huidige Video's kan enige tijd in beslag nemen omdat dit een batch proces is.

U kunt een gezicht bijwerken dat Video Indexer herkend als een beroemdheden met een nieuwe naam. De nieuwe naam die u geeft, heeft voor rang op de ingebouwde beroemdheden-herkenning.

Als u het gezicht wilt bijwerken, gebruikt u de API [een video face bijwerken](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Video-Face?) .

Namen zijn uniek voor persoons modellen, dus als u twee verschillende gezichten in hetzelfde persoons model dezelfde `name` parameter waarde geeft, video indexer de gezichten weer geven als dezelfde persoon en deze coniciteit zodra u de video opnieuw indexeert.

## <a name="next-steps"></a>Volgende stappen

[Persoonlijk model aanpassen met behulp van de Video Indexer-website](customize-person-model-with-website.md)
