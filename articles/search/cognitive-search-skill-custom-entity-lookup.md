---
title: Kwalificatie Zoek vaardigheid aangepaste entiteit opzoeken
titleSuffix: Azure Cognitive Search
description: Extraheer verschillende aangepaste entiteiten van tekst in een Azure Cognitive Search cognitieve Zoek pijplijn. Deze vaardigheid is momenteel in openbare preview.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/17/2020
ms.openlocfilehash: 704763e8e6e7c5336d0ed3e1c28791fb96c77aba
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97844925"
---
#     <a name="custom-entity-lookup-cognitive-skill-preview"></a>Aangepaste entiteit opzoeken cognitieve vaardigheid (preview-versie)

> [!IMPORTANT] 
> Deze vaardigheid is momenteel in openbare preview. Deze previewfunctie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie. Er is momenteel geen portal-of .NET SDK-ondersteuning.

De **Zoek vaardigheid voor aangepaste entiteiten** zoekt naar tekst uit een aangepaste, door de gebruiker gedefinieerde lijst met woorden en zinsdelen. Aan de hand van deze lijst worden alle documenten met overeenkomende entiteiten gelabeld. De vaardigheid ondersteunt ook het zoeken naar fuzzy overeenkomsten, waarmee overeenkomsten worden gevonden die vergelijkbaar zijn, maar niet helemaal exact.  

Deze vaardigheid is niet gebonden aan een Cognitive Services-API en kan gratis worden gebruikt tijdens de preview-periode. U moet nog steeds [een Cognitive Services resource koppelen](./cognitive-search-attach-cognitive-services.md)om de dagelijkse verrijkings limiet te onderdrukken. De dagelijkse limiet is van toepassing op gratis toegang tot Cognitive Services wanneer deze via Azure Cognitive Search wordt geopend.

## <a name="odatatype"></a>@odata.type  
Micro soft. skills. Text. CustomEntityLookupSkill 

## <a name="data-limits"></a>Gegevenslimieten
+ De maximale invoer record grootte die wordt ondersteund, is 256 MB. Als u uw gegevens wilt opsplitsen voordat u deze naar de aangepaste entiteit lookup-vaardigheid verzendt, kunt u overwegen de [Kwalificatie tekst splitsen](cognitive-search-skill-textsplit.md)te gebruiken.
+ De Maxi maal ondersteunde entiteit definitie tabel is 10 MB als deze wordt gebruikt met behulp van de para meter *entitiesDefinitionUri* . 
+ Als de entiteiten inline zijn gedefinieerd, met behulp van de para meter *inlineEntitiesDefinition* , is de Maxi maal ondersteunde grootte 10 KB.

## <a name="skill-parameters"></a>Vaardigheids parameters

Parameters zijn hoofdlettergevoelig.

| Parameternaam     | Beschrijving |
|--------------------|-------------|
| `entitiesDefinitionUri`    | Pad naar een JSON-of CSV-bestand met alle doel tekst die moet worden vergeleken. Deze entiteits definitie wordt gelezen aan het begin van het uitvoeren van een Indexeer functie. eventuele updates van dit bestand worden uitgevoerd totdat de volgende keer wordt uitgevoerd. Deze configuratie moet toegankelijk zijn via HTTPS. Zie de aangepaste indeling voor de [entiteits definitie](#custom-entity-definition-format) hieronder voor het verwachte CSV-of JSON-schema.|
|`inlineEntitiesDefinition` | Definities van de inline JSON-entiteit. Deze para meter vervangt de para meter entitiesDefinitionUri indien aanwezig. Er kan niet meer dan 10 KB aan configuratie worden meegeleverd. Zie de definitie van de [aangepaste entiteit](#custom-entity-definition-format) hieronder voor het verwachte JSON-schema. |
|`defaultLanguageCode` |    Beschrijving De taal code van de invoer tekst die wordt gebruikt voor het Tokenize en afbakenen van invoer tekst. De volgende talen worden ondersteund: `da, de, en, es, fi, fr, it, ko, pt` . De standaard waarde is English ( `en` ). Als u een language code-CountryCode-indeling doorgeeft, wordt alleen het language code deel van de indeling gebruikt.  |
|`globalDefaultCaseSensitive` | Beschrijving Standaard waarde voor hoofdletter gevoelig voor de vaardigheid. Als de `defaultCaseSensitive` waarde van een entiteit niet is opgegeven, wordt deze waarde de `defaultCaseSensitive` waarde voor die entiteit. |
|`globalDefaultAccentSensitive` | Beschrijving Standaard accent gevoelige waarde voor de vaardigheid. Als de `defaultAccentSensitive` waarde van een entiteit niet is opgegeven, wordt deze waarde de `defaultAccentSensitive` waarde voor die entiteit. |
|`globalDefaultFuzzyEditDistance` | Beschrijving Standaard waarde voor onbewerkte afstand voor de vaardigheid. Als de `defaultFuzzyEditDistance` waarde van een entiteit niet is opgegeven, wordt deze waarde de `defaultFuzzyEditDistance` waarde voor die entiteit. |

## <a name="skill-inputs"></a>Vaardigheids invoer

| Invoer naam      | Beschrijving                   |
|---------------|-------------------------------|
| `text`          | De tekst die moet worden geanalyseerd.          |
| `languageCode`    | Optioneel. De standaardinstelling is `"en"`.  |


## <a name="skill-outputs"></a>Vaardigheids uitvoer


| Uitvoer naam      | Beschrijving                   |
|---------------|-------------------------------|
| `entities` | Een matrix met objecten die informatie bevatten over de gevonden overeenkomsten en gerelateerde meta gegevens. Elk van de geïdentificeerde entiteiten bevat mogelijk de volgende velden:  <ul> <li> *naam*: de entiteit op het hoogste niveau dat is geïdentificeerd. De entiteit vertegenwoordigt het ' genormaliseerde ' formulier. </li> <li> *id*: een unieke id voor de entiteit zoals gedefinieerd door de gebruiker in de aangepaste entiteits definitie-indeling.</li> <li> *Beschrijving*: beschrijving van de entiteit zoals gedefinieerd door de gebruiker in de definitie-indeling aangepaste entiteit. </li> <li> *type:* Entiteits type zoals gedefinieerd door de gebruiker in de aangepaste entiteits definitie-indeling.</li> <li> *subtype:* Subtype van entiteit zoals gedefinieerd door de gebruiker in de definitie-indeling aangepaste entiteit.</li>  <li> *matchs*: verzameling waarmee de overeenkomsten voor die entiteit worden beschreven op de bron tekst. Elke overeenkomst heeft de volgende leden: </li> <ul> <li> *tekst*: de onbewerkte tekst komt overeen met het bron document. </li> <li> *Offset*: de locatie waar de overeenkomst is gevonden in de tekst. </li> <li> *Length*: de lengte van de overeenkomende tekst. </li> <li> *matchDistance*: het aantal tekens dat anders is dan dit komt overeen met de oorspronkelijke entiteits naam of-alias.  </li> </ul> </ul>
  |

## <a name="custom-entity-definition-format"></a>Indeling aangepaste entiteits definitie

Er zijn drie verschillende manieren om de lijst met aangepaste entiteiten te voorzien van de aangepaste vaardigheid voor het opzoeken van entiteiten. U kunt de lijst opgeven in een. CSV-bestand, a. JSON-bestand of als een inline-definitie als onderdeel van de vaardigheids definitie.  

Als het definitie bestand een is. CSV of. JSON-bestand, het pad van het bestand moet worden gegeven als onderdeel van de para meter *entitiesDefinitionUri* . In dit geval wordt het bestand eenmaal gedownload aan het begin van elke Indexeer functie die wordt uitgevoerd. Het bestand moet toegankelijk zijn zolang de Indexeer functie is bedoeld om te worden uitgevoerd. Daarnaast moet het bestand UTF-8 zijn gecodeerd.

Als de definitie inline wordt vermeld, moet deze als inline worden verschaft als de inhoud van de para meter *inlineEntitiesDefinition* . 

### <a name="csv-format"></a>CSV-indeling

U kunt de definitie van de aangepaste entiteiten die moeten worden gezocht in een CSV-bestand (Comma-Separated waarde) door het pad naar het bestand op te geven en het in te stellen in de para meter *entitiesDefinitionUri*  . Het pad moet zich op een HTTPS-locatie bevinden. Het definitie bestand kan Maxi maal 10 MB groot zijn.

De CSV-indeling is eenvoudig. Elke regel vertegenwoordigt een unieke entiteit, zoals hieronder wordt weer gegeven:

```
Bill Gates, BillG, William H. Gates
Microsoft, MSFT
Satya Nadella 
```

In dit geval zijn er drie entiteiten die kunnen worden geretourneerd als entiteiten (Bill Gates, Satya NADELLA, micro soft), maar ze worden geïdentificeerd als een van de voor waarden op de regel (aliassen) overeenkomt met de tekst. Als bijvoorbeeld de teken reeks ' William H. Gates ' in een document wordt gevonden, wordt er een overeenkomst voor de entiteit ' Bill Gates ' geretourneerd.

### <a name="json-format"></a>JSON-indeling

U kunt ook de definitie van de aangepaste entiteiten opgeven om in een JSON-bestand te zoeken. De JSON-indeling biedt u een beetje meer flexibiliteit, omdat u hiermee overeenkomende regels per term kunt definiëren. U kunt bijvoorbeeld de op benadering van de afstand (Damerau-levenshtein) opgeven voor elke term of de overeenkomende waarde moet hoofdletter gevoelig of niet zijn. 

 Net als bij CSV-bestanden moet u het pad naar het JSON-bestand opgeven en instellen in de para meter *entitiesDefinitionUri* . Het pad moet zich op een HTTPS-locatie bevinden. Het definitie bestand kan Maxi maal 10 MB groot zijn.

De belangrijkste definitie van de aangepaste JSON-entiteits lijst kan een lijst met entiteiten zijn die moeten worden vergeleken:

```json
[ 
    { 
        "name" : "Bill Gates"
    }, 
    { 
        "name" : "Microsoft"
    }, 
    { 
        "name" : "Satya Nadella"
    }
]
```

Een complexere voor beeld van een JSON-definitie kan optioneel de id, de beschrijving, het type en het subtype van elke entiteit, evenals andere *aliassen*, opgeven. Als een alias term overeenkomt, wordt de entiteit geretourneerd.

```json
[ 
    { 
        "name" : "Bill Gates",
        "description" : "Microsoft founder." ,
        "aliases" : [ 
            { "text" : "William H. Gates", "caseSensitive" : false },
            { "text" : "BillG", "caseSensitive" : true }
        ]
    }, 
    { 
        "name" : "Xbox One", 
        "type": "Harware",
        "subtype" : "Gaming Device",
        "id" : "4e36bf9d-5550-4396-8647-8e43d7564a76",
        "description" : "The Xbox One product"
    }, 
    { 
        "name" : "LinkedIn" , 
        "description" : "The LinkedIn company", 
        "id" : "differentIdentifyingScheme123", 
        "fuzzyEditDistance" : 0 
    }, 
    { 
        "name" : "Microsoft" , 
        "description" : "Microsoft Corporation", 
        "id" : "differentIdentifyingScheme987", 
        "defaultCaseSensitive" : false, 
        "defaultFuzzyEditDistance" : 1, 
        "aliases" : [ 
            { "text" : "MSFT", "caseSensitive" : true }
        ]
    } 
] 
```

De onderstaande tabellen bevatten meer informatie over de verschillende configuratie parameters die u kunt instellen bij het definiëren van de entiteiten die moeten overeenkomen:

|  Veldnaam  |        Beschrijving  |
|--------------|----------------------|
| `name` | De entiteits descriptor op het hoogste niveau. Overeenkomsten in de vaardigheids uitvoer worden gegroepeerd op basis van deze naam en moeten de "genormaliseerde" vorm vertegenwoordigen van de tekst die wordt gevonden.  |
| `description`  | Beschrijving Dit veld kan worden gebruikt als passthrough voor aangepaste meta gegevens over de overeenkomende tekst (en). De waarde van dit veld wordt weer gegeven bij elke overeenkomst van de entiteit in de vaardigheids uitvoer. |
| `type` | Beschrijving Dit veld kan worden gebruikt als passthrough voor aangepaste meta gegevens over de overeenkomende tekst (en). De waarde van dit veld wordt weer gegeven bij elke overeenkomst van de entiteit in de vaardigheids uitvoer. |
| `subtype` | Beschrijving Dit veld kan worden gebruikt als passthrough voor aangepaste meta gegevens over de overeenkomende tekst (en). De waarde van dit veld wordt weer gegeven bij elke overeenkomst van de entiteit in de vaardigheids uitvoer. |
| `id` | Beschrijving Dit veld kan worden gebruikt als passthrough voor aangepaste meta gegevens over de overeenkomende tekst (en). De waarde van dit veld wordt weer gegeven bij elke overeenkomst van de entiteit in de vaardigheids uitvoer. |
| `caseSensitive` | Beschrijving De standaard waarde is False. Booleaanse waarde waarmee wordt aangegeven of vergelijkingen met de naam van de entiteit gevoelig zijn voor teken behuizing. Voor beelden van niet-hoofdletter gevoelige overeenkomsten van micro soft zijn: micro soft, micro soft, micro soft |
| `accentSensitive` | Beschrijving De standaard waarde is False. Een Booleaanse waarde die aangeeft of accenten en niet-accenten letters zoals ' é ' en ' e ' identiek moeten zijn. |
| `fuzzyEditDistance` | Beschrijving De standaard waarde is 0. De maximum waarde van 5. Hiermee wordt het toegestane aantal uiteenlopende tekens aangegeven dat nog steeds overeenkomt met de naam van de entiteit. De kleinste mogelijke overeenkomst voor elk gegeven resultaat wordt geretourneerd.  Als de bewerkings afstand is ingesteld op 3, zou Windows 10 nog steeds overeenkomen met ' Windows ', ' Windows10 ' en ' Windows 7 '. <br/> Wanneer hoofdletter gevoeligheid is ingesteld op ONWAAR, worden de verschillen in de overeenkomst niet meegeteld bij de tolerantie, maar anderszins. |
| `defaultCaseSensitive` | Beschrijving Hiermee wijzigt u de standaard waarde voor hoofdletter gevoeligheid voor deze entiteit. Het kan worden gebruikt om de standaard waarde van alle aliassen caseSensitive waarden te wijzigen. |
| `defaultAccentSensitive` | Beschrijving Hiermee wijzigt u de standaard waarde voor accent gevoeligheid voor deze entiteit. Het kan worden gebruikt om de standaard waarde van alle aliassen accentSensitive waarden te wijzigen.|
| `defaultFuzzyEditDistance` | Beschrijving Wijzigt de standaard waarde voor het bewerken van fuzzy voor deze entiteit. Het kan worden gebruikt om de standaard waarde van alle aliassen fuzzyEditDistance waarden te wijzigen. |
| `aliases` | Beschrijving Een matrix met complexe objecten die kunnen worden gebruikt om alternatieve spellingen of synoniemen op te geven voor de naam van de hoofd entiteit. |

| Alias eigenschappen | Beschrijving |
|------------------|-------------|
| `text`  | De alternatieve spelling of weer gave van de naam van een doel entiteit.  |
| `caseSensitive` | Beschrijving Fungeert hetzelfde als de hoofd entiteit ' caseSensitive-para meter hierboven, maar is alleen van toepassing op deze ene alias. |
| `accentSensitive` | Beschrijving Fungeert hetzelfde als de hoofd entiteit ' accentSensitive-para meter hierboven, maar is alleen van toepassing op deze ene alias. |
| `fuzzyEditDistance` | Beschrijving Fungeert hetzelfde als de hoofd entiteit ' fuzzyEditDistance-para meter hierboven, maar is alleen van toepassing op deze ene alias. |


### <a name="inline-format"></a>Inline-indeling

In sommige gevallen is het handiger om de lijst met aangepaste entiteiten op te geven, zodat deze rechtstreeks overeenkomt met de vaardigheids definitie. In dat geval kunt u een vergelijk bare JSON-indeling gebruiken die hierboven wordt beschreven, maar deze wordt in de vaardigheids definitie aangegeven.
Alleen configuraties met een grootte van minder dan 10 KB (geserialiseerde grootte) kunnen inline worden gedefinieerd. 

##    <a name="sample-definition"></a>Voorbeeld definitie

Een voor beeld van een vaardigheids definitie die gebruikmaakt van een inline-indeling, wordt hieronder weer gegeven:

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.CustomEntityLookupSkill",
    "context": "/document",
    "inlineEntitiesDefinition": 
    [
      { 
        "name" : "Bill Gates",
        "description" : "Microsoft founder." ,
        "aliases" : [ 
            { "text" : "William H. Gates", "caseSensitive" : false },
            { "text" : "BillG", "caseSensitive" : true }
        ]
      }, 
      { 
        "name" : "Xbox One", 
        "type": "Hardware",
        "subtype" : "Gaming Device",
        "id" : "4e36bf9d-5550-4396-8647-8e43d7564a76",
        "description" : "The Xbox One product"
      }
    ],    
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "entities",
        "targetName": "matchedEntities"
      }
    ]
  }
```
Als u besluit om een verwijzing naar het definitie bestand van de entiteiten op te geven, wordt hieronder een voor beeld vaardigheids definitie met de `entitiesDefinitionUri` indeling weer gegeven:

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.CustomEntityLookupSkill",
    "context": "/document",
    "entitiesDefinitionUri": "https://myblobhost.net/keyWordsConfig.csv",    
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "entities",
        "targetName": "matchedEntities"
      }
    ]
  }

```

##    <a name="sample-input"></a>Voorbeeldinvoer

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
             "text": "The company, Microsoft, was founded by Bill Gates. Microsoft's gaming console is called Xbox",
             "languageCode": "en"
           }
      }
    ]
}
```

##    <a name="sample-output"></a>Voorbeelduitvoer

```json
  { 
    "values" : 
    [ 
      { 
        "recordId": "1", 
        "data" : { 
          "entities": [
            { 
              "name" : "Microsoft", 
              "description" : "This document refers to Microsoft the company", 
              "id" : "differentIdentifyingScheme987", 
              "matches" : [ 
                { 
                  "text" : "microsoft", 
                  "offset" : 13, 
                  "length" : 9, 
                  "matchDistance" : 0 
                }, 
                { 
                  "text" : "Microsoft",
                  "offset" : 49, 
                  "length" : 9, 
                  "matchDistance" : 0
                }
              ] 
            },
            { 
              "name" : "Bill Gates",
              "description" : "William Henry Gates III, founder of Microsoft.", 
              "matches" : [
                { 
                  "text" : "Bill Gates",
                  "offset" : 37, 
                  "length" : 10,
                  "matchDistance" : 0 
                }
              ]
            }
          ] 
        } 
      } 
    ] 
  } 
```

## <a name="errors-and-warnings"></a>Fouten en waarschuwingen

### <a name="warning-reached-maximum-capacity-for-matches-skipping-all-further-duplicate-matches"></a>Waarschuwing: de maximale capaciteit voor treffers is bereikt. alle andere dubbele treffers worden overgeslagen.

Deze waarschuwing wordt verzonden als het aantal gedetecteerde overeenkomsten groter is dan het toegestane maximum. In dit geval worden er ook dubbele overeenkomsten beëindigd. Als dit niet acceptabel is, kunt u een [ondersteunings ticket](https://ms.portal.azure.com/#create/Microsoft.Support) indienen zodat we u kunnen helpen met uw individuele use-case.

## <a name="see-also"></a>Zie ook

+ [Ingebouwde vaardigheden](cognitive-search-predefined-skills.md)
+ [Een vaardig heden definiëren](cognitive-search-defining-skillset.md)
+ [Vaardigheid van entiteits herkenning (om te zoeken naar bekende entiteiten)](cognitive-search-skill-entity-recognition.md)
