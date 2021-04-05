---
title: Een opdracht bijwerken vanuit een client-app
titleSuffix: Azure Cognitive Services
description: Meer informatie over het bijwerken van een opdracht van een client toepassing.
services: cognitive-services
author: nitinme
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/20/2020
ms.author: nitinme
ms.openlocfilehash: 08c674a7a7ec060a4273836064cb1c21e979e725
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "97560284"
---
# <a name="update-a-command-from-a-client-app"></a>Een opdracht bijwerken vanuit een client-app

In dit artikel leert u hoe u een doorlopende opdracht bijwerkt vanuit een client toepassing.

## <a name="prerequisites"></a>Vereisten
> [!div class = "checklist"]
> * Een eerder [gemaakte aangepaste opdrachten-app](quickstart-custom-commands-application.md)

## <a name="update-the-state-of-a-command"></a>De status van een opdracht bijwerken

Als uw client toepassing vereist dat u de status van een doorlopende opdracht bijwerkt zonder stem invoer, kunt u een gebeurtenis verzenden om de opdracht bij te werken.

Als u dit scenario wilt illustreren, verzendt u de volgende gebeurtenis activiteit voor het bijwerken van de status van een doorlopende opdracht ( `TurnOnOff` ): 

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

Laten we de belangrijkste kenmerken van deze activiteit bekijken:

| Kenmerk | Uitleg |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| **type** | De activiteit is van het type `"event"` en de naam van de gebeurtenis moet zijn `"RemoteUpdate"` . |
| **value** | Het kenmerk `"value"` bevat de kenmerken die nodig zijn om de huidige opdracht bij te werken. |
| **updatedCommand** | Het kenmerk `"updatedCommand"` bevat de naam van de opdracht. Binnen dat kenmerk `"updatedParameters"` is een kaart met de namen van de para meters en de bijgewerkte waarden. |
| **Annuleren** | Als de doorlopende opdracht moet worden geannuleerd, stelt u het kenmerk `"cancel"` in op `true` . |
| **updatedGlobalParameters** | Het kenmerk `"updatedGlobalParameters"` is een kaart op dezelfde manier `"updatedParameters"` , maar wordt gebruikt voor globale para meters. |
| **processTurn** | Als de turn moet worden verwerkt nadat de activiteit is verzonden, stelt u het kenmerk `"processTurn"` in op `true` . |

U kunt dit scenario testen in de portal voor aangepaste opdrachten:

1. Open de toepassing voor aangepaste opdrachten die u eerder hebt gemaakt. 
1. Selecteer **trainen** en vervolgens **testen**.
1. Verzenden `turn` .
1. Open het deel venster aan de zijkant en selecteer **activiteit-editor**.
1. Typ en verzend de `RemoteCommand` gebeurtenis die is opgegeven in de vorige sectie.
    > [!div class="mx-imgBorder"]
    > ![Scherm opname van de gebeurtenis voor een externe opdracht.](media/custom-commands/send-remote-command-activity.png)

U ziet hoe de waarde voor de para meter `"OnOff"` is ingesteld op `"on"` via een activiteit van de client in plaats van spraak of tekst.

## <a name="update-the-catalog-of-the-parameter-for-a-command"></a>De catalogus van de para meter voor een opdracht bijwerken

Wanneer u de lijst met geldige opties voor een para meter configureert, worden de waarden voor de para meter globaal gedefinieerd voor de toepassing. 

In ons voor beeld bevat de `SubjectDevice` para meter een vaste lijst met ondersteunde waarden, ongeacht de conversatie.

Als u per gesprek nieuwe vermeldingen aan de catalogus van de para meter wilt toevoegen, kunt u de volgende activiteit verzenden:

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
Met deze activiteit hebt u een vermelding toegevoegd voor `"stereo"` de catalogus van de para meter `"SubjectDevice"` in de opdracht `"TurnOnOff"` .

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/custom-commands/update-catalog-with-remote-activity.png" alt-text="Scherm afbeelding waarin een catalogus update wordt weer gegeven.":::

Let op een aantal dingen:
- U moet deze activiteit slechts eenmaal verzenden (IDEA liter, direct nadat u een verbinding hebt gestart).
- Nadat u deze activiteit hebt verzonden, moet u wachten tot de gebeurtenis `ParameterCatalogsUpdated` weer naar de client is verzonden.

## <a name="add-more-context-from-the-client-application"></a>Meer context toevoegen vanuit de client toepassing

U kunt een aanvullende context van de client toepassing per conversatie instellen die later kan worden gebruikt in uw toepassing voor aangepaste opdrachten. 

Denk bijvoorbeeld na over het scenario waarin u de ID en naam wilt verzenden van het apparaat dat is verbonden met de toepassing aangepaste opdrachten.

Als u dit scenario wilt testen, kunt u een nieuwe opdracht in de huidige toepassing maken:
1. Maak een nieuwe opdracht met de naam `GetDeviceInfo` .
1. Voeg een voor beeld van een zin toe van `get device info` .
1. Voeg in de voltooiings regel **uitgevoerd** een actie voor het verzenden van een **antwoord** met de kenmerken van toe `clientContext` .
   ![Scherm afbeelding met een reactie op het verzenden van spraak met context.](media/custom-commands/send-speech-response-context.png)
1. Uw toepassing opslaan, trainen en testen.
1. Verzend in het venster testen een activiteit om de client context bij te werken.

    ```json
    {
       "type": "event",
       "name": "RemoteUpdate",
       "value": {
         "clientContext": {
           "deviceId": "12345",
           "deviceName": "My device"
         },
         "processTurn": false
       }
    }
    ```
1. De tekst verzenden `get device info` .
   ![Scherm afbeelding met een activiteit voor het verzenden van de client context.](media/custom-commands/send-client-context-activity.png)

Let op enkele dingen:
- U moet deze activiteit slechts eenmaal verzenden (IDEA liter, direct nadat u een verbinding hebt gestart).
- U kunt complexe objecten gebruiken voor `clientContext` .
- U kunt `clientContext` in spraak reacties gebruiken voor het verzenden van activiteiten en voor het aanroepen van web-eind punten.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een opdracht bijwerken vanuit een webeindpunt](./how-to-custom-commands-update-command-from-web-endpoint.md)
