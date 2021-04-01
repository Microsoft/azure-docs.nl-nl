---
title: Toezicht beoordelingen maken met REST API-console-Content Moderator
titleSuffix: Azure Cognitive Services
description: Gebruik de Azure Content Moderator Review-Api's om afbeeldings-of tekst beoordelingen te maken voor menselijke toezicht.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: pafarley
ms.openlocfilehash: 479c7c455f07d098edd327196803e85df24dfb6d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "96905127"
---
# <a name="create-human-reviews-api-console"></a>Menselijke beoordelingen maken (API-console)

[Beoordelingen](./review-api.md#reviews) van de winkel en de weer gave van inhoud voor menselijke moderators om te beoordelen. Wanneer een gebruiker een beoordeling voltooit, worden de resultaten verzonden naar een opgegeven eind punt voor terugbellen. In deze hand leiding leert u hoe u beoordelingen kunt instellen met behulp van de REST-Api's controleren via de API-console. Zodra u de structuur van de Api's begrijpt, kunt u deze aanroepen eenvoudig naar een wille keurig platform met een REST-compatibel poort.

## <a name="prerequisites"></a>Vereisten

- Meld u aan of maak een account op de site van het Content Moderator [controle programma](https://contentmoderator.cognitive.microsoft.com/) .

## <a name="create-a-review"></a>Een beoordeling maken

Als u een beoordeling wilt maken, gaat u naar de pagina voor het maken van API **[-](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4)** verwijzing en selecteert u de knop voor uw sleutel regio (u vindt deze in de URL van het eind punt op de pagina **referenties** van het [hulp programma beoordeling](https://contentmoderator.cognitive.microsoft.com/)). Hiermee wordt de API-console gestart, waar u eenvoudig REST API-aanroepen kunt maken en uitvoeren.

![Beoordeling: de selectie van regio's ophalen](images/test-drive-region.png)

### <a name="enter-rest-call-parameters"></a>REST Call-para meters invoeren

Voer waarden in voor **teamnaam** en **OCP-APIM-Subscription-Key**:

- **team** naam: de team-ID die u hebt gemaakt bij het instellen van het account voor het [beoordelings programma](https://contentmoderator.cognitive.microsoft.com/) (gevonden in het veld **id** op het scherm met de referenties van het controle programma).
- **OCP-APIM-abonnements sleutel**: uw content moderator sleutel. U vindt dit op het tabblad **instellingen** van het [hulp programma voor beoordeling](https://contentmoderator.cognitive.microsoft.com).

### <a name="enter-a-review-definition"></a>Voer een beoordelings definitie in

Bewerk het vak **hoofd tekst** van de aanvraag om de JSON-aanvraag in te voeren met de volgende velden:

- **Meta gegevens**: aangepaste sleutel-waardeparen die moeten worden geretourneerd naar uw call back-eind punt. Als de sleutel een korte code is die in het [beoordelings programma](https://contentmoderator.cognitive.microsoft.com)is gedefinieerd, wordt deze weer gegeven als een tag.
- **Inhoud**: in het geval van afbeeldings-en video-inhoud is dit een URL-teken reeks die verwijst naar de inhoud. Voor tekst inhoud is dit de werkelijke tekst teken reeks.
- **ContentId**: een aangepaste ID-teken reeks. Deze teken reeks wordt door gegeven aan de API en geretourneerd door de retour aanroep. Het is handig om interne id's of meta gegevens te koppelen aan de resultaten van een toezicht taak.
- **CallbackEndpoint**: (optioneel) de URL voor het ontvangen van Terugbel informatie wanneer de controle is voltooid.

De standaard tekst van de aanvraag bevat voor beelden van de verschillende soorten beoordelingen die u kunt maken:

```json
[Image]
[
  {
    "Metadata": [
      {
        "Key": "string",
        "Value": "string"
      }
    ],
    "Type": "Image",
    "Content": "<Content Url>",
    "ContentId": "<Your identifier for this content>",
    "CallbackEndpoint": "<Url where you would receive callbacks>"
  }
]
[Text]
[
  {
    "Metadata": [
      {
        "Key": "string",
        "Value": "string"
      }
    ],
    "Type": "Text",
    "Content": "<Your Text Content>",
    "ContentId": "<Your identifier for this content>",
    "CallbackEndpoint": "<Url where you would receive callbacks>"
  }
]
[Video]
[
  {
    "VideoFrames":[
      {
          "Id": "<Frame Id>",
          "Timestamp": "<Frame Timestamp",
          "FrameImage":"<Frame Image URL",
          "Metadata": [
            {
              "Key": "<Key>",
              "Value": "<Value"
            }
          ],
          "ReviewerResultTags": [
          ]
    ], 
    "Metadata": [
      {
        "Key": "string",
        "Value": "string"
      },
      //For encrypted Videos
        {
          "Key": "protectedType",
          "Value": "AES or FairPlay or Widevine or Playready"
        },
        {
          "Key": "authenticationToken",
          "Value": "your viewtoken(In case of Video Indexer AES encryption type, this value is viewtoken from breakdown json)"
        },
      //For FairPlay encrypted type video include certificateUrl as well
        {
          "Key": "certificateUrl",
          "Value": "your certificate url"
        }
    ],
    "Type": "Video",
    "Content": "<Stream Url>",
    "ContentId": "<Your identifier for this content>",
    "CallbackEndpoint": "<Url where you would receive callbacks>",
    [Optional]
    "Timescale": "<Timescale of the video>
  }
]
```

### <a name="submit-your-request"></a>De aanvraag verzenden
  
Selecteer **Verzenden**. Als de bewerking is geslaagd, is de **reactie status** `200 OK` en wordt in het vak **antwoord inhoud** een id voor de beoordeling weer gegeven. Kopieer deze ID voor gebruik in de volgende stappen.

![Beoordeling: inhoud van het dialoog venster voor het maken van een console-antwoord wordt weer gegeven](images/test-drive-review-2.PNG)

### <a name="examine-the-new-review"></a>Bekijk de nieuwe beoordeling

Selecteer in het [hulp programma beoordeling](https://contentmoderator.cognitive.microsoft.com)de optie   >  **afbeeldings** / **tekst** / **video** bekijken (afhankelijk van de inhoud die u hebt gebruikt). De inhoud die u hebt geüpload, moet worden weer gegeven, gereed voor menselijke beoordeling.

![De afbeelding van een voetbal bekijken](images/test-drive-review-5.PNG)

## <a name="get-review-details"></a>Beoordelingsdetails ophalen

Als u details over een bestaande beoordeling wilt ophalen, gaat u naar de pagina [controleren-API-](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c2) verwijzing en selecteert u de knop voor uw regio (de regio waarin uw sleutel wordt beheerd).

![Werk stroom-selectie van regio ophalen](images/test-drive-region.png)

Voer de para meters voor REST-aanroep in, zoals in de bovenstaande sectie. Voor deze stap is **reviewId** de unieke id-teken reeks die u hebt ontvangen tijdens het maken van de beoordeling.

![Beoordeling: maken van resultaten van console ophalen](images/test-drive-review-3.PNG)
  
Selecteer **Verzenden**. Als de bewerking is geslaagd, is de **antwoord status** `200 OK` en in het vak met de **antwoord inhoud** de details van de controle in JSON-indeling, zoals in het volgende:

```json
{  
  "reviewId":"201712i46950138c61a4740b118a43cac33f434",
  "subTeam":"public",
  "status":"Complete",
  "reviewerResultTags":[  
    {  
      "key":"a",
      "value":"False"
    },
    {  
      "key":"r",
      "value":"True"
    },
    {  
      "key":"sc",
      "value":"True"
    }
  ],
  "createdBy":"<teamname>",
  "metadata":[  
    {  
      "key":"sc",
      "value":"true"
    }
  ],
  "type":"Image",
  "content":"https://reviewcontentprod.blob.core.windows.net/<teamname>/IMG_201712i46950138c61a4740b118a43cac33f434",
  "contentId":"0",
  "callbackEndpoint":"<callbackUrl>"
}
```

Noteer de volgende velden in het antwoord:

- **status**
- **reviewerResultTags**: dit wordt weer gegeven als labels hand matig zijn toegevoegd door het team voor menselijke beoordeling (weer gegeven het **CreatedBy** -veld).
- **meta gegevens**: hier ziet u de tags die in eerste instantie werden toegevoegd aan de beoordeling voordat het personeels team wijzigingen heeft aangebracht.

## <a name="next-steps"></a>Volgende stappen

In deze hand leiding hebt u geleerd hoe u toezicht op inhouds restricties kunt maken met behulp van de REST API. Vervolgens integreert u beoordelingen in een end-to-end-beheer scenario, zoals de zelf studie voor [E-commerce toezicht](./ecommerce-retail-catalog-moderation.md) .