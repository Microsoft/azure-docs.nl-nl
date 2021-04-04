---
title: De Sleuteltermextractie container instantie controleren
titleSuffix: Azure Cognitive Services
description: Meer informatie over het controleren van het exemplaar van de Sleuteltermextractie-container.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 5dca4828a5c1127133461ddf9fc06099fc176b68
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "96009916"
---
### <a name="verify-the-key-phrase-extraction-container-instance"></a>De Sleuteltermextractie container instantie controleren

1. Selecteer het tabblad **overzicht** en kopieer het IP-adres.
1. Open een nieuw browser tabblad en voer het IP-adres in. Voer bijvoorbeeld in `http://<IP-address>:5000 (http://55.55.55.55:5000` ). De start pagina van de container wordt weer gegeven, waarin u kunt zien dat de container wordt uitgevoerd.

    ![De start pagina van de container weer geven om te controleren of deze wordt uitgevoerd](../media/how-tos/container-instance/swagger-docs-on-container.png)

1. Selecteer de koppeling **Service-API-beschrijving** om naar de Swagger-pagina van de container te gaan.

1. Kies een van de **post** -api's en selecteer **try-out**. De para meters worden weer gegeven, met inbegrip van de volgende voorbeeld invoer:

    ```json
    {
      "documents": [
        {
          "id": "1",
          "text": "Hello world"
        },
        {
          "id": "2",
          "text": "Bonjour tout le monde"
        },
        {
          "id": "3",
          "text": "La carretera estaba atascada. Había mucho tráfico el día de ayer."
        },
        {
          "id": "4",
          "text": ":) :( :D"
        }
      ]
    }
    ```

1. Vervang de invoer door de volgende JSON-inhoud:

    ```json
    {
      "documents": [
        {
          "language": "en",
          "id": "7",
          "text": "I was fortunate to attend the KubeCon Conference in Barcelona, it is one of the best conferences I have ever attended. Great people, great sessions and I thoroughly enjoyed it!"
        }
      ]
    }
    ```

1. Stel **showStats** in op `true` .

1. Selecteer **uitvoeren** om de sentiment van de tekst te bepalen.

    Het model dat in de container is verpakt, genereert een Score van 0 tot 1, waarbij 0 negatief is en 1 positief is.

    Het JSON-antwoord dat wordt geretourneerd bevat sentiment voor de bijgewerkte tekst invoer:

    ```json
    {
      "documents": [
        {
          "id": "7",
          "keyPhrases": [
            "Great people",
            "great sessions",
            "KubeCon Conference",
            "Barcelona",
            "best conferences"
          ],
          "statistics": {
            "charactersCount": 176,
            "transactionsCount": 1
          }
        }
      ],
      "errors": [],
      "statistics": {
        "documentsCount": 1,
        "validDocumentsCount": 1,
        "erroneousDocumentsCount": 0,
        "transactionsCount": 1
      }
    }
    ```

We kunnen nu het document `id` van de JSON-gegevens van de reactie Payload koppelen aan het oorspronkelijke document met de aanvraag lading `id` . Het resulterende document heeft een `keyPhrases` matrix die de lijst met sleutel zinnen bevat die zijn geëxtraheerd uit het bijbehorende invoer document. Daarnaast zijn er verschillende statistieken, zoals `characterCount` en `transactionCount` voor elk van de resulterende documenten.