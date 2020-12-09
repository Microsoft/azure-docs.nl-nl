---
title: Gebruik moderators taken met de REST API-console-Content Moderator
titleSuffix: Azure Cognitive Services
description: Gebruik de taak bewerkingen van de beoordelings-API om end-to-end taken voor het beheer van inhoud te initiëren voor afbeeldings-of tekst inhoud in azure Content Moderator.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: pafarley
ms.openlocfilehash: 924c21037a464770fac13c9b45ddcf261ff5a058
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/09/2020
ms.locfileid: "96905136"
---
# <a name="define-and-use-moderation-jobs-api-console"></a>Toezicht taken definiëren en gebruiken (API-console)

Een toezicht taak fungeert als een soort wrapper voor de functionaliteit van toezicht op inhoud, werk stromen en Beoordelingen. In deze hand leiding wordt beschreven hoe u de taak REST-Api's kunt gebruiken om taken voor inhouds toezicht te initiëren en te controleren. Zodra u de structuur van de Api's begrijpt, kunt u deze aanroepen eenvoudig naar een wille keurig platform met een REST-compatibel poort.

## <a name="prerequisites"></a>Vereisten

- Meld u aan of maak een account op de site van het Content Moderator [controle programma](https://contentmoderator.cognitive.microsoft.com/) .
- Beschrijving [Definieer een aangepaste werk stroom](./Review-Tool-User-Guide/Workflows.md) voor gebruik met uw taak. u kunt ook de standaardwerk stroom gebruiken.

## <a name="create-a-job"></a>Een taak maken

Als u een toezicht taak wilt maken, gaat u naar de [taak-API-](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c5) referentie pagina maken en selecteert u de knop voor uw abonnements regio. U vindt uw regio in de eind punt-URL op de pagina **referenties** van het [hulp programma voor controle](https://contentmoderator.cognitive.microsoft.com/). Hiermee wordt de API-console gestart, waar u eenvoudig REST API-aanroepen kunt maken en uitvoeren.

![Taak-selectie van pagina regio maken](images/test-drive-job-1.png)

### <a name="enter-rest-call-parameters"></a>REST Call-para meters invoeren

Voer de volgende waarden in om de REST-aanroep te maken:

- **team** naam: de team-ID die u hebt gemaakt bij het instellen van het account voor het [beoordelings programma](https://contentmoderator.cognitive.microsoft.com/) (gevonden in het veld **id** op het scherm met de referenties van het controle programma).
- **Content type**: dit kan ' afbeelding ', ' tekst ' of ' video ' zijn.
- **ContentId**: een aangepaste ID-teken reeks. Deze teken reeks wordt door gegeven aan de API en geretourneerd door de retour aanroep. Het is handig om interne id's of meta gegevens te koppelen aan de resultaten van een toezicht taak.
- **Workflowactie**: de naam van de werk stroom die u eerder hebt gemaakt (of ' standaard ' voor de standaardwerk stroom).
- **CallbackEndpoint**: (optioneel) de URL voor het ontvangen van Terugbel informatie wanneer de controle is voltooid.
- **OCP-APIM-abonnements sleutel**: uw content moderator sleutel. U kunt deze sleutel vinden op het tabblad **instellingen** van het [hulp programma voor beoordeling](https://contentmoderator.cognitive.microsoft.com).

### <a name="fill-in-the-request-body"></a>Vul de hoofd tekst van de aanvraag in

De hoofd tekst van uw REST-aanroep bevat één veld, **ContentValue**. Plak de inhoud van de onbewerkte tekst als u toezicht houdt op tekst, of voer een afbeelding of video-URL in als u een afbeelding of video wilt beelementen. U kunt de volgende voorbeeld afbeeldings-URL gebruiken: [https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg](https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg)

![Taak: console-query parameters, kopteksten en hoofd tekst van aanvraag maken](images/job-api-console-inputs.PNG)

### <a name="submit-your-request"></a>De aanvraag verzenden

Selecteer **Verzenden**. Als de bewerking is geslaagd, is de **reactie status** `200 OK` en wordt in het vak **antwoord inhoud** een id voor de taak weer gegeven. Kopieer deze ID voor gebruik in de volgende stappen.

![Beoordeling: inhoud van het dialoog venster voor het maken van een console-antwoord wordt weer gegeven](images/test-drive-job-3.PNG)

## <a name="get-job-status"></a>Taak status ophalen

Als u de status en Details van een actieve of voltooide taak wilt ophalen, gaat u naar de [taak-API-](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c3) referentie pagina en selecteert u de knop voor uw regio (de regio waarin uw sleutel wordt beheerd).

![Taak-regio selectie ophalen](images/test-drive-region.png)

Voer de para meters voor REST-aanroep in, zoals in de bovenstaande sectie. Voor deze stap is **JobId** de unieke id die u hebt ontvangen tijdens het maken van de taak. Selecteer **Verzenden**. Als de bewerking is geslaagd, is de **antwoord status** en wordt de `200 OK` taak in de JSON-indeling weer gegeven in het vak met de **antwoord inhoud** , zoals in het volgende voor beeld:

```json
{  
  "Id":"2018014caceddebfe9446fab29056fd8d31ffe",
  "TeamName":"some team name",
  "Status":"Complete",
  "WorkflowId":"OCR",
  "Type":"Image",
  "CallBackEndpoint":"",
  "ReviewId":"201801i28fc0f7cbf424447846e509af853ea54",
  "ResultMetaData":[  
    {  
      "Key":"hasText",
      "Value":"True"
    },
    {  
      "Key":"ocrText",
      "Value":"IF WE DID \r\nALL \r\nTHE THINGS \r\nWE ARE \r\nCAPABLE \r\nOF DOING, \r\nWE WOULD \r\nLITERALLY \r\nASTOUND \r\nOURSELVE \r\n"
    }
  ],
  "JobExecutionReport":[  
    {  
      "Ts":"2018-01-07T00:38:29.3238715",
      "Msg":"Posted results to the Callbackendpoint: https://requestb.in/vxke1mvx"
    },
    {  
      "Ts":"2018-01-07T00:38:29.2928416",
      "Msg":"Job marked completed and job content has been removed"
    },
    {  
      "Ts":"2018-01-07T00:38:29.0856472",
      "Msg":"Execution Complete"
    },
    {  
      "Ts":"2018-01-07T00:38:26.7714671",
      "Msg":"Successfully got hasText response from Moderator"
    },
    {  
      "Ts":"2018-01-07T00:38:26.4181346",
      "Msg":"Getting hasText from Moderator"
    },
    {  
      "Ts":"2018-01-07T00:38:25.5122828",
      "Msg":"Starting Execution - Try 1"
    }
  ]
}
```

![Taak-antwoord van REST-aanroep ophalen](images/test-drive-job-5.png)

### <a name="examine-the-new-reviews"></a>Bekijk de nieuwe beoordeling (en)

Als uw inhouds taak heeft geresulteerd in het maken van een beoordeling, kunt u deze weer geven in het [beoordelings programma](https://contentmoderator.cognitive.microsoft.com). Selecteer **Review**  >  **afbeeldings** / **tekst** / **video** bekijken (afhankelijk van de inhoud die u hebt gebruikt). De inhoud moet worden weer gegeven, gereed voor menselijke beoordeling. Nadat een Human toezicht houder de automatisch toegewezen tags en Voorspellings gegevens heeft beoordeeld en een beslissing van definitieve controle indient, verzendt de taak-API al deze informatie naar het opgegeven eind punt van de terugbellen.

## <a name="next-steps"></a>Volgende stappen

In deze hand leiding hebt u geleerd hoe u met de REST API inhouds toezichthouders kunt maken en opvragen. Vervolgens integreert u taken in een end-to-end-beheer scenario, zoals de zelf studie voor [E-commerce toezicht](./ecommerce-retail-catalog-moderation.md) .