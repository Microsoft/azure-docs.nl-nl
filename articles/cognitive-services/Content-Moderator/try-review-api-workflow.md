---
title: Beheer werk stromen definiëren met de REST API-console-Content Moderator
titleSuffix: Azure Cognitive Services
description: U kunt de Azure Content Moderator Review-Api's gebruiken om aangepaste werk stromen en drempel waarden te definiëren op basis van uw inhouds beleid.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: pafarley
ms.openlocfilehash: 79749533d636f4b73ff3bef6b12d9e842ac485ea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "96905146"
---
# <a name="define-and-use-moderation-workflows-api-console"></a>Toezicht werk stromen definiëren en gebruiken (API-console)

Werk stromen zijn op de cloud gebaseerde aangepaste filters die u kunt gebruiken om inhoud efficiënter af te handelen. Werk stromen kunnen verbinding maken met verschillende services om inhoud op verschillende manieren te filteren en vervolgens de juiste actie ondernemen. In deze hand leiding wordt beschreven hoe u de werk stroom REST Api's kunt gebruiken via de API-console om werk stromen te maken en te gebruiken. Zodra u de structuur van de Api's begrijpt, kunt u deze aanroepen eenvoudig naar een wille keurig platform met een REST-compatibel poort.

## <a name="prerequisites"></a>Vereisten

- Meld u aan of maak een account op de site van het Content Moderator [controle programma](https://contentmoderator.cognitive.microsoft.com/) .

## <a name="create-a-workflow"></a>Een werkstroom maken

Als u een werk stroom wilt maken of bijwerken, gaat u naar de pagina **[werk stroom-API-verwijzing maken of bijwerken](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59)** en selecteert u de knop voor uw sleutel regio. U vindt uw regio in de eind punt-URL op de pagina **referenties** van het [hulp programma voor controle](https://contentmoderator.cognitive.microsoft.com/). Hiermee wordt de API-console gestart, waar u eenvoudig REST API-aanroepen kunt maken en uitvoeren.

![Werk stroom: selectie van pagina regio maken of bijwerken](images/test-drive-region.png)

### <a name="enter-rest-call-parameters"></a>REST Call-para meters invoeren

Voer waarden in voor **team**, **werk stroomnaam** en **OCP-APIM-Subscription-Key**:

- **team**: de team-ID die u hebt gemaakt bij het instellen van het account voor het [beoordelings programma](https://contentmoderator.cognitive.microsoft.com/) (gevonden in het veld **id** op het scherm met de referenties van het controle programma).
- **workflowactie**: de naam van een nieuwe werk stroom die u wilt toevoegen (of een bestaande naam als u een bestaande werk stroom wilt bijwerken).
- **OCP-APIM-abonnements sleutel**: uw content moderator sleutel. U kunt deze sleutel vinden op het tabblad **instellingen** van het [hulp programma voor beoordeling](https://contentmoderator.cognitive.microsoft.com).

![Werk stroom: query parameters en kopteksten voor console maken of bijwerken](images/workflow-console-parameters.PNG)

### <a name="enter-a-workflow-definition"></a>Een werk stroom definitie invoeren

1. Bewerk het vak **hoofd tekst** van de aanvraag om de JSON-aanvraag in te voeren met Details voor de **Beschrijving** en het **type** ( `Image` of `Text` ).
2. Voor **expressie** kopieert u de standaard JSON-expressie voor de werk stroom. De uiteindelijke JSON-teken reeks moet er als volgt uitzien:

```json
{
  "Description":"<A description for the Workflow>",
  "Type":"Text",
  "Expression":{
    "Type":"Logic",
    "If":{
      "ConnectorName":"moderator",
      "OutputName":"isAdult",
      "Operator":"eq",
      "Value":"true",
      "Type":"Condition"
    },
    "Then":{
      "Perform":[
        {
          "Name":"createreview",
          "CallbackEndpoint":null,
          "Tags":[

          ]
        }
      ],
      "Type":"Actions"
    }
  }
}
```

> [!NOTE]
> Met deze API kunt u eenvoudige, complexe en zelfs geneste expressies definiëren voor uw werk stromen. De documentatie voor het [maken of bijwerken van werk stromen](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59) bevat voor beelden van complexere logica.

### <a name="submit-your-request"></a>De aanvraag verzenden
  
Selecteer **Verzenden**. Als de bewerking is geslaagd, is de **reactie status** `200 OK` en wordt het vak **antwoord inhoud** weer gegeven `true` .

### <a name="examine-the-new-workflow"></a>De nieuwe werk stroom controleren

In het [hulp programma controleren](https://contentmoderator.cognitive.microsoft.com/)selecteert u **instellingen**  >  **werk stromen**. De nieuwe werk stroom wordt weer gegeven in de lijst.

![Lijst met hulpprogram ma's voor beoordeling van werk stromen](images/workflow-console-new-workflow.PNG)

Selecteer de optie **bewerken** voor uw werk stroom en ga naar het tabblad **ontwerp** . Hier ziet u een intuïtieve representatie van de JSON-logica.

![Tabblad ontwerpen voor een geselecteerde werk stroom](images/workflow-console-new-workflow-designer.PNG)

## <a name="get-workflow-details"></a>Werk stroom Details ophalen

Als u details over een bestaande werk stroom wilt ophalen, gaat u naar de pagina **[werk stroom-API-](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b44b3f9b0711b43c4c58)** verwijzing en selecteert u de knop voor uw regio (de regio waarin uw sleutel wordt beheerd).

![Werk stroom-selectie van regio ophalen](images/test-drive-region.png)

Voer de para meters voor REST-aanroep in, zoals in de bovenstaande sectie. Controleer of deze keer de naam van een **bestaande werk stroom is.**

![Query parameters en kopteksten ophalen](images/workflow-get-default.PNG)

Selecteer **Verzenden**. Als de bewerking is geslaagd, is de **reactie status** en wordt in `200 OK` het vak **reactie inhoud** de werk stroom in JSON-indeling weer gegeven, zoals in het volgende voor beeld:

```json
{
  "Name":"default",
  "Description":"Default",
  "Type":"Image",
  "Expression":{
    "If":{
      "ConnectorName":"moderator",
      "OutputName":"isadult",
      "Operator":"eq",
      "Value":"true",
      "AlternateInput":null,
      "Type":"Condition"
    },
    "Then":{
      "Perform":[
        {
          "Name":"createreview",
          "Subteam":null,
          "CallbackEndpoint":null,
          "Tags":[

          ]
        }
      ],
      "Type":"Actions"
    },
    "Else":null,
    "Type":"Logic"
  }
}
```

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het gebruik van werk stromen met [taken voor inhouds toezicht](try-review-api-job.md).