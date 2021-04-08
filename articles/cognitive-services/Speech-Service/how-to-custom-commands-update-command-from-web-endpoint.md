---
title: Een opdracht bijwerken vanuit een webeindpunt
titleSuffix: Azure Cognitive Services
description: Meer informatie over het bijwerken van de status van een opdracht met behulp van een aanroep naar een webeindpunt.
services: cognitive-services
author: nitinme
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/20/2020
ms.author: nitinme
ms.openlocfilehash: d0b77e6af36f0a71405f6c032bfdd121abeb0071
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "97560267"
---
# <a name="update-a-command-from-a-web-endpoint"></a>Een opdracht bijwerken vanuit een webeindpunt

Als voor uw client toepassing een update moet worden uitgevoerd op de status van een doorlopende opdracht zonder stem invoer, kunt u een aanroep naar een webeindpunt gebruiken om de opdracht bij te werken.

In dit artikel leert u hoe u een doorlopende opdracht kunt bijwerken vanuit een webeindpunt.

## <a name="prerequisites"></a>Vereisten
> [!div class = "checklist"]
> * Een eerder [gemaakte aangepaste opdrachten-app](quickstart-custom-commands-application.md)

## <a name="create-an-azure-function"></a>Een Azure-functie maken 

Voor dit voor beeld hebt u een [Azure-functie](../../azure-functions/index.yml) met http geactiveerd die de volgende invoer ondersteunt (of een subset van deze invoer):

```JSON
{
  "conversationId": "SomeConversationId",
  "currentCommand": {
    "name": "SomeCommandName",
    "parameters": {
      "SomeParameterName": "SomeParameterValue",
      "SomeOtherParameterName": "SomeOtherParameterValue"
    }
  },
  "currentGlobalParameters": {
      "SomeGlobalParameterName": "SomeGlobalParameterValue",
      "SomeOtherGlobalParameterName": "SomeOtherGlobalParameterValue"
  }
}
```

Laten we de belangrijkste kenmerken van deze invoer bekijken:

| Kenmerk | Uitleg |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| **conversationId** | De unieke id van de conversatie. Houd er rekening mee dat deze ID kan worden gegenereerd op basis van de client-app. |
| **currentCommand** | De opdracht die momenteel actief is in de conversatie. |
| **name** | De naam van de opdracht. Het `parameters` kenmerk is een toewijzing met de huidige waarden van de para meters. |
| **currentGlobalParameters** | Een kaart zoals `parameters` , die wordt gebruikt voor algemene para meters. |

De uitvoer van de Azure-functie moet de volgende indeling ondersteunen:

```JSON
{
  "updatedCommand": {
    "name": "SomeCommandName",
    "updatedParameters": {
      "SomeParameterName": "SomeParameterValue"
    },
    "cancel": false
  },
  "updatedGlobalParameters": {
    "SomeGlobalParameterName": "SomeGlobalParameterValue"
  }
}
```

U kunt deze indeling herkennen omdat deze hetzelfde is als de naam die u hebt gebruikt bij het [bijwerken van een opdracht van de client](./how-to-custom-commands-update-command-from-client.md). 

Maak nu een Azure-functie op basis van Node.js. Kopieer/plak deze code:

```nodejs
module.exports = async function (context, req) {
    context.log(req.body);
    context.res = {
        body: {
            updatedCommand: {
                name: "IncrementCounter",
                updatedParameters: {
                    Counter: req.body.currentCommand.parameters.Counter + 1
                }
            }
        }
    };
}
```

Wanneer u deze Azure-functie aanroept vanuit aangepaste opdrachten, verzendt u de huidige waarden van de conversatie. U retourneert de para meters die u wilt bijwerken of als u de huidige opdracht wilt annuleren.

## <a name="update-the-existing-custom-commands-app"></a>De bestaande aangepaste opdrachten-app bijwerken

We gaan de Azure-functie koppelen met de bestaande app voor aangepaste opdrachten:

1. Voeg een nieuwe opdracht toe met de naam `IncrementCounter` .
1. Voeg slechts één voorbeeld zin toe met de waarde `increment` .
1. Voeg een nieuwe para meter toe `Counter` (dezelfde naam zoals opgegeven in de Azure-functie) van het type `Number` met de standaard waarde van `0` .
1. Voeg een nieuw webeind punt toe `IncrementEndpoint` met de naam met de URL van uw Azure-functie, waarbij **externe updates** zijn ingesteld op **ingeschakeld**.
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-commands/set-web-endpoint-with-remote-updates.png" alt-text="Scherm afbeelding van het instellen van een webeindpunt met externe updates.":::
1. Maak een nieuwe interactie regel met de naam **IncrementRule** en voeg een **aanroep-Web-eindpunt** actie toe.
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-commands/increment-rule-web-endpoint.png" alt-text="Scherm afbeelding die het maken van een interactie regel weergeeft.":::
1. Selecteer in de actie configuratie `IncrementEndpoint` . Configureer **op geslaagd** om een **antwoord te verzenden** met de waarde van `Counter` en configureer **bij fout** met een fout bericht.
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-commands/set-increment-counter-call-endpoint.png" alt-text="Scherm opname van het instellen van een verhogings teller voor het aanroepen van een webeindpunt.":::
1. Stel de status na uitvoering van de regel in op **wachten op invoer** van de gebruiker.

## <a name="test-it"></a>Testen

1. Sla uw app op en Train deze.
1. Selecteer **Testen**.
1. Verzend `increment` een paar keer (dit is de voor beeld-zin voor de `IncrementCounter` opdracht).
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-commands/increment-counter-example.png" alt-text="Scherm afbeelding van een voor beeld van een oplopend item.":::

U ziet hoe de functie Azure de waarde van de `Counter` para meter op elke beurt verhoogt.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een CI/CD-proces inschakelen voor uw aangepaste opdrachten-toepassing](./how-to-custom-commands-deploy-cicd.md)