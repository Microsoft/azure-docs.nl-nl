---
title: Een opdracht bijwerken vanuit de client-app
titleSuffix: Azure Cognitive Services
description: een opdracht bijwerken vanuit de client-app
services: cognitive-services
author: encorona-ms
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/20/2020
ms.author: encorona
ms.openlocfilehash: 1bffb09d0f49bbd0059e8a528d67bfe215f0650d
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94654342"
---
# <a name="update-a-command-from-the-client"></a>Een opdracht van de client bijwerken

In dit artikel leert u hoe u een doorlopende opdracht bijwerkt vanuit een client toepassing.

## <a name="prerequisites"></a>Vereisten
> [!div class = "checklist"]
> * Een eerder [gemaakte aangepaste opdrachten-app](quickstart-custom-commands-application.md)

## <a name="update-the-state-of-a-command"></a>De status van een opdracht bijwerken

Als uw client toepassing de status van een doorlopende opdracht moet bijwerken zonder stem invoer, kunt u een gebeurtenis verzenden om de opdracht bij te werken.

Ter illustratie van dit scenario kan het bijwerken van de status van een doorlopende opdracht (TurnOnOff) de volgende gebeurtenis activiteit verzenden. 

```json
{
  "type": "event",
  "name": "RemoteUpdate",
  "value": {
    "updatedCommand": {
      "name": "TurnOnOff",
      "updatedParameters": {
        "OnOff": "on"
      },
      "cancel": false
    },
    "updatedGlobalParameters": {},
    "processTurn": true
  }
}
```

Hiermee worden de belangrijkste kenmerken van deze activiteit gecontroleerd.

| Kenmerk | Uitleg |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| **type** | De activiteit is van het type Event en de naam van de gebeurtenis moet RemoteUpdate zijn. |
| **value** | Het kenmerk ' value ' bevat de kenmerken die nodig zijn om de huidige opdracht bij te werken. |
| **updatedCommand** | Het kenmerk ' updatedCommand ' bevat de naam van de opdracht, ' updatedParameters ' is een toewijzing met de naam van de para meters en de bijgewerkte waarden. |
| **Annuleren** | Als de doorlopende opdracht moet worden geannuleerd, stelt u het kenmerk annuleren in op waar. |
| **updatedGlobalParameters** | Het kenmerk ' updatedGlobalParameters ' is ook een kaart, net als ' updatedParameters ', maar wordt gebruikt voor globale para meters. |
| **processTurn** | Als de turn moet worden verwerkt nadat de activiteit is verzonden, stelt u het kenmerk ' processTurn ' in op ' True '. |

U kunt dit scenario testen in de portal voor aangepaste opdrachten.

1. Open de aangepaste opdrachten-toepassing die u eerder hebt gemaakt. 
1. Klik op trainen en vervolgens op testen.
1. ' Turn ' verzenden.
1. Open het deel venster aan de zijkant en klik op activiteit-editor.
1. Typ en verzend de gebeurtenis RemoteCommand die is opgegeven in de vorige sectie.
    > [!div class="mx-imgBorder"]
    > ![Opdracht externe verzenden](media/custom-commands/send-remote-command-activity.png)

U ziet hoe de waarde voor de para meter ' OnOff ' is ingesteld op ' aan ' met behulp van een activiteit van de client in plaats van spraak of tekst.

## <a name="update-the-catalog-of-the-parameter-for-a-command"></a>De catalogus van de para meter voor een opdracht bijwerken

Wanneer u de lijst met geldige opties voor een para meter configureert, worden de waarden voor de para meter globaal gedefinieerd voor de toepassing. 

In ons voor beeld bevat de para meter SubjectDevice een vaste lijst met ondersteunde waarden, ongeacht de conversatie.

Als u per conversatie nieuwe vermeldingen wilt toevoegen aan de catalogus van de para meter, kunt u de volgende activiteit verzenden.

```json
{
  "type": "event",
  "name": "RemoteUpdate",
  "value": {
    "catalogUpdate": {
      "commandParameterCatalogs": {
        "TurnOnOff": [
          {
            "name": "SubjectDevice",
            "values": {
              "stereo": [
                "cd player"
              ]
            }
          }
        ]
      }
    },
    "processTurn": false
  }
}
```
Met deze activiteit hebben we een vermelding voor ' stereo ' toegevoegd aan de catalogus van de para meter ' SubjectDevice ' in de opdracht ' TurnOnOff '.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/custom-commands/update-catalog-with-remote-activity.png" alt-text="Catalogus bijwerken":::

Let op enkele dingen.
1. U hoeft deze activiteit slechts eenmaal te verzenden (in het ideale geval nadat u een verbinding hebt gestart).
1. Nadat u deze activiteit hebt verzonden, moet u wachten tot de gebeurtenis ParameterCatalogsUpdated terug naar de client is verzonden.

## <a name="add-additional-context-from-the-client-application"></a>Aanvullende context toevoegen vanuit de client toepassing

U kunt een aanvullende context van de client toepassing per conversatie instellen die later kan worden gebruikt in uw toepassing voor aangepaste opdrachten. 

Denk bijvoorbeeld na over het scenario waarin u de ID en naam wilt verzenden van het apparaat dat is verbonden met de toepassing aangepaste opdrachten.

Als u dit scenario wilt testen, gaan we een nieuwe opdracht in onze huidige toepassing maken.
1. Maak een nieuwe opdracht met de naam GetDeviceInfo.
1. Voeg een voorbeeld zin toe met ' apparaatgegevens ophalen '.
1. Voeg in de voltooiings regel ' gereed ' een actie voor het verzenden van een spraak bewerking toe die de kenmerken van de clientContext bevat.
    > ![Spraak antwoord met context verzenden](media/custom-commands/send-speech-response-context.png)
1. Uw toepassing opslaan, trainen en testen.
1. In het test venster kunt u een activiteit verzenden om de client context bij te werken.
    > ```json
    >{
    >   "type": "event",
    >   "name": "RemoteUpdate",
    >   "value": {
    >     "clientContext": {
    >       "deviceId": "12345",
    >       "deviceName": "My device"
    >     },
    >     "processTurn": false
    >   }
    >}
    > ```
1. De tekst ' Get Device Info ' verzenden.
    > ![Client context activiteit verzenden](media/custom-commands/send-client-context-activity.png)

Let op enkele dingen.
1. U hoeft deze activiteit slechts eenmaal te verzenden (in het ideale geval nadat u een verbinding hebt gestart).
1. U kunt complexe objecten gebruiken voor clientContext.
1. U kunt clientContext gebruiken in spraak reacties, voor het verzenden van activiteiten en bij het aanroepen van web-eind punten.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een opdracht bijwerken vanuit een webeindpunt](./how-to-custom-commands-update-command-from-web-endpoint.md)
