---
title: Herkenning van animatie-tekens met Video Indexer
titleSuffix: Azure Media Services
description: Hier ziet u hoe u met behulp van animatie teken detectie kunt gebruiken met Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.custom: references_regions
ms.topic: how-to
ms.date: 12/07/2020
ms.author: juliako
ms.openlocfilehash: 1ee179efbe936c742f1eb51b998c10f9349c14fb
ms.sourcegitcommit: 799f0f187f96b45ae561923d002abad40e1eebd6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/24/2020
ms.locfileid: "97763384"
---
# <a name="use-the-animated-character-detection-preview-with-portal-and-api"></a>De functie voor het detecteren van een animatie (preview) gebruiken met de portal en API 

Azure Media Services Video Indexer ondersteunt de detectie, groepering en herkenning van tekens in inhoud met animatie, deze functionaliteit is beschikbaar via de Azure Portal en de API. Bekijk [Dit overzichts](animated-characters-recognition.md) onderwerp.

In dit artikel wordt gedemonstreerd hoe u de herkenning van de animatie met de Azure Portal en de Video Indexer-API gebruikt.

## <a name="use-the-animated-character-detection-with-portal"></a>De herkenning van de animatie gebruiken met portal 

In de proef accounts wordt de integratie van Custom Vision beheerd door Video Indexer, kunt u beginnen met het maken en gebruiken van het model met animatie-tekens. Als u de proef account gebruikt, kunt u de volgende sectie ("verbinding maken met uw Custom Vision-account") overs Laan.

### <a name="connect-your-custom-vision-account-paid-accounts-only"></a>Verbinding maken met uw Custom Vision-account (alleen betaalde accounts)

Als u een Video Indexer betaalde account hebt, moet u eerst verbinding maken met een Custom Vision-account. Als u nog geen Custom Vision-account hebt, moet u er een maken. Zie [Custom Vision](../../cognitive-services/custom-vision-service/overview.md)voor meer informatie.

> [!NOTE]
> Beide accounts moeten zich in dezelfde regio bevinden. De integratie van Custom Vision wordt momenteel niet ondersteund in de Japan-regio.

Betaalde accounts die toegang hebben tot hun Custom Vision-account kunnen de modellen en gelabelde installatie kopieën daar zien. Meer informatie over [het verbeteren van uw classificatie in Custom Vision](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-improving-your-classifier). 

Houd er rekening mee dat de training van het model alleen moet worden uitgevoerd via Video Indexer en niet via de Custom Vision-website. 

#### <a name="connect-a-custom-vision-account-with-api"></a>Een Custom Vision-account verbinden met API 

Volg deze stappen om u te verbinden Custom Vision account te Video Indexer of het Custom Vision account te wijzigen dat momenteel is verbonden met Video Indexer:

1. Ga naar [www.customvision.ai](https://www.customvision.ai) en meld u aan.
1. Kopieer de sleutels voor de resources voor training en voor spellingen:

    > [!NOTE]
    > Om alle sleutels te bieden die u nodig hebt om twee afzonderlijke resources te hebben in Custom Vision, één voor de training en één voor voor spellingen.
1. Andere informatie opgeven:

    * Eindpunt 
    * Resource-ID voor voor spelling
1. Blader en meld u aan bij de [video indexer](https://vi.microsoft.com/).
1. Klik op het vraag teken in de rechter bovenhoek van de pagina en kies API- **verwijzing**.
1. Zorg ervoor dat u bent geabonneerd op API Management door te klikken op het tabblad **producten** . Als u een API hebt verbonden, kunt u door gaan met de volgende stap, anders abonneren. 
1. Op de ontwikkelaars Portal klikt u op de **volledige API-verwijzing** en bladert u naar **bewerkingen**.  
1. Selecteer **verbinding maken Custom Vision account (preview)** en klik op **proberen**.
1. Vul de vereiste velden en het toegangs token in en klik op **verzenden**. 

    Ga naar de [ontwikkelaars Portal](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Account-Access-Token?)voor meer informatie over het verkrijgen van het video indexer toegangs token en Raadpleeg de [relevante documentatie](video-indexer-use-apis.md#obtain-access-token-using-the-authorization-api).  
1. Zodra de aanroep 200 OK reageert, is uw account verbonden.
1. Als u wilt controleren of uw verbinding is, gaat u naar de [video indexer](https://vi.microsoft.com/)-portal:
1. Klik op de knop **aanpassing van inhouds model** in de rechter bovenhoek.
1. Ga naar het tabblad **tekens met animatie** .
1. Nadat u op modellen beheren in Custom Vision hebt geklikt, wordt u overgezet naar het Custom Vision account dat u zojuist hebt verbonden.

> [!NOTE]
> Op dit moment worden alleen modellen ondersteund die zijn gemaakt via Video Indexer. Modellen die via Custom Vision zijn gemaakt, zijn niet beschikbaar. Daarnaast is het best practice om modellen te bewerken die via Video Indexer alleen via het Video Indexer platform zijn gemaakt, omdat wijzigingen die via Custom Vision zijn aangebracht, onbedoelde resultaten kunnen veroorzaken.

### <a name="create-an-animated-characters-model"></a>Een model met een animatie teken maken

1. Ga naar de [Video Indexer](https://vi.microsoft.com/)-website en meld u aan.
1. Als u een model in uw account wilt aanpassen, selecteert u de knop **aanpassing van inhouds model** aan de linkerkant van de pagina.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/content-model-customization/content-model-customization.png" alt-text="Het inhouds model in Video Indexer aanpassen":::
1. Ga naar het tabblad met **animatie tekens** in de sectie model aanpassing.
1. Klik op **model toevoegen**.
1. Geef een naam op voor het model en klik op ENTER om de naam op te slaan.

> [!NOTE]
> Het best practice heeft één aangepast Vision model voor elke animatie reeks. 

### <a name="index-a-video-with-an-animated-model"></a>Een video indexeren met een animatie model

Upload ten minste twee Video's voor de eerste training. Elk moet bij voor keur langer dan 15 minuten zijn voordat een goed herkennings model werd verwacht. Als u een kortere afleveringen hebt, raden we u aan om ten minste 30 minuten aan video-inhoud te uploaden vóór de training. Zo kunt u groepen samen voegen die horen bij hetzelfde teken van verschillende scènes en achtergronden en daarom de kans verg Roten dat het teken wordt gedetecteerd in de volgende afleveringen die u indexeert. Als u een model wilt trainen op meerdere Video's (afleveringen), moet u deze allemaal indexeren met hetzelfde animatie model. 

1. Klik op de knop **uploaden** .
1. Kies een video die u wilt uploaden (vanuit een bestand of een URL).
1. Klik op **Geavanceerde opties**.
1. Onder **personen/animatie tekens** kiest u **animatie modellen**.
1. Als u één model hebt, wordt dit automatisch gekozen en als u meerdere modellen hebt, kunt u de gewenste optie kiezen in het vervolg keuzemenu.
1. Klik op uploaden.
1. Zodra de video is geïndexeerd, worden de gedetecteerde tekens in het gedeelte met **animatie tekens** in het deel venster **inzichten** weer geven.

Voordat u het model kunt labelen en trainen, krijgen alle animatie-tekens de naam onbekend #X. Nadat u het model hebt getraind, worden ze ook herkend.

### <a name="customize-the-animated-characters-models"></a>De modellen met animatie tekens aanpassen

1. Noem de tekens in Video Indexer.

    1. Nadat u het model hebt gemaakt, kunt u deze groepen het beste controleren in Custom Vision. 
    1. Als u een animatie teken in uw video wilt labelen, gaat u naar het tabblad **inzichten**   en klikt u op de knop **bewerken**   in de rechter bovenhoek van het venster. 
    1. Klik in **** het   deel venster inzichten op een van de gedetecteerde animatie tekens en wijzig de namen van ' onbekend #X ' in een tijdelijke naam (of de naam die eerder aan het teken is toegewezen). 
    1. Nadat u de nieuwe naam hebt getypt, klikt u op het controle pictogram naast de nieuwe naam. Hiermee slaat u de nieuwe naam op in het model in Video Indexer. 
1. Alleen betaalde accounts: de groepen in Custom Vision bekijken 

    > [!NOTE]
    > Betaalde accounts die toegang hebben tot hun Custom Vision-account kunnen de modellen en gelabelde installatie kopieën daar zien. Meer informatie over [het verbeteren van uw classificatie in Custom Vision](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-improving-your-classifier). Het is belang rijk te weten dat de training van het model alleen moet worden uitgevoerd via Video Indexer (zoals beschreven in dit topid) en niet via de Custom Vision-website. 

    1. Ga naar de pagina **aangepaste modellen** in video indexer en kies het tabblad met **animatie tekens** . 
    1. Klik op de knop bewerken voor het model waaraan u werkt om deze te beheren in Custom Vision. 
    1. Bekijk elke teken groep: 

        * Als de groep niet-gerelateerde installatie kopieën bevat, wordt aanbevolen deze te verwijderen op de Custom Vision-website. 
        * Als er afbeeldingen zijn die deel uitmaken van een ander teken, wijzigt u de tag op deze specifieke installatie kopieën door te klikken op de afbeelding, de juiste tag toe te voegen en de verkeerde code te verwijderen. 
        * Als de groep niet juist is, wat betekent dat deze voornamelijk niet-teken afbeeldingen of afbeeldingen uit meerdere tekens bevat, kunt u in Custom Vision website of in Video Indexer inzichten verwijderen. 
        * Met de groeperings algoritme worden uw tekens soms gesplitst in verschillende groepen. Het wordt daarom aangeraden om alle groepen die bij hetzelfde teken horen dezelfde naam te geven (in Video Indexer Insights), waardoor deze groepen onmiddellijk worden weer gegeven als op Custom Vision-website. 
    1. Zodra de groep is verfijnd, moet u ervoor zorgen dat de oorspronkelijke naam waarmee u deze hebt gelabeld, overeenkomt met het teken in de groep. 
1. Het model trainen 

    1. Wanneer u klaar bent met het bewerken van alle gewenste namen, moet u het model trainen. 
    1. Zodra een teken is getraind in het model, wordt dit herkend aan de volgende video die met het model is geïndexeerd. 
    1. Open de pagina aanpassing en klik op het tabblad **tekst met animatie**   en klik vervolgens op de knop **trainen** om uw model te trainen. Als u de verbinding tussen video wilt hand haven 
    
Indexeerer en het model, Train het model niet op de Custom Vision website (betaalde accounts hebben alleen toegang tot Custom Vision website), alleen in Video Indexer. Eenmaal getraind, worden de getrainde tekens herkend door alle Video's die worden geïndexeerd of herindexeerd met het model. 

## <a name="delete-an-animated-character-and-the-model"></a>Een animatie teken en het model verwijderen

1. Een animatie teken verwijderen.

    1. Als u een animatie teken in uw video inzichten wilt verwijderen, gaat u naar het tabblad **inzichten** en klikt u op de knop **bewerken** in de rechter bovenhoek van het venster.
    1. Kies het animatie teken en klik vervolgens op de knop **verwijderen** onder hun naam.

    > [!NOTE]
    > Hiermee wordt het inzicht uit deze video verwijderd, maar dit is niet van invloed op het model.
1. Een model verwijderen.

    1. Klik op de knop **aanpassing van inhouds model** in het bovenste menu en ga naar het tabblad **tekens met animatie** .
    1. Klik op het pictogram met het weglatings teken rechts van het model dat u wilt verwijderen en klik vervolgens op de knop verwijderen.
    
    * Betaald account: het model wordt losgekoppeld van Video Indexer en u kunt dit niet opnieuw verbinden.
    * Proef account: het model wordt ook verwijderd uit het douane gezicht. 
    
        > [!NOTE]
        > In een proef account hebt u slechts één model dat u kunt gebruiken. Nadat u de app hebt verwijderd, kunt u geen andere modellen trainen.

## <a name="use-the-animated-character-detection-with-api"></a>De functie voor het detecteren van een animatie met API gebruiken 

1. Verbinding maken met een Custom Vision-account.

    Als u een Video Indexer betaalde account hebt, moet u eerst verbinding maken met een Custom Vision-account. <br/>
    Als u nog geen Custom Vision-account hebt, moet u er een maken. Zie [Custom Vision](../../cognitive-services/custom-vision-service/overview.md)voor meer informatie.

    [Verbind uw Custom Vision-account met behulp van API](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Connect-Custom-Vision-Account?tags=&pattern=&groupBy=tag).
1. Maak een model met animatie-tekens.

    Gebruik de API voor het maken van een [animatie model](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Create-Animation-Model?&groupBy=tag) .
1. Een video indexeren of opnieuw indexeren.

    Gebruik de API voor [opnieuw indexeren](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-Index-Video?) . 
1. Pas de modellen met animatie-tekens aan.

    Gebruik de [Train animatie model](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Train-Animation-Model?&groupBy=tag) -API.

### <a name="view-the-output"></a>De uitvoer weer geven

Bekijk de animatie tekens in het gegenereerde JSON-bestand.

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

* Momenteel wordt de functie voor animatie-identificatie niet ondersteund in East-Asia regio.
* Tekens die klein of ten opzichte van de video worden weer gegeven, worden mogelijk niet goed herkend als de kwaliteit van de video slecht is.
* De aanbeveling is een model te gebruiken per set met animatie tekens (bijvoorbeeld per reeks met animatie).

## <a name="next-steps"></a>Volgende stappen

[Overzicht van Video Indexer](video-indexer-overview.md)
