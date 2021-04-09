---
title: De Taaldetectie container instantie controleren
titleSuffix: Azure Cognitive Services
description: Meer informatie over het controleren van het exemplaar van de Taaldetectie-container.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 543a4d85982adadc86435819679351c8ffaa9814
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "96009898"
---
### <a name="verify-the-language-detection-container-instance"></a>De Taaldetectie container instantie controleren

1. Selecteer het tabblad **overzicht** en kopieer het IP-adres.
1. Open een nieuw browser tabblad en voer het IP-adres in. Voer bijvoorbeeld in `http://<IP-address>:5000 (http://55.55.55.55:5000` ). De start pagina van de container wordt weer gegeven, waarin u kunt zien dat de container wordt uitgevoerd.

    ![De start pagina van de container weer geven om te controleren of deze wordt uitgevoerd](../media/how-tos/container-instance/swagger-docs-on-container.png)

1. Selecteer de koppeling **Service-API-beschrijving** om naar de Swagger-pagina van de container te gaan.

1. Kies een van de **post** -api's en selecteer **try-out**. De para meters worden weer gegeven, met inbegrip van de volgende voorbeeld invoer:

    ```json
    {
      "documents": [
        {
          "language": "en",
          "id": "1",
          "text": "Hello world. This is some input text that I love."
        },
        {
          "language": "fr",
          "id": "2",
          "text": "Bonjour tout le monde"
        },
        {
          "language": "es",
          "id": "3",
          "text": "La carretera estaba atascada. Había mucho tráfico el día de ayer."
        }
      ]
    }
    ```

1. Stel **showStats** in op `true` .

1. Selecteer **uitvoeren** om de sentiment van de tekst te bepalen.

    Het model dat in de container is verpakt, genereert een Score van 0 tot 1, waarbij 0 negatieve sentiment is en 1 positief sentiment is.

    Het JSON-antwoord dat wordt geretourneerd bevat sentiment voor de bijgewerkte tekst invoer:

    ```json
    {
      "documents": [
        {
          "id": "1",
          "detectedLanguages": [
            {
              "name": "English",
              "iso6391Name": "en",
              "score": 1
            }
          ],
          "statistics": {
            "charactersCount": 11,
            "transactionsCount": 1
          }
        },
        {
          "id": "2",
          "detectedLanguages": [
            {
              "name": "French",
              "iso6391Name": "fr",
              "score": 1
            }
          ],
          "statistics": {
            "charactersCount": 21,
            "transactionsCount": 1
          }
        },
        {
          "id": "3",
          "detectedLanguages": [
            {
              "name": "Spanish",
              "iso6391Name": "es",
              "score": 1
            }
          ],
          "statistics": {
            "charactersCount": 65,
            "transactionsCount": 1
          }
        },
        {
          "id": "4",
          "detectedLanguages": [
            {
              "name": "French",
              "iso6391Name": "fr",
              "score": 1
            }
          ],
          "statistics": {
            "charactersCount": 8,
            "transactionsCount": 1
          }
        }
      ],
      "errors": [],
      "statistics": {
        "documentsCount": 4,
        "validDocumentsCount": 4,
        "erroneousDocumentsCount": 0,
        "transactionsCount": 4
      }
    }
    ```

We kunnen nu de documenten van de gegevens van de nettolading van de reactie aan de oorspronkelijke aanvraag Payload-documenten correleren met hun overeenkomstige `id` . Elk document wordt onafhankelijk behandeld met verschillende statistieken, zoals `characterCount` en `transactionCount` . Daarnaast heeft elk het resulterende document de `detectedLanguages` matrix met de `name` , `iso6391Name` en `score` voor elke gedetecteerde taal. Wanneer er meerdere talen worden gedetecteerd, `score` wordt de gebruikt om de meest waarschijnlijke taal te bepalen.