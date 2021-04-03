---
title: Aangepaste AML-vaardigheid in vaardig heden
titleSuffix: Azure Cognitive Search
description: Breid mogelijkheden van Azure Cognitive Search vaardig heden uit met Azure Machine Learning-modellen.
manager: nitinme
author: mattmsft
ms.author: magottei
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/12/2020
ms.openlocfilehash: 6cefe543ea8ba992b028448070bf041a77bfec64
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97630272"
---
# <a name="aml-skill-in-an-azure-cognitive-search-enrichment-pipeline"></a>AML-vaardigheid in een Azure Cognitive Search-verrijkings pijplijn

> [!IMPORTANT] 
> Deze vaardigheid is momenteel in openbare preview. Deze previewfunctie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie. Er is momenteel geen .NET SDK-ondersteuning.

Met de **AML** -vaardigheid kunt u AI-verrijking uitbreiden met een aangepast [Azure machine learning](../machine-learning/overview-what-is-azure-ml.md) (AML)-model. Zodra een AML-model is [getraind en geïmplementeerd](../machine-learning/concept-azure-machine-learning-architecture.md#workspace), wordt het door een **AML** -vaardigheid geïntegreerd in AI-verrijking.

Net als bij ingebouwde vaardig heden bevat een **AML** -vaardigheid invoer en uitvoer. De invoer wordt naar uw geïmplementeerde AML-service verzonden als een JSON-object, die een JSON-nettolading levert als antwoord, samen met een status code voor geslaagd. Er wordt verwacht dat het antwoord de uitvoer bevat die is opgegeven door uw **AML** -vaardigheid. Elk ander antwoord wordt als een fout beschouwd en er worden geen verrijkingen uitgevoerd.

> [!NOTE]
> De Indexeer functie voert twee keer opnieuw uit voor bepaalde standaard HTTP-status codes die worden geretourneerd door de AML-service. Deze HTTP-status codes zijn:
> * `503 Service Unavailable`
> * `429 Too Many Requests`

## <a name="prerequisites"></a>Vereisten

* Een [AML-werk ruimte](../machine-learning/concept-workspace.md)
* Een [Azure Kubernetes service AML Compute target](../machine-learning/concept-compute-target.md) in deze werk ruimte met een [geïmplementeerd model](../machine-learning/how-to-deploy-azure-kubernetes-service.md)
  * [SSL moet zijn ingeschakeld voor het Compute-doel](../machine-learning/how-to-secure-web-service.md#deploy-on-azure-kubernetes-service). Azure Cognitive Search verleent alleen toegang tot **https** -eind punten
  * Zelfondertekende certificaten kunnen niet worden gebruikt.

## <a name="odatatype"></a>@odata.type  
Micro soft. skills. custom. AmlSkill

## <a name="skill-parameters"></a>Vaardigheids parameters

Parameters zijn hoofdlettergevoelig. Welke para meters u kunt gebruiken, is afhankelijk van [de verificatie die uw AML-service nodig heeft, indien van toepassing](#WhatSkillParametersToUse)

| Parameternaam | Beschrijving |
|--------------------|-------------|
| `uri` | (Vereist voor [geen verificatie of sleutel verificatie](#WhatSkillParametersToUse)) De [Score-URI van de AML-service](../machine-learning/how-to-consume-web-service.md) waarnaar de _JSON_ -nettolading wordt verzonden. Alleen het **https** URI-schema is toegestaan. |
| `key` | (Vereist voor [sleutel verificatie](#WhatSkillParametersToUse)) De [sleutel voor de AML-service](../machine-learning/how-to-consume-web-service.md#authentication-with-keys). |
| `resourceId` | (Vereist voor [token verificatie](#WhatSkillParametersToUse)). De Azure Resource Manager Resource-ID van de AML-service. Deze moet de indeling abonnementen/{GUID}/resourceGroups/{resource-group-name}/micro soft. MachineLearningServices/Workspaces/{Workspace-name}/services/{service_name} hebben. |
| `region` | (Optioneel voor [token verificatie](#WhatSkillParametersToUse)). De [regio](https://azure.microsoft.com/global-infrastructure/regions/) waarin de AML-service is geïmplementeerd. |
| `timeout` | Beschrijving Hiermee geeft u de time-out op voor de HTTP-client die de API-aanroep maakt. Deze moet worden ingedeeld als een XSD ' dayTimeDuration-waarde (een beperkte subset van een [ISO 8601 duration](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) -waarde). Bijvoorbeeld `PT60S` gedurende 60 seconden. Als deze niet is ingesteld, wordt de standaard waarde van 30 seconden gekozen. De time-out kan worden ingesteld op een maximum van 230 seconden en een minimum van 1 seconde. |
| `degreeOfParallelism` | Beschrijving Indien opgegeven, wordt hiermee het aantal aanroepen aangegeven dat de Indexeer functie parallel moet maken aan het eind punt dat u hebt opgegeven. U kunt deze waarde verlagen als uw eind punt met een te hoge belasting van de aanvraag is mislukt of als u het wilt verhogen als uw eind punt meer aanvragen kan accepteren en u de prestaties van de Indexeer functie wilt verhogen.  Als deze niet is ingesteld, wordt de standaard waarde 5 gebruikt. De degreeOfParallelism kan worden ingesteld op een maximum van 10 en een minimum van 1.

<a name="WhatSkillParametersToUse"></a>

## <a name="what-skill-parameters-to-use"></a>Welke vaardigheids parameters u moet gebruiken

Welke para meters voor de AML-vaardig heden zijn vereist, is afhankelijk van de verificatie die uw AML-service gebruikt, indien van toepassing. AML-services bieden drie verificatie opties:

* [Verificatie op basis van een sleutel](../machine-learning/how-to-authenticate-web-service.md#key-based-authentication). Er wordt een statische sleutel gegeven om Score aanvragen van AML-vaardig heden te verifiëren
  * De _URI_ -en _sleutel_ parameters gebruiken
* [Verificatie op basis van tokens](../machine-learning/how-to-authenticate-web-service.md#token-based-authentication). De AML-service wordt [geïmplementeerd met verificatie op basis van tokens](../machine-learning/how-to-authenticate-web-service.md#token-based-authentication). De [beheerde identiteit](../active-directory/managed-identities-azure-resources/overview.md) van de Azure Cognitive Search-service is toegewezen aan de [rol van lezer](../machine-learning/how-to-assign-roles.md) in de werk ruimte van de AML-service. De AML-vaardigheid gebruikt vervolgens de beheerde identiteit van de Azure Cognitive Search-service om te verifiëren bij de AML-service, zonder dat er statische sleutels vereist zijn.
  * Gebruik de para meter _resourceId_ .
  * Als de Azure Cognitive Search-service zich in een andere regio bevindt dan de AML-werk ruimte, gebruikt u de para meter _Region_ voor het instellen van de regio waarin de AML-service is geïmplementeerd
* Geen verificatie. Er is geen verificatie vereist voor het gebruik van de AML-service
  * De _URI_ -para meter gebruiken

## <a name="skill-inputs"></a>Vaardigheids invoer

Er zijn geen "vooraf gedefinieerde" invoer voor deze vaardigheid. U kunt een of meer velden kiezen die al beschikbaar zijn op het moment dat de uitvoering van deze vaardigheid als invoer wordt uitgevoerd en de _JSON_ -nettolading die naar de AML-service wordt verzonden, verschillende velden heeft.

## <a name="skill-outputs"></a>Vaardigheids uitvoer

Er zijn geen vooraf gedefinieerde uitvoer voor deze vaardigheid. Afhankelijk van de reactie die uw AML-service retourneert, voegt u uitvoer velden toe zodat deze kunnen worden opgehaald uit het _JSON_ -antwoord.

## <a name="sample-definition"></a>Voorbeeld definitie

```json
  {
    "@odata.type": "#Microsoft.Skills.Custom.AmlSkill",
    "description": "A sample model that detects the language of sentence",
    "uri": "https://contoso.count-things.com/score",
    "context": "/document",
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "detected_language_code"
      }
    ]
  }
```

## <a name="sample-input-json-structure"></a>Voorbeeld structuur voor de invoer van JSON

Deze _JSON_ -structuur vertegenwoordigt de payload die naar uw AML-service wordt verzonden. De velden op het hoogste niveau van de structuur komen overeen met de ' namen ' die zijn opgegeven in de `inputs` sectie van de vaardigheids definitie. De waarde van deze velden gaat uit `source` van deze velden (die mogelijk afkomstig zijn uit een veld in het document of mogelijk van een andere vaardigheid)

```json
{
  "text": "Este es un contrato en Inglés"
}
```

## <a name="sample-output-json-structure"></a>Voor beeld van JSON-structuur van uitvoer

De uitvoer komt overeen met het antwoord dat door uw AML-service is geretourneerd. De AML-service mag alleen een _JSON_ -nettolading retour neren (gecontroleerd door te kijken naar de `Content-Type` reactie header) en moet een object zijn waarin de velden verrijkingen zijn die overeenkomen met de ' namen ' in de `output` en waarvan de waarde wordt beschouwd als de verrijking.

```json
{
    "detected_language_code": "es"
}
```

## <a name="inline-shaping-sample-definition"></a>Voorbeeld definitie van inline-Shaping

```json
  {
    "@odata.type": "#Microsoft.Skills.Custom.AmlSkill",
    "description": "A sample model that detects the language of sentence",
    "uri": "https://contoso.count-things.com/score",
    "context": "/document",
    "inputs": [
      {
        "name": "shapedText",
        "sourceContext": "/document",
        "inputs": [
            {
              "name": "content",
              "source": "/document/content"
            }
        ]
      }
    ],
    "outputs": [
      {
        "name": "detected_language_code"
      }
    ]
  }
```

## <a name="inline-shaping-input-json-structure"></a>JSON-structuur van invoer van inline-Shaping

```json
{
  "shapedText": { "content": "Este es un contrato en Inglés" }
}
```

## <a name="inline-shaping-sample-output-json-structure"></a>JSON-structuur voor beeld van inline-vorm uitvoer

```json
{
    "detected_language_code": "es"
}
```

## <a name="error-cases"></a>Fout cases
Naast uw AML is niet beschikbaar of het verzenden van niet-geslaagde status codes, worden de volgende als foutieve cases beschouwd:

* Als de AML-service een status code voor geslaagde pogingen retourneert, maar het antwoord geeft aan dat het niet is `application/json` , wordt het antwoord beschouwd als ongeldig en worden er geen verrijkingen uitgevoerd.
* Als de AML-service een ongeldige JSON retourneert

Als de AML-service niet beschikbaar is of een HTTP-fout retourneert, wordt een beschrijvende fout met alle beschik bare Details over de HTTP-fout toegevoegd aan de indexerings geschiedenis.

## <a name="see-also"></a>Zie ook

+ [Een vaardig heden definiëren](cognitive-search-defining-skillset.md)
+ [Problemen met de AML-service oplossen](../machine-learning/how-to-troubleshoot-deployment.md)