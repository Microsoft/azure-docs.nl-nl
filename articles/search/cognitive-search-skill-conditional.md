---
title: Voorwaardelijke cognitieve vaardigheid
titleSuffix: Azure Cognitive Search
description: Met de voorwaardelijke vaardigheid in azure Cognitive Search filteren, standaard instellingen maken en waarden samen voegen in een definitie van een vakkennisset.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: f47ca56fa1b40422edeb0d4e11c24be6f60e49e5
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101666366"
---
# <a name="conditional-cognitive-skill"></a>Voorwaardelijke cognitieve vaardigheid

De **voorwaardelijke** vaardigheid maakt Azure Cognitive Search scenario's mogelijk waarvoor een Booleaanse bewerking is vereist om te bepalen welke gegevens moeten worden toegewezen aan een uitvoer. Deze scenario's zijn onder andere het filteren, het toewijzen van een standaard waarde en het samen voegen van gegevens op basis van een voor waarde.

De volgende pseudocode laat zien wat de voorwaardelijke vaardigheid heeft:

```
if (condition) 
    { output = whenTrue } 
else 
    { output = whenFalse } 
```

> [!NOTE]
> Deze vaardigheid is niet gebonden aan een Azure Cognitive Services-API en er worden geen kosten in rekening gebracht voor het gebruik ervan. U moet echter wel [een Cognitive Services resource koppelen](cognitive-search-attach-cognitive-services.md) om de resource optie ' gratis ' te onderdrukken, waardoor u een klein aantal verrijkingen per dag beperkt.

## <a name="odatatype"></a>@odata.type  
Micro soft. skills. util. ConditionalSkill


## <a name="evaluated-fields"></a>Geëvalueerde velden

Deze vaardigheid is speciaal omdat de invoer ervan geëvalueerde velden zijn.

De volgende items zijn geldige waarden van een expressie:

-   Aantekening paden (paden in expressies moeten worden gescheiden door $ (en))
 <br/>
    Voorbeelden:
    ```
        "= $(/document)"
        "= $(/document/content)"
    ```

-  Letterlijke tekens (teken reeksen, getallen, waar, onwaar, null) <br/>
    Voorbeelden:
    ```
       "= 'this is a string'"   // string (note the single quotation marks)
       "= 34"                   // number
       "= true"                 // Boolean
       "= null"                 // null value
    ```

-  Expressies die gebruikmaken van vergelijkings operatoren (= =,! =, >=, >, <=, <) <br/>
    Voorbeelden:
    ```
        "= $(/document/language) == 'en'"
        "= $(/document/sentiment) >= 0.5"
    ```

-   Expressies die gebruikmaken van Booleaanse Opera tors (&&, | |,!, ^) <br/>
    Voorbeelden:
    ```
        "= $(/document/language) == 'en' && $(/document/sentiment) > 0.5"
        "= !true"
    ```

-   Expressies die gebruikmaken van numerieke Opera tors (+,-, \* ,/,%) <br/>
    Voorbeelden: 
    ```
        "= $(/document/sentiment) + 0.5"         // addition
        "= $(/document/totalValue) * 1.10"       // multiplication
        "= $(/document/lengthInMeters) / 0.3049" // division
    ```

Omdat de voorwaardelijke vaardigheid evaluatie ondersteunt, kunt u deze gebruiken in scenario's voor kleine trans formatie. Zie bijvoorbeeld [vaardigheids definitie 4](#transformation-example).

## <a name="skill-inputs"></a>Vaardigheids invoer
De invoer is hoofdlettergevoelig.

| Invoer   | Beschrijving |
|-------------|-------------|
| regeling   | Deze invoer is een [geëvalueerd veld](#evaluated-fields) dat staat voor de voor waarde die moet worden geëvalueerd. Deze voor waarde moet resulteren in een Booleaanse waarde (*waar* of *Onwaar*).   <br/>  Voorbeelden: <br/> "= True" <br/> "= $ (/document/Language) = = ' fr '" <br/> "= $ (/document/Pages/ \* /Language) = = $ (/document/expectedLanguage)" <br/> |
| whenTrue    | Deze invoer is een [geëvalueerd veld](#evaluated-fields) dat de waarde vertegenwoordigt die moet worden geretourneerd als de voor waarde wordt geëvalueerd als *waar*. Constanten teken reeksen moeten worden geretourneerd tussen enkele aanhalings tekens (' en '). <br/>Voorbeeld waarden: <br/> "=" contract ""<br/>"= $ (/document/contractType)" <br/> "= $ (/document/entities/ \* )" <br/> |
| whenFalse   | Deze invoer is een [geëvalueerd veld](#evaluated-fields) dat de waarde vertegenwoordigt die moet worden geretourneerd als de voor waarde wordt geëvalueerd als *Onwaar*. <br/>Voorbeeld waarden: <br/> "=" contract ""<br/>"= $ (/document/contractType)" <br/> "= $ (/document/entities/ \* )" <br/>

## <a name="skill-outputs"></a>Vaardigheids uitvoer
Er is één uitvoer die gewoon "uitvoer" wordt genoemd. Retourneert de waarde *whenFalse* als de voor waarde ONWAAR is of *whenTrue* als de voor waarde waar is.

## <a name="examples"></a>Voorbeelden

### <a name="sample-skill-definition-1-filter-documents-to-return-only-french-documents"></a>Voor beeld van vaardigheids definitie 1: documenten filteren om alleen Franse documenten te retour neren

De volgende uitvoer retourneert een matrix met zinnen ("/document/frenchSentences") als de taal van het document Frans is. Als de taal niet Frans is, wordt de waarde ingesteld op *Null*.

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document",
    "inputs": [
        { "name": "condition", "source": "= $(/document/language) == 'fr'" },
        { "name": "whenTrue", "source": "/document/sentences" },
        { "name": "whenFalse", "source": "= null" }
    ],
    "outputs": [ { "name": "output", "targetName": "frenchSentences" } ]
}
```
Als "/document/frenchSentences" wordt gebruikt als de *context* van een andere vaardigheid, wordt die vaardigheid alleen uitgevoerd als "/document/frenchSentences" niet is ingesteld op *Null*.


### <a name="sample-skill-definition-2-set-a-default-value-for-a-value-that-doesnt-exist"></a>Voor beeld van vaardigheids definitie 2: Stel een standaard waarde in voor een waarde die niet bestaat

Met de volgende uitvoer wordt een aantekening ("/document/languageWithDefault") gemaakt die is ingesteld op de taal van het document of op "ES" als de taal niet is ingesteld.

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document",
    "inputs": [
        { "name": "condition", "source": "= $(/document/language) == null" },
        { "name": "whenTrue", "source": "= 'es'" },
        { "name": "whenFalse", "source": "= $(/document/language)" }
    ],
    "outputs": [ { "name": "output", "targetName": "languageWithDefault" } ]
}
```

### <a name="sample-skill-definition-3-merge-values-from-two-fields-into-one"></a>Voor beeld van vaardigheids definitie 3: waarden uit twee velden samen voegen tot één

In dit voor beeld hebben sommige zinnen een eigenschap *frenchSentiment* . Wanneer de eigenschap *frenchSentiment* null is, willen we de *englishSentiment* -waarde gebruiken. We wijzen de uitvoer toe aan een lid met de naam *sentiment* ("/document/sentences/*/sentiment").

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document/sentences/*",
    "inputs": [
        { "name": "condition", "source": "= $(/document/sentences/*/frenchSentiment) == null" },
        { "name": "whenTrue", "source": "/document/sentences/*/englishSentiment" },
        { "name": "whenFalse", "source": "/document/sentences/*/frenchSentiment" }
    ],
    "outputs": [ { "name": "output", "targetName": "sentiment" } ]
}
```

## <a name="transformation-example"></a>Voor beeld van trans formatie
### <a name="sample-skill-definition-4-data-transformation-on-a-single-field"></a>Voor beeld van vaardigheids definitie 4: gegevens transformatie voor één veld

In dit voor beeld ontvangen we een *sentiment* tussen 0 en 1. We willen deze transformeren tussen-1 en 1. We kunnen de voorwaardelijke vaardigheid gebruiken om deze secundaire trans formatie uit te voeren.

In dit voor beeld gebruiken we niet het voorwaardelijke aspect van de vaardigheid, omdat de voor waarde altijd *waar* is.

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document/sentences/*",
    "inputs": [
        { "name": "condition", "source": "= true" },
        { "name": "whenTrue", "source": "= $(/document/sentences/*/sentiment) * 2 - 1" },
        { "name": "whenFalse", "source": "= 0" }
    ],
    "outputs": [ { "name": "output", "targetName": "normalizedSentiment" } ]
}
```

## <a name="special-considerations"></a>Speciale overwegingen
Sommige para meters worden geëvalueerd, dus u moet er met name op letten dat u het gedocumenteerde patroon volgt. Expressies moeten met een gelijkteken beginnen. Een pad moet worden gescheiden door $ (en). Zorg ervoor dat teken reeksen tussen enkele aanhalings tekens worden geplaatst. Dit helpt de evaluator onderscheid te maken tussen teken reeksen en werkelijke paden en Opera tors. Zorg er ook voor dat u witruimte rond Opera tors plaatst (bijvoorbeeld, een ' * ' in een pad betekent iets anders dan vermenigvuldigen).


## <a name="next-steps"></a>Volgende stappen

+ [Ingebouwde vaardigheden](cognitive-search-predefined-skills.md)
+ [Een vaardig heden definiëren](cognitive-search-defining-skillset.md)
