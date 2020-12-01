---
title: Suggesties voor actieve trainingen-QnA Maker
description: Met actieve Learning suggesties kunt u de kwaliteit van uw Knowledge Base verbeteren door alternatieve vragen te stellen op basis van de gebruikers inzendingen, op uw vraag en antwoord paar.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.openlocfilehash: e1a8043912c984be46f85bd384a7049da27028b3
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96353235"
---
# <a name="active-learning-suggestions"></a>Suggesties voor actieve trainingen

Met de functie voor het gebruik van _actieve trainingen_ kunt u de kwaliteit van uw Knowledge Base verbeteren door alternatieve vragen te stellen, op basis van de gebruikers inzendingen, naar uw vraag en antwoord paar. U kunt deze suggesties bekijken, ofwel toevoegen aan bestaande vragen of afwijzen.

Uw kennis database wordt niet automatisch gewijzigd. Als u een wijziging wilt door voeren, moet u de suggesties accepteren. Deze suggesties Voeg vragen toe, maar u kunt geen bestaande vragen wijzigen of verwijderen.

## <a name="what-is-active-learning"></a>Wat is actief leren?

QnA Maker leert nieuwe vraag varianten met impliciete en expliciete feedback.

* [Impliciete feedback](#how-qna-makers-implicit-feedback-works) : de rang schikking begrijpt wanneer een gebruikers vraag meerdere antwoorden heeft met scores die zeer dicht zijn en die als feedback beschouwt. U hoeft niets te doen om dit te doen.
* [Expliciete feedback](#how-you-give-explicit-feedback-with-the-train-api) : wanneer meerdere antwoorden met weinig variatie in scores worden geretourneerd door de Knowledge Base, vraagt de client toepassing aan de gebruiker welke vraag de juiste vraag is. De expliciete feedback van de gebruiker wordt verzonden naar QnA Maker met de [Train API](../How-to/improve-knowledge-base.md#train-api).

Beide methoden bieden de rang schikking van overeenkomende query's die geclusterd zijn.

## <a name="how-active-learning-works"></a>Werking van actief leren

Actief leren wordt geactiveerd op basis van de scores van de meeste antwoorden die door QnA Maker worden geretourneerd. Als de Score verschillen tussen QnA paren die overeenkomen met de query binnen een klein bereik liggen, wordt de query als een mogelijke suggestie (als een alternatieve vraag) beschouwd voor elk van de mogelijke QnA-paren. Zodra u de voorgestelde vraag voor een specifiek QnA-paar accepteert, wordt het voor de andere paren afgewezen. U moet onthouden om op te slaan en te trainen, nadat u suggesties hebt geaccepteerd.

Actief leren biedt de best mogelijke suggesties in gevallen waarin de eind punten een redelijk aantal en verschillende gebruiks query's verkrijgen. Wanneer vijf of meer soort gelijke query's worden geclusterd, wordt er om de 30 minuten geadviseerd op basis van de gebruikers vragen aan de Knowledge Base QnA Maker-ontwerp functie om te accepteren of te weigeren. Alle suggesties worden geclusterd op basis van de gelijkenis en de meeste suggesties voor alternatieve vragen worden weer gegeven, gebaseerd op de frequentie van de specifieke query's door eind gebruikers.

Zodra vragen worden voorgesteld in de QnA Maker Portal, moet u deze suggesties controleren en accepteren of afwijzen. Er is geen API om suggesties te beheren.

## <a name="turn-on-active-learning"></a>Actief leren inschakelen

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabiele release)](#tab/v1)

Actief leren is standaard **uitgeschakeld**.
Actief leren gebruiken:
* U moet [actief leren inschakelen](../How-To/use-active-learning.md#turn-on-active-learning-for-alternate-questions) zodat QnA Maker alternatieve vragen voor uw Knowledge Base verzamelt.
* Als u de voorgestelde alternatieve vragen wilt zien, [gebruikt u weergave opties](../How-To/improve-knowledge-base.md#view-suggested-questions) op de pagina bewerken.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker beheerd (preview-release)](#tab/v2)

Actief leren bevindt zich **standaard in QnA Maker** Managed (preview). Als u de voorgestelde alternatieve vragen wilt zien, [gebruikt u weergave opties](../How-To/improve-knowledge-base.md#view-suggested-questions) op de pagina bewerken.

---

## <a name="how-qna-makers-implicit-feedback-works"></a>Hoe de impliciete feedback van QnA Maker werkt

De impliciete feedback van QnA Maker maakt gebruik van een algoritme om de Score nabijheid te bepalen en maakt vervolgens suggesties voor het actief leren. Het algoritme voor het bepalen van de nabijheid is geen eenvoudige berekening. De bereiken in het volgende voor beeld zijn niet bedoeld om te worden hersteld, maar moeten worden gebruikt als richt lijn om alleen de impact van het algoritme te begrijpen.

Wanneer de Score van een vraag zeer betrouwbaar is, zoals 80%, is het bereik van de scores die worden overwogen voor actief onderwijs breed, ongeveer 10%. Naarmate de betrouwbaarheids Score, zoals 40%, afneemt, wordt het bereik van de scores in ongeveer 4% verkleind.

In de volgende JSON-reactie van een query naar de generateAnswer van QnA Maker zijn de scores voor A, B en C in de buurt en worden ze als suggesties beschouwd.

```json
{
  "activeLearningEnabled": true,
  "answers": [
    {
      "questions": [
        "Q1"
      ],
      "answer": "A1",
      "score": 80,
      "id": 15,
      "source": "Editorial",
      "metadata": [
        {
          "name": "topic",
          "value": "value"
        }
      ]
    },
    {
      "questions": [
        "Q2"
      ],
      "answer": "A2",
      "score": 78,
      "id": 16,
      "source": "Editorial",
      "metadata": [
        {
          "name": "topic",
          "value": "value"
        }
      ]
    },
    {
      "questions": [
        "Q3"
      ],
      "answer": "A3",
      "score": 75,
      "id": 17,
      "source": "Editorial",
      "metadata": [
        {
          "name": "topic",
          "value": "value"
        }
      ]
    },
    {
      "questions": [
        "Q4"
      ],
      "answer": "A4",
      "score": 50,
      "id": 18,
      "source": "Editorial",
      "metadata": [
        {
          "name": "topic",
          "value": "value"
        }
      ]
    }
  ]
}
```

QnA Maker weet niet welk antwoord het beste antwoord is. Gebruik de lijst met suggesties van de QnA Maker Portal om het beste antwoord en Train opnieuw te selecteren.


## <a name="how-you-give-explicit-feedback-with-the-train-api"></a>Expliciete feedback geven met de trein-API

QnA Maker expliciete feedback nodig hebt over welke van de antwoorden het beste antwoord is. Hoe het beste antwoord wordt bepaald, is aan u toe te voegen en kan het volgende omvatten:

* Feedback van gebruikers, selecteert u een van de antwoorden.
* Bedrijfs logica, zoals het bepalen van een acceptabel Score bereik.
* Een combi natie van feedback van gebruikers en bedrijfs logica.

Gebruik de [Train API](/rest/api/cognitiveservices/qnamaker4.0/runtime/train) om het juiste antwoord naar QnA Maker te verzenden nadat de gebruiker dit heeft geselecteerd.

## <a name="next-step"></a>Volgende stap

> [!div class="nextstepaction"]
> [Query's uitvoeren op de Knowledge Base](query-knowledge-base.md)