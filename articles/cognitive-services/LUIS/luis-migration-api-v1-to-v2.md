---
title: Migratie van v1 tot v2 API
titleSuffix: Azure Cognitive Services
description: De Api's voor het versie 1-eind punt en de ontwerp-Language Understanding zijn afgeschaft. Gebruik deze hand leiding om te begrijpen hoe u kunt migreren naar versie 2-eind punt-en ontwerp-Api's.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 04/02/2019
ms.openlocfilehash: 867ae2cc7567077786bb0840cd11c47b786be423
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "95018749"
---
# <a name="api-v1-to-v2-migration-guide-for-luis-apps"></a>API v1 naar v2-migratie handleiding voor LUIS-apps
De api's voor het versie 1- [eind punt](https://aka.ms/v1-endpoint-api-docs) en het [ontwerp](https://aka.ms/v1-authoring-api-docs) zijn afgeschaft. Gebruik deze hand leiding om te begrijpen hoe u kunt migreren naar versie 2- [eind punt](https://go.microsoft.com/fwlink/?linkid=2092356) -en [ontwerp](https://go.microsoft.com/fwlink/?linkid=2092087) -api's.

## <a name="new-azure-regions"></a>Nieuwe Azure-regio's
LUIS heeft nieuwe [regio's](./luis-reference-regions.md) voor de Luis-api's. LUIS biedt een andere portal voor regio groepen. De toepassing moet worden gemaakt in dezelfde regio als die waarvoor u de query verwacht. In toepassingen worden regio's niet automatisch gemigreerd. U exporteert de app uit de ene regio en importeert deze vervolgens in een nieuwe regio.

## <a name="authoring-route-changes"></a>Wijzigingen in de route worden geschreven
De API-route van authoring is gewijzigd van met behulp van de **PROG** -route naar met behulp van de **API** -route.


| versie | route |
|--|--|
|1|/Luis/v1.0/**PROG**/apps|
|2|/Luis/-**API**/v2.0/apps|


## <a name="endpoint-route-changes"></a>Wijzigingen van eindpunt route
De eindpunt-API heeft nieuwe query reeks parameters en een ander antwoord. Als de vlag uitgebreid is ingesteld op True, worden alle intentiesen, ongeacht de score, geretourneerd in een matrix met de naam intenties, naast het topScoringIntent.

| versie | Route ophalen |
|--|--|
|1|/Luis/v1/Application? ID = {appId} &q = {q}|
|2|/luis/v2.0/apps/{appId}? q = {q} [&time zone offset] [&uitgebreid] [&spelling] [&fase ring] [&Bing-spelling controle-abonnement-sleutel] [&logboek]|


geslaagde reacties van v1-eind punt:
```json
{
  "odata.metadata":"https://dialogice.cloudapp.net/odata/$metadata#domain","value":[
    {
      "id":"bccb84ee-4bd6-4460-a340-0595b12db294","q":"turn on the camera","response":"[{\"intent\":\"OpenCamera\",\"score\":0.976928055},{\"intent\":\"None\",\"score\":0.0230718572}]"
    }
  ]
}
```

geslaagde reactie v2-eind punt:
```json
{
  "query": "forward to frank 30 dollars through HSBC",
  "topScoringIntent": {
    "intent": "give",
    "score": 0.3964121
  },
  "entities": [
    {
      "entity": "30",
      "type": "builtin.number",
      "startIndex": 17,
      "endIndex": 18,
      "resolution": {
        "value": "30"
      }
    },
    {
      "entity": "frank",
      "type": "frank",
      "startIndex": 11,
      "endIndex": 15,
      "score": 0.935219169
    },
    {
      "entity": "30 dollars",
      "type": "builtin.currency",
      "startIndex": 17,
      "endIndex": 26,
      "resolution": {
        "unit": "Dollar",
        "value": "30"
      }
    },
    {
      "entity": "hsbc",
      "type": "Bank",
      "startIndex": 36,
      "endIndex": 39,
      "resolution": {
        "values": [
          "BankeName"
        ]
      }
    }
  ]
}
```

## <a name="key-management-no-longer-in-api"></a>Sleutel beheer is niet meer beschikbaar in API
De Api's voor het abonnements-endpoint zijn afgeschaft en retour neren 410 weg.

| versie | route |
|--|--|
|1|/luis/v1.0/prog/subscriptions|
|1|/luis/v1.0/prog/subscriptions/{subscriptionKey}|

Azure- [eindpunt sleutels](luis-how-to-azure-subscription.md) worden gegenereerd in de Azure Portal. U wijst de sleutel toe aan een LUIS-app op de pagina **[publiceren](luis-how-to-azure-subscription.md)** . U hoeft niet meer te weten over de daad werkelijke sleutel waarde. LUIS maakt gebruik van de naam van het abonnement om de toewijzing te maken.

## <a name="new-versioning-route"></a>Nieuwe versie route
Het v2-model is nu opgenomen in een [versie](luis-how-to-manage-versions.md). Een versie naam is 10 tekens in de route. De standaard versie is ' 0,1 '.

| versie | route |
|--|--|
|1|/Luis/v1.0/**PROG**/apps/{AppID}/entities|
|2|/Luis/**API**/v2.0/apps/{AppID}/-**versies**/{versionId}/entities|

## <a name="metadata-renamed"></a>De naam van de meta gegevens is gewijzigd
Verschillende Api's die LUIS-meta gegevens retour neren, hebben nieuwe namen.

| v1-route naam | v2-route naam |
|--|--|
|PersonalAssistantApps |assistenten|
|applicationcultures|culturen|
|applicationdomains|domeinen|
|applicationusagescenarios|usagescenarios|


## <a name="sample-renamed-to-suggest"></a>' Voor beeld ' is gewijzigd in ' suggereren '
LUIS raadt uitingen aan van de bestaande [eindpunt uitingen](luis-how-to-review-endpoint-utterances.md) die het model kan verbeteren. In de vorige versie werd dit voor **beeld** genoemd. In de nieuwe versie wordt de naam gewijzigd van voor beeld om te **suggereren**. Dit wordt **[geëvalueerd eind punt uitingen](luis-how-to-review-endpoint-utterances.md)** op de Luis-website genoemd.

| versie | route |
|--|--|
|1|/Luis/v1.0/**PROG**/apps/{AppID}/entities/{entityId}/-voor **beeld**|
|1|/Luis/v1.0/**PROG**/apps/{AppID}/intents/{intentId}/-voor **beeld**|
|2|/Luis/**API**/v2.0/apps/{AppID}/-**versies**/{versionId}/entities/{entityId}/**suggereren**|
|2|/Luis/**API**/v2.0/apps/{AppID}/-**versies**/{versionId}/intents/{intentId}/**suggereren**|


## <a name="create-app-from-prebuilt-domains"></a>Een app maken op basis van vooraf gebouwde domeinen
Vooraf [ontwikkelde domeinen](./howto-add-prebuilt-models.md) bieden een voorgedefinieerd domein model. Met vooraf gemaakte domeinen kunt u snel uw LUIS-toepassing ontwikkelen voor algemene domeinen. Met deze API kunt u een nieuwe app maken op basis van een vooraf gebouwd domein. Het antwoord is de nieuwe appID.

|v2-route|term|
|--|--|
|/luis/api/v2.0/apps/customprebuiltdomains  |Get, post|
|/luis/api/v2.0/apps/customprebuiltdomains/{culture}  |ophalen|

## <a name="importing-1x-app-into-2x"></a>De 1. x-app wordt geïmporteerd in 2. x
De geëxporteerde JSON van de 1. x-app heeft enkele gebieden die u moet wijzigen voordat u deze importeert in [LUIS][LUIS] 2,0.

### <a name="prebuilt-entities"></a>Vooraf gemaakte entiteiten
De [vooraf gemaakte entiteiten](./howto-add-prebuilt-models.md) zijn gewijzigd. Zorg ervoor dat u de v2 vooraf gedefinieerde entiteiten gebruikt. Dit omvat het gebruik van [datetimeV2](luis-reference-prebuilt-datetimev2.md)in plaats van DateTime.

### <a name="actions"></a>Acties
De eigenschap actions is niet meer geldig. Deze moet leeg zijn

### <a name="labeled-utterances"></a>Gelabelde uitingen
V1 heeft uitingen toegestaan om spaties toe te voegen aan het begin of einde van het woord of de woord groep. De spaties zijn verwijderd.

## <a name="common-reasons-for-http-response-status-codes"></a>Veelvoorkomende redenen voor HTTP-antwoord status codes
Zie [Luis-API-respons codes](luis-reference-response-codes.md).

## <a name="next-steps"></a>Volgende stappen

Gebruik de v2 API-documentatie om bestaande REST-aanroepen bij te werken naar LUIS- [eind punt](https://go.microsoft.com/fwlink/?linkid=2092356) -en [ontwerp](https://go.microsoft.com/fwlink/?linkid=2092087) -api's.

[LUIS]: ./luis-reference-regions.md