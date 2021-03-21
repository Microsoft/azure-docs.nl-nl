---
title: Actief leren gebruiken met Knowledge Base-QnA Maker
description: Meer informatie over het verbeteren van de kwaliteit van uw Knowledge Base met actief leren. Beoordeling, accepteren of afwijzen, toevoegen zonder bestaande vragen te verwijderen of te wijzigen.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: 87dde7662050794a24cf976a0bae6237b91d29b2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102213705"
---
# <a name="active-learning"></a>Actief leren

Met de functie voor het gebruik van _actieve trainingen_ kunt u de kwaliteit van uw Knowledge Base verbeteren door alternatieve vragen te stellen, op basis van de gebruikers inzendingen, naar uw vraag en antwoord paar. U kunt deze suggesties bekijken, ofwel toevoegen aan bestaande vragen of afwijzen.

Uw kennis database wordt niet automatisch gewijzigd. Als u een wijziging wilt door voeren, moet u de suggesties accepteren. Deze suggesties Voeg vragen toe, maar u kunt geen bestaande vragen wijzigen of verwijderen.

## <a name="what-is-active-learning"></a>Wat is actief leren?

QnA Maker leert nieuwe vraag varianten met impliciete en expliciete feedback.

* [Impliciete feedback](#how-qna-makers-implicit-feedback-works) : de rang schikking begrijpt wanneer een gebruikers vraag meerdere antwoorden heeft met scores die zeer dicht zijn en die als feedback beschouwt. U hoeft niets te doen om dit te doen.
* [Expliciete feedback](#how-you-give-explicit-feedback-with-the-train-api) : wanneer meerdere antwoorden met weinig variatie in scores worden geretourneerd door de Knowledge Base, vraagt de client toepassing aan de gebruiker welke vraag de juiste vraag is. De expliciete feedback van de gebruiker wordt verzonden naar QnA Maker met de [Train API](../How-To/improve-knowledge-base.md#train-api).

Beide methoden bieden de rang schikking van overeenkomende query's die geclusterd zijn.

## <a name="how-active-learning-works"></a>Werking van actief leren

Actief leren wordt geactiveerd op basis van de scores van de meeste antwoorden die door QnA Maker worden geretourneerd. Als de Score verschillen tussen QnA paren die overeenkomen met de query binnen een klein bereik liggen, wordt de query als een mogelijke suggestie (als een alternatieve vraag) beschouwd voor elk van de mogelijke QnA-paren. Zodra u de voorgestelde vraag voor een specifiek QnA-paar accepteert, wordt het voor de andere paren afgewezen. U moet onthouden om op te slaan en te trainen, nadat u suggesties hebt geaccepteerd.

Actief leren biedt de best mogelijke suggesties in gevallen waarin de eind punten een redelijk aantal en verschillende gebruiks query's verkrijgen. Wanneer vijf of meer soort gelijke query's worden geclusterd, wordt er om de 30 minuten geadviseerd op basis van de gebruikers vragen aan de Knowledge Base QnA Maker-ontwerp functie om te accepteren of te weigeren. Alle suggesties worden geclusterd op basis van de gelijkenis en de meeste suggesties voor alternatieve vragen worden weer gegeven, gebaseerd op de frequentie van de specifieke query's door eind gebruikers.

Zodra vragen worden voorgesteld in de QnA Maker Portal, moet u deze suggesties controleren en accepteren of afwijzen. Er is geen API om suggesties te beheren.

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

## <a name="upgrade-runtime-version-to-use-active-learning"></a>Runtime versie bijwerken om actief leren te gebruiken

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabiele release)](#tab/v1)

Actief leren wordt ondersteund in runtime versie 4.4.0 en hoger. Als uw Knowledge Base is gemaakt in een eerdere versie, moet u [de runtime upgraden](configure-QnA-Maker-resources.md#get-the-latest-runtime-updates) om deze functie te gebruiken.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker beheerd (preview-release)](#tab/v2)

In QnA Maker Managed (preview), omdat de runtime wordt gehost door de QnA Maker-service zelf, hoeft u de runtime niet hand matig bij te werken.

---

## <a name="turn-on-active-learning-for-alternate-questions"></a>Actief leren inschakelen voor alternatieve vragen

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabiele release)](#tab/v1)

Actief leren is standaard uitgeschakeld. Schakel deze in om voorgestelde vragen te bekijken. Nadat u actief leren hebt ingeschakeld, moet u gegevens van de client-app naar QnA Maker verzenden. Zie [de architectuur stroom voor het gebruik van GenerateAnswer en Train api's van een bot](improve-knowledge-base.md#architectural-flow-for-using-generateanswer-and-train-apis-from-a-bot)voor meer informatie.

1. Selecteer **publiceren** om de Knowledge Base te publiceren. Actieve leer query's worden alleen verzameld van het GenerateAnswer API prediction-eind punt. De query's naar het test venster in de QnA Maker Portal hebben geen invloed op actief leren.

1. Als u actief leren wilt inschakelen in de QnA Maker Portal, gaat u naar de rechter bovenhoek en selecteert u uw **naam**. Ga naar [**Service-instellingen**](https://www.qnamaker.ai/UserSettings).

    ![Schakel de voorgestelde vraag van het actieve leer proces in op de pagina Service-instellingen. Selecteer uw gebruikers naam in het menu rechtsboven en selecteer vervolgens Service-instellingen.](../media/improve-knowledge-base/Endpoint-Keys.png)


1. Zoek de QnA Maker-service en schakel vervolgens **actief leren** in.

    > [!div class="mx-imgBorder"]
    > [![Schakel op de pagina Service-instellingen de optie actief leren in. Als u de functie niet kunt in-of uitschakelen, moet u mogelijk een upgrade van uw service uitvoeren.](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png)](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png#lightbox)

    > [!Note]
    > De exacte versie van de voor gaande afbeelding wordt alleen weer gegeven als voor beeld. Uw versie kan afwijken.

    Zodra **actief leren** is ingeschakeld, worden met de Knowledge Base regel matig nieuwe vragen voorgesteld op basis van door de gebruiker ingediende vragen. U kunt **actief leren** uitschakelen door de instelling opnieuw in te scha kelen.
    
# <a name="qna-maker-managed-preview-release"></a>[QnA Maker beheerd (preview-release)](#tab/v2)

Actief leren bevindt zich **standaard in QnA Maker** Managed (preview). Als u de voorgestelde alternatieve vragen wilt zien, [gebruikt u weergave opties](../How-To/improve-knowledge-base.md#view-suggested-questions) op de pagina bewerken.

---

## <a name="review-suggested-alternate-questions"></a>Voorgestelde alternatieve vragen bekijken

[Bekijk alternatieve voorgestelde vragen](improve-knowledge-base.md) op de pagina **bewerken** van elke kennis database.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een kennisdatabase maken](./manage-knowledge-bases.md)
