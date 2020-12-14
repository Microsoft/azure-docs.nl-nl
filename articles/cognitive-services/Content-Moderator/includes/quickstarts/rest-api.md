---
title: 'Quickstart: Content Moderator-REST API'
titleSuffix: Azure Cognitive Services
description: In deze quickstart leert u hoe u aan de slag gaat met de Azure Content Moderator-REST API. Voeg software voor het filteren van inhoud toe aan uw app om te voldoen aan de regelgeving of om de beoogde omgeving voor gebruikers te behouden.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: include
ms.date: 12/08/2020
ms.author: pafarley
ms.openlocfilehash: 06af722e8c827acdad356acb982ac3761ef68236
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/09/2020
ms.locfileid: "96905657"
---
Ga aan de slag met de Azure Content Moderator-REST API. 

Content Moderator is een AI-service waarmee u inhoud kunt afhandelen die mogelijk aanstootgevend, riskant of anderszins ongewenst is. Gebruik de service voor inhoudsbeheer op basis van AI, waarmee tekst, afbeeldingen en video's worden gescand en automatisch inhoudsmarkeringen worden toegepast. Integreer vervolgens uw app met het controleprogramma, een onlinebeheeromgeving voor een team van menselijke revisoren. Voeg software voor het filteren van inhoud toe aan uw app om te voldoen aan de regelgeving of om de beoogde omgeving voor gebruikers te behouden.

Gebruik de Content Moderator-REST API voor:

* [Tekst modereren](#moderate-text)
* [Afbeeldingen modereren](#moderate-images)

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [Krijg een gratis abonnement](https://azure.microsoft.com/free/cognitive-services/)
* Zodra u een Azure-abonnement hebt, kunt u <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator"  title="Een Content Moderator-resource maken"  target="_blank">een Content Moderator-resource maken <span class="docon docon-navigate-external x-hidden-focus"></span></a> in Azure Portal om uw sleutel en eindpunt op te halen. Wacht tot deze is geïmplementeerd en klik op de knop **Naar de resource gaan**.
    * U hebt de sleutel en het eindpunt nodig van de resource die u maakt, om de toepassing te verbinden met Content Moderator. Later in de quickstart plakt u uw sleutel en eindpunt in de onderstaande code.
    * U kunt de gratis prijscategorie (`F0`) gebruiken om de service uit te proberen, en later upgraden naar een betaalde laag voor productie.


## <a name="moderate-text"></a>Tekst modereren

U gebruikt een opdracht als wat hier volgt om de Content Moderator-API aan te roepen om een stuk tekst te analyseren en de resultaten af te drukken naar de console.

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/content-moderator/quickstart.sh" ID="textmod":::

Kopieer de opdracht naar een teksteditor en maak de volgende wijzigingen:

1. Wijs `Ocp-Apim-Subscription-Key` toe aan de geldige Face-abonnementssleutel.
1. Wijzig het eerste deel van deze URL, zodat deze overeenkomt met het eindpunt dat bij uw abonnementssleutel hoort.
   [!INCLUDE [subdomains-note](../../../../../includes/cognitive-services-custom-subdomains-note.md)]
1. Wijzig eventueel de hoofdtekst van de aanvraag naar een teksttekenreeks die u wilt analyseren.

Nadat u de wijzigingen hebt aangebracht, opent u een opdrachtprompt en voert u de nieuwe opdracht in. 

### <a name="examine-the-results"></a>De resultaten bekijken

De resultaten van het tekstbeheer worden nu in het consolevenster weergegeven als JSON-gegevens. Bijvoorbeeld:

```json
{
  "OriginalText": "Is this a crap email abcdef@abcd.com, phone: 6657789887, IP: 255.255.255.255,\n1 Microsoft Way, Redmond, WA 98052\n",
  "NormalizedText": "Is this a crap email abide@ abed. com, phone: 6657789887, IP: 255. 255. 255. 255, \n1 Microsoft Way, Redmond, WA 98052",
  "AutoCorrectedText": "Is this a crap email abide@ abed. com, phone: 6657789887, IP: 255. 255. 255. 255, \n1 Microsoft Way, Redmond, WA 98052",
  "Misrepresentation": null,
  "PII": {
    "Email": [
      {
        "Detected": "abcdef@abcd.com",
        "SubType": "Regular",
        "Text": "abcdef@abcd.com",
        "Index": 21
      }
    ],
    "IPA": [
      {
        "SubType": "IPV4",
        "Text": "255.255.255.255",
        "Index": 61
      }
    ],
    "Phone": [
      {
        "CountryCode": "US",
        "Text": "6657789887",
        "Index": 45
      }
    ],
    "Address": [
      {
        "Text": "1 Microsoft Way, Redmond, WA 98052",
        "Index": 78
      }
    ]
  },
 "Classification": {
    "Category1": 
    {
      "Score": 0.5
    },
    "Category2": 
    {
      "Score": 0.6
    },
    "Category3": 
    {
      "Score": 0.5
    },
    "ReviewRecommended": true
  },
  "Language": "eng",
  "Terms": [
    {
      "Index": 10,
      "OriginalIndex": 10,
      "ListId": 0,
      "Term": "crap"
    }
  ],
  "Status": {
    "Code": 3000,
    "Description": "OK",
    "Exception": null
  },
  "TrackingId": "1717c837-cfb5-4fc0-9adc-24859bfd7fac"
}
```

Zie de handleiding [Text moderation concepts](../../text-moderation-api.md) (concepten voor tekstmoderatie) voor informatie over de tekstkenmerken waarop Content Moderator let.

## <a name="moderate-images"></a>Afbeeldingen modereren

U gebruikt een opdracht als wat hier volgt om de Content Moderator-API aan te roepen om een externe afbeelding te modereren en de resultaten af te drukken naar de console.

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/content-moderator/quickstart.sh" ID="imagemod":::

Kopieer de opdracht naar een teksteditor en maak de volgende wijzigingen:

1. Wijs `Ocp-Apim-Subscription-Key` toe aan de geldige Face-abonnementssleutel.
1. Wijzig het eerste deel van deze URL, zodat deze overeenkomt met het eindpunt dat bij uw abonnementssleutel hoort.
1. Wijzig eventueel de URL voor `"Value"` in de hoofdtekst van de aanvraag naar de externe afbeelding die u wilt modereren.

> [!TIP]
> U kunt ook lokale afbeeldingen modereren door de bytegegevens door te geven aan de aanvraagbody. Lees de [documentatie](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c) voor meer informatie over hoe u dit kunt doen.

Nadat u de wijzigingen hebt aangebracht, opent u een opdrachtprompt en voert u de nieuwe opdracht in. 

### <a name="examine-the-results"></a>De resultaten bekijken

De resultaten van het afbeeldingsbeheer worden nu in het consolevenster weergegeven als JSON-gegevens. 

```json
{
  "AdultClassificationScore": x.xxx,
  "IsImageAdultClassified": <Bool>,
  "RacyClassificationScore": x.xxx,
  "IsImageRacyClassified": <Bool>,
  "AdvancedInfo": [],
  "Result": false,
  "Status": {
    "Code": 3000,
    "Description": "OK",
    "Exception": null
  },
  "TrackingId": "<Request Tracking Id>"
}
```

Zie de handleiding [Image moderation concepts](../../image-moderation-api.md) (concepten voor afbeeldingsmoderatie) voor informatie over de afbeeldingskenmerken waarop Content Moderator let.

## <a name="clean-up-resources"></a>Resources opschonen

Als u een Cognitive Services-abonnement wilt opschonen en verwijderen, kunt u de resource of resourcegroep verwijderen. Als u de resourcegroep verwijdert, worden ook alle bijbehorende resources verwijderd.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure-CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u geleerd hoe u de Content Moderator-REST API kunt gebruiken om moderatietaken uit te voeren. Nu kunt u doorgaan en in conceptgids meer lezen over het modereren van afbeeldingen en andere media.

* [Concepten voor afbeeldingsmoderatie](../../image-moderation-api.md)
* [Concepten voor tekstmoderatie](../../text-moderation-api.md)
* [Wat is Azure Content Moderator?](../../overview.md)