---
title: Detectie van animaties met Video Indexer hoe u
titleSuffix: Azure Media Services
description: Op deze manier wordt gedemonstreerd hoe u detectie van animaties gebruikt met Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.custom: references_regions
ms.topic: how-to
ms.date: 12/07/2020
ms.author: juliako
ms.openlocfilehash: e80699a043d4c18a1bc7ba75ce58c6972fae0fad
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531555"
---
# <a name="use-the-animated-character-detection-preview-with-portal-and-api"></a>De detectie van animaties (preview) gebruiken met de portal en API 

Azure Media Services Video Indexer ondersteuning biedt voor detectie, groepering en herkenning van tekens in animaties, is deze functionaliteit beschikbaar via Azure Portal api. Lees [dit overzichtsonderwerp.](animated-characters-recognition.md)

In dit artikel wordt gedemonstreerd hoe u de detectie van animaties gebruikt met de Azure Portal en de Video Indexer API.

## <a name="use-the-animated-character-detection-with-portal"></a>De detectie van animaties gebruiken met de portal 

In de proefaccounts wordt de Custom Vision beheerd door Video Indexer, kunt u beginnen met het maken en gebruiken van het model met animaties. Als u het proefaccount gebruikt, kunt u de volgende sectie ('Verbinding maken met uw Custom Vision account') overslaan.

### <a name="connect-your-custom-vision-account-paid-accounts-only"></a>Verbinding maken met Custom Vision account (alleen betaalde accounts)

Als u eigenaar bent van Video Indexer betaalde account, moet u eerst verbinding maken met Custom Vision account. Als u nog geen account voor Custom Vision hebt, maakt u er een. Zie voor meer informatie [Custom Vision](../../cognitive-services/custom-vision-service/overview.md).

> [!NOTE]
> Beide accounts moeten zich in dezelfde regio hebben. De Custom Vision integratie wordt momenteel niet ondersteund in de regio Japan.

Betaalde accounts die toegang hebben tot hun Custom Vision kunnen de modellen en gelabelde afbeeldingen daar zien. Meer informatie over [het verbeteren van uw classificatie in Custom Vision](../../cognitive-services/custom-vision-service/getting-started-improving-your-classifier.md). 

Houd er rekening mee dat de training van het model alleen moet worden uitgevoerd via Video Indexer en niet via de Custom Vision website. 

#### <a name="connect-a-custom-vision-account-with-api"></a>Een Custom Vision-account verbinden met API 

Volg deze stappen om uw Custom Vision te koppelen aan Video Indexer of om het Custom Vision-account te wijzigen dat momenteel is verbonden met Video Indexer:

1. Blader naar [www.customvision.ai](https://www.customvision.ai) en meld u aan.
1. Kopieer de sleutels voor de trainings- en voorspellingsbronnen:

    > [!NOTE]
    > Als u alle sleutels wilt bieden, hebt u twee afzonderlijke resources in Custom Vision, één voor training en één voor voorspelling.
1. Geef andere informatie op:

    * Eindpunt 
    * Resource-id voor voorspelling
1. Blader naar en meld u aan bij [Video Indexer](https://vi.microsoft.com/).
1. Klik op het vraagteken in de rechterbovenhoek van de pagina en kies **API-verwijzing.**
1. Zorg ervoor dat u bent geabonneerd op API Management door op het tabblad **Producten te** klikken. Als u een API hebt verbonden, kunt u doorgaan met de volgende stap, anders kunt u zich abonneren. 
1. Klik in de ontwikkelaarsportal op de **Volledige API-verwijzing** en blader naar **Bewerkingen.**  
1. Selecteer **Verbinding Custom Vision account (PREVIEW)** en klik op **Uitproberen.**
1. Vul de vereiste velden en het toegangsken in en klik op **Verzenden.** 

    Voor meer informatie over het verkrijgen van het Video Indexer-token gaat u naar de [ontwikkelaarsportal](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Account-Access-Token)en bekijkt u de [relevante documentatie](video-indexer-use-apis.md#obtain-access-token-using-the-authorization-api).  
1. Zodra de aanroep het antwoord 200 OK retourneerde, is uw account verbonden.
1. Als u de verbinding wilt controleren, bladert u [naar Video Indexer](https://vi.microsoft.com/)) portal:
1. Klik op de **knop Aanpassing inhoudsmodel** in de rechterbovenhoek.
1. Ga naar het **tabblad Animaties.**
1. Wanneer u in de Custom Vision op Modellen beheren klikt, wordt u overgedragen naar Custom Vision account dat u zojuist hebt verbonden.

> [!NOTE]
> Momenteel worden alleen modellen ondersteund die zijn gemaakt via Video Indexer. Modellen die zijn gemaakt via Custom Vision zijn niet beschikbaar. Daarnaast is het best practice modellen te bewerken die zijn gemaakt via Video Indexer alleen via het Video Indexer-platform, omdat wijzigingen die via Custom Vision zijn aangebracht onbedoelde resultaten kunnen veroorzaken.

### <a name="create-an-animated-characters-model"></a>Een model met animaties maken

1. Ga naar de [Video Indexer](https://vi.microsoft.com/)-website en meld u aan.
1. Als u een model in uw account wilt aanpassen, selecteert u de knop **Inhoudsmodel** aanpassen aan de linkerkant van de pagina.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/content-model-customization/content-model-customization.png" alt-text="Inhoudsmodel aanpassen in Video Indexer":::
1. Ga naar het **tabblad Animaties** in de sectie Modelaanpassing.
1. Klik op **Model toevoegen.**
1. Geef het model de naam en klik op Enter om de naam op te slaan.

> [!NOTE]
> De best practice is om voor elke animatiereeks één Custom Vision-model te hebben. 

### <a name="index-a-video-with-an-animated-model"></a>Een video indexeren met een animatiemodel

Upload ten minste twee video's voor de eerste training. Elke moet bij voorkeur langer zijn dan 15 minuten voordat een goed herkenningsmodel wordt verwacht. Als u een kortere periode hebt, raden we u aan om ten minste 30 minuten aan video-inhoud te uploaden vóór de training. Hiermee kunt u groepen samenvoegen die deel uitmaken van hetzelfde teken uit verschillende scènes en achtergronden, en zo de kans vergroten dat het teken wordt gedetecteerd in de volgende index van uw index. Als u een model wilt trainen op meerdere video's (man), moet u ze allemaal indexeren met hetzelfde animatiemodel. 

1. Klik op de **knop** Uploaden.
1. Kies een video die u wilt uploaden (vanuit een bestand of een URL).
1. Klik op **Geavanceerde opties.**
1. Kies **animatiemodellen onder Personen/animaties.** 
1. Als u één model hebt, wordt dit automatisch gekozen en als u meerdere modellen hebt, kunt u het relevante model kiezen in de vervolgkeuzelijst.
1. Klik op Uploaden.
1. Zodra de video is geïndexeerd, ziet  u de gedetecteerde tekens in de sectie **Animaties** in het deelvenster Inzichten.

Voordat u het model gaat taggen en trainen, krijgen alle animaties de naam Onbekend #X. Nadat u het model hebt trainen, worden ze ook herkend.

### <a name="customize-the-animated-characters-models"></a>De modellen met animaties aanpassen

1. Noem de tekens in Video Indexer.

    1. Nadat de tekengroep voor het model is gemaakt, is het raadzaam om deze groepen in de Custom Vision. 
    1. Als u een animatie in uw video wilt taggen, gaat u naar het tabblad **Inzichten** en klikt u op de knop Bewerken in de rechterbovenhoek   van het ****   venster. 
    1. Klik **** in het deelvenster Inzichten op een van de gedetecteerde animaties en wijzig de namen van Onbekende #X in een tijdelijke naam (of de naam die eerder aan het teken was   toegewezen). 
    1. Nadat u de nieuwe naam hebt typen, klikt u op het vinkje naast de nieuwe naam. Hiermee slaat u de nieuwe naam in het model op in Video Indexer. 
1. Alleen betaalde accounts: controleer de groepen in Custom Vision 

    > [!NOTE]
    > Betaalde accounts die toegang hebben tot hun Custom Vision kunnen de modellen en gelabelde afbeeldingen daar zien. Meer informatie over [het verbeteren van uw classificatie in Custom Vision](../../cognitive-services/custom-vision-service/getting-started-improving-your-classifier.md). Het is belangrijk te weten dat de training van het model alleen moet worden uitgevoerd via Video Indexer (zoals beschreven in deze topid) en niet via de Custom Vision website. 

    1. Ga naar de **pagina Aangepaste** modellen in Video Indexer en kies het **tabblad Animaties.** 
    1. Klik op de knop Bewerken voor het model waarmee u werkt om het te beheren in Custom Vision. 
    1. Controleer elke tekengroep: 

        * Als de groep niet-gerelateerde afbeeldingen bevat, is het raadzaam deze te verwijderen in de Custom Vision website. 
        * Als er afbeeldingen zijn die deel uitmaken van een ander teken, wijzigt u de tag op deze specifieke afbeeldingen door op de afbeelding te klikken, de juiste tag toe te voegen en de verkeerde tag te verwijderen. 
        * Als de groep niet juist is, wat betekent dat deze voornamelijk niet-tekenafbeeldingen of afbeeldingen uit meerdere tekens bevat, kunt u deze verwijderen in Custom Vision website of in Video Indexer insights. 
        * Het groeperingsalgoritme splitst uw tekens soms op in verschillende groepen. Daarom is het raadzaam om alle groepen die deel uitmaken van hetzelfde teken dezelfde naam te geven (in Video Indexer Insights), waardoor al deze groepen onmiddellijk worden weergegeven als op de Custom Vision website. 
    1. Nadat de groep is verfijnd, moet u ervoor zorgen dat de initiële naam met wie u de groep hebt getagd, het teken in de groep weerspiegelt. 
1. Het model trainen 

    1. Nadat u klaar bent met het bewerken van alle namen die u wilt, moet u het model trainen. 
    1. Zodra een teken is getraind in het model, wordt het herkend in de volgende video die is geïndexeerd met dat model. 
    1. Open de aanpassingspagina, klik op het **tabblad Animaties** en klik vervolgens op de knop    **Trainen** om uw model te trainen. Om de verbinding tussen video's te behouden 
    
Indexer en het model trainen het model niet op de Custom Vision-website (betaalde accounts hebben toegang tot Custom Vision-website), alleen in Video Indexer. Na de training worden de getrainde tekens herkend in elke video die wordt geïndexeerd of opnieuw wordt geïndexeerd met dat model. 

## <a name="delete-an-animated-character-and-the-model"></a>Een animatie en het model verwijderen

1. Verwijder een animatie.

    1. Als u een animatie in uw video-inzichten wilt verwijderen,  gaat u naar het tabblad Inzichten en klikt u op de knop Bewerken in de rechterbovenhoek van het venster. 
    1. Kies het animatieteken en klik vervolgens op de **knop Verwijderen** onder de naam.

    > [!NOTE]
    > Hiermee verwijdert u het inzicht uit deze video, maar heeft dit geen invloed op het model.
1. Een model verwijderen.

    1. Klik op de **knop Aanpassing inhoudsmodel** in het bovenste menu en ga naar het **tabblad Animaties.**
    1. Klik op het beletselteken rechts van het model dat u wilt verwijderen en klik vervolgens op de knop Verwijderen.
    
    * Betaald account: het model wordt losgekoppeld van Video Indexer en u kunt het niet opnieuw verbinden.
    * Proefaccount: het model wordt ook verwijderd uit Het zicht op de import. 
    
        > [!NOTE]
        > In een proefaccount hebt u slechts één model dat u kunt gebruiken. Nadat u deze hebt verwijderd, kunt u geen andere modellen trainen.

## <a name="use-the-animated-character-detection-with-api"></a>De detectie van animaties met API gebruiken 

1. Verbinding maken met Custom Vision account.

    Als u eigenaar bent Video Indexer betaalde account, moet u eerst verbinding maken met Custom Vision account. <br/>
    Als u nog geen account Custom Vision, maakt u er een. Zie voor meer informatie [Custom Vision](../../cognitive-services/custom-vision-service/overview.md).

    [Verbind uw Custom Vision-account met behulp van API](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Connect-Custom-Vision-Account).
1. Maak een model met animaties.

    Gebruik de [API voor het maken van een animatiemodel.](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Create-Animation-Model)
1. Indexeren of opnieuw indexeren van een video.

    Gebruik de [API voor opnieuw indexeren.](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Re-Index-Video) 
1. Pas de modellen met animaties aan.

    Gebruik de [API voor het trainen van animatiemodellen.](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Train-Animation-Model)

### <a name="view-the-output"></a>De uitvoer weergeven

Bekijk de animaties in het gegenereerde JSON-bestand.

```json
"animatedCharacters": [
    {
    "videoId": "e867214582",
    "confidence": 0,
    "thumbnailId": "00000000-0000-0000-0000-000000000000",
    "seenDuration": 201.5,
    "seenDurationRatio": 0.3175,
    "isKnownCharacter": true,
    "id": 4,
    "name": "Bunny",
    "appearances": [
        {
            "startTime": "0:00:52.333",
            "endTime": "0:02:02.6",
            "startSeconds": 52.3,
            "endSeconds": 122.6
        },
        {
            "startTime": "0:02:40.633",
            "endTime": "0:03:16.6",
            "startSeconds": 160.6,
            "endSeconds": 196.6
        },
    ]
    },
]
```

## <a name="limitations"></a>Beperkingen

* Op dit moment wordt de mogelijkheid voor animatie-identificatie niet ondersteund in East-Asia regio.
* Tekens die klein of ver in de video lijken te zijn, worden mogelijk niet goed geïdentificeerd als de kwaliteit van de video slecht is.
* Het wordt aanbevolen om een model per set bewegende tekens te gebruiken (bijvoorbeeld per animatiereeks).

## <a name="next-steps"></a>Volgende stappen

[Overzicht van Video Indexer](video-indexer-overview.md)