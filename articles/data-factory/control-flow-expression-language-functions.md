---
title: Expressie en functies in Azure Data Factory
description: Dit artikel bevat informatie over expressies en functies die u kunt gebruiken om data factory entiteiten te maken.
services: data-factory
documentationcenter: ''
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 11/25/2019
ms.openlocfilehash: 3c966f0efc51a3b2fa8908e060b4031ae1ad1e50
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96500016"
---
# <a name="expressions-and-functions-in-azure-data-factory"></a>Expressies en functies in Azure Data Factory

> [!div class="op_single_selector" title1="Selecteer de versie van de Data Factory-service die u gebruikt:"]
> * [Versie 1:](v1/data-factory-functions-variables.md)
> * [Huidige versie](control-flow-expression-language-functions.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Dit artikel bevat informatie over expressies en functies die door Azure Data Factory worden ondersteund. 

## <a name="expressions"></a>Expressies

JSON-waarden in de definitie kunnen letterlijk zijn of expressies die tijdens runtime worden geëvalueerd. Bijvoorbeeld:  
  
```json
"name": "value"
```

 of  
  
```json
"name": "@pipeline().parameters.password"
```

Expressies kunnen ergens in een JSON-teken reeks waarde worden weer gegeven en hebben altijd een andere JSON-waarde. Als een JSON-waarde een expressie is, wordt de hoofd tekst van de expressie geëxtraheerd door de at-teken ( \@ ) te verwijderen. Als er een letterlijke teken reeks nodig is die begint met \@ , moet deze worden ontsnapd met behulp van \@ \@ . In de volgende voor beelden ziet u hoe expressies worden geëvalueerd.  
  
|JSON-waarde|Resultaat|  
|----------------|------------|  
|instellen|De tekens ' para meters ' worden geretourneerd.|  
|"para meters [1]"|De tekens ' para meters [1] ' worden geretourneerd.|  
|"\@\@"|Er wordt een teken reeks van 1 \@ geretourneerd die ' ' bevat.|  
|" \@"|Een teken reeks van 2 tekens die ' ' bevat, \@ wordt geretourneerd.|  
  
 Expressies kunnen ook worden weer gegeven in teken reeksen, met behulp van een functie met de naam *teken reeks interpolatie* waarin expressies worden ingepakt `@{ ... }` . Bijvoorbeeld: `"name" : "First Name: @{pipeline().parameters.firstName} Last Name: @{pipeline().parameters.lastName}"`  
  
 Als u de teken reeks interpolatie gebruikt, is het resultaat altijd een teken reeks. Stel dat ik is gedefinieerd `myNumber` als `42` en  `myString` als  `foo` :  
  
|JSON-waarde|Resultaat|  
|----------------|------------|  
|" \@ pijp lijn (). para meters. MyString"| Retourneert `foo` als een teken reeks.|  
|" \@ {pipeline (). para meters. MyString}"| Retourneert `foo` als een teken reeks.|  
|" \@ pijp lijn (). para meters. myNumber"| Retourneert `42` als een *getal*.|  
|" \@ {pipeline (). para meters. myNumber}"| Retourneert `42` als een *teken reeks*.|  
|"Antwoord is: @ {pipeline (). para meters. myNumber}"| Retourneert de teken reeks `Answer is: 42` .|  
|' \@ concat (' antwoord is: ', String (pijp lijn (). para meters. myNumber)) '| Retourneert de teken reeks `Answer is: 42`|  
|"Antwoord is: \@ \@ {pipeline (). para meters. myNumber}"| Retourneert de teken reeks `Answer is: @{pipeline().parameters.myNumber}` .|  
  
## <a name="examples"></a>Voorbeelden

### <a name="complex-expression-example"></a>Voor beeld van complexe expressie
In het onderstaande voor beeld ziet u een complex voor beeld dat verwijst naar een diepe subveld van de uitvoer van de activiteit. Als u wilt verwijzen naar een pijplijn parameter die naar een subveld wordt geëvalueerd, gebruikt u de syntaxis [] in plaats van een punt (.)-operator (zoals in het geval van subveld1 en subveld2)

@activity('*activiteitsnummer*'). output. *subveld1*. *subveld2*[pipeline (). para meters.*subfield3*]. *subfield4*

### <a name="a-dataset-with-a-parameter"></a>Een gegevensset met een para meter
In het volgende voor beeld gebruikt de BlobDataset een para meter met de naam **Path**. De waarde wordt gebruikt om een waarde voor de eigenschap **FolderPath** in te stellen met behulp van de expressie: `dataset().path` . 

```json
{
    "name": "BlobDataset",
    "properties": {
        "type": "AzureBlob",
        "typeProperties": {
            "folderPath": "@dataset().path"
        },
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "parameters": {
            "path": {
                "type": "String"
            }
        }
    }
}
```

### <a name="a-pipeline-with-a-parameter"></a>Een pijp lijn met een para meter
In het volgende voor beeld worden de para meters **inputPath** en **outputPath** gebruikt voor de pijp lijn. Het **pad** voor de geparametriseerde BLOB-gegevensset wordt ingesteld met behulp van de waarden van deze para meters. De syntaxis die hier wordt gebruikt, is: `pipeline().parameters.parametername` . 

```json
{
    "name": "Adfv2QuickStartPipeline",
    "properties": {
        "activities": [
            {
                "name": "CopyFromBlobToBlob",
                "type": "Copy",
                "inputs": [
                    {
                        "referenceName": "BlobDataset",
                        "parameters": {
                            "path": "@pipeline().parameters.inputPath"
                        },
                        "type": "DatasetReference"
                    }
                ],
                "outputs": [
                    {
                        "referenceName": "BlobDataset",
                        "parameters": {
                            "path": "@pipeline().parameters.outputPath"
                        },
                        "type": "DatasetReference"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                }
            }
        ],
        "parameters": {
            "inputPath": {
                "type": "String"
            },
            "outputPath": {
                "type": "String"
            }
        }
    }
}
```
### <a name="tutorial"></a>Zelfstudie
In deze [zelf studie](https://azure.microsoft.com/mediahandler/files/resourcefiles/azure-data-factory-passing-parameters/Azure%20data%20Factory-Whitepaper-PassingParameters.pdf) leert u hoe u para meters kunt door geven tussen een pijp lijn en activiteit en tussen de activiteiten.

  
## <a name="functions"></a>Functions

U kunt functies aanroepen binnen expressies. De volgende secties bevatten informatie over de functies die kunnen worden gebruikt in een expressie.  

## <a name="string-functions"></a>Tekenreeksfuncties  

Als u met teken reeksen wilt werken, kunt u deze teken reeks functies en ook bepaalde [verzamelings functies](#collection-functions)gebruiken.
Teken reeks functies werken alleen voor teken reeksen.

| Teken reeks functie | Taak |
| --------------- | ---- |
| [concat](control-flow-expression-language-functions.md#concat) | Combi neer twee of meer teken reeksen en retour neer de gecombineerde teken reeks. |
| [endsWith](control-flow-expression-language-functions.md#endswith) | Controleren of een teken reeks eindigt met de opgegeven subtekenreeks. |
| [guid](control-flow-expression-language-functions.md#guid) | Genereer een Globally Unique Identifier (GUID) als een teken reeks. |
| [indexOf](control-flow-expression-language-functions.md#indexof) | Retourneert de begin positie van een subtekenreeks. |
| [lastIndexOf](control-flow-expression-language-functions.md#lastindexof) | Retourneert de begin positie voor het laatste exemplaar van een subtekenreeks. |
| [vervangen](control-flow-expression-language-functions.md#replace) | Vervang een subtekenreeks door de opgegeven teken reeks en retour neer de bijgewerkte teken reeks. |
| [delen](control-flow-expression-language-functions.md#split) | Retourneert een matrix die subtekenreeksen bevat, gescheiden door komma's, van een grotere teken reeks op basis van een opgegeven scheidings teken in de oorspronkelijke teken reeks. |
| [startsWith](control-flow-expression-language-functions.md#startswith) | Controleren of een teken reeks begint met een specifieke subtekenreeks. |
| [subtekenreeks](control-flow-expression-language-functions.md#substring) | Retourneert tekens uit een teken reeks, te beginnen met de opgegeven positie. |
| [toLower](control-flow-expression-language-functions.md#toLower) | Retourneert een teken reeks met de indeling kleine letters. |
| [toUpper](control-flow-expression-language-functions.md#toUpper) | Retourneert een teken reeks met een hoofd letter. |
| [interne](control-flow-expression-language-functions.md#trim) | Verwijder voor loop-en volg spaties uit een teken reeks en retour neer de bijgewerkte teken reeks. |

## <a name="collection-functions"></a>Verzamelingsfuncties

Als u wilt werken met verzamelingen, meestal matrices, teken reeksen en soms, woorden lijsten, kunt u deze verzamelings functies gebruiken.

| Functie verzameling | Taak |
| ------------------- | ---- |
| [daarin](control-flow-expression-language-functions.md#contains) | Controleer of een verzameling een specifiek item heeft. |
| [gelaten](control-flow-expression-language-functions.md#empty) | Controleer of een verzameling leeg is. |
| [instantie](control-flow-expression-language-functions.md#first) | Het eerste item van een verzameling retour neren. |
| [Snij punt](control-flow-expression-language-functions.md#intersection) | Een verzameling retour neren die *alleen* de gemeen schappelijke items in de opgegeven verzamelingen heeft. |
| [Jointypen](control-flow-expression-language-functions.md#join) | Retourneert een teken reeks met *alle* items uit een matrix, gescheiden door het opgegeven teken. |
| [duren](control-flow-expression-language-functions.md#last) | Het laatste item van een verzameling retour neren. |
| [length](control-flow-expression-language-functions.md#length) | Retourneert het aantal items in een teken reeks of matrix. |
| [verdergaan](control-flow-expression-language-functions.md#skip) | Verwijder items van de voor kant van een verzameling en retour neer *alle andere* items. |
| [take](control-flow-expression-language-functions.md#take) | Items van de voor grond van een verzameling retour neren. |
| [Réunion](control-flow-expression-language-functions.md#union) | Een verzameling retour neren die *alle* items uit de opgegeven verzamelingen bevat. | 

## <a name="logical-functions"></a>Logische functies  

Deze functies zijn handig in voor waarden en kunnen worden gebruikt om elk type logica te evalueren.  
  
| Logische vergelijkings functie | Taak |
| --------------------------- | ---- |
| [and](control-flow-expression-language-functions.md#and) | Controleer of alle expressies waar zijn. |
| [gelijk is aan](control-flow-expression-language-functions.md#equals) | Controleer of beide waarden gelijk zijn. |
| [groter](control-flow-expression-language-functions.md#greater) | Controleer of de eerste waarde groter is dan de tweede waarde. |
| [greaterOrEquals](control-flow-expression-language-functions.md#greaterOrEquals) | Controleer of de eerste waarde groter is dan of gelijk is aan de tweede waarde. |
| [If](control-flow-expression-language-functions.md#if) | Controleer of een expressie True of False is. Retour neer een opgegeven waarde op basis van het resultaat. |
| [jonge](control-flow-expression-language-functions.md#less) | Controleer of de eerste waarde lager is dan de tweede waarde. |
| [lessOrEquals](control-flow-expression-language-functions.md#lessOrEquals) | Controleer of de eerste waarde kleiner is dan of gelijk is aan de tweede waarde. |
| [ten](control-flow-expression-language-functions.md#not) | Controleer of een expressie onwaar is. |
| [or](control-flow-expression-language-functions.md#or) | Controleer of ten minste één expressie waar is. |
  
## <a name="conversion-functions"></a>Conversiefuncties  

 Deze functies worden gebruikt om te converteren tussen de systeem eigen typen in de taal:  
-   tekenreeks
-   geheel getal
-   float
-   booleaans
-   matrices
-   woorden lijsten

| Conversie functie | Taak |
| ------------------- | ---- |
| [matrix](control-flow-expression-language-functions.md#array) | Een matrix retour neren van een enkele opgegeven invoer. Zie [createArray](control-flow-expression-language-functions.md#createArray)voor meerdere invoer. |
| [base64](control-flow-expression-language-functions.md#base64) | Retourneert de met base64 gecodeerde versie voor een teken reeks. |
| [base64ToBinary](control-flow-expression-language-functions.md#base64ToBinary) | Retourneert de binaire versie voor een base64-gecodeerde teken reeks. |
| [base64ToString](control-flow-expression-language-functions.md#base64ToString) | Retourneert de versie van de teken reeks voor een base64-gecodeerde teken reeks. |
| [waarde](control-flow-expression-language-functions.md#binary) | Retourneert de binaire versie voor een invoer waarde. |
| [booleaans](control-flow-expression-language-functions.md#bool) | Retourneert de Booleaanse versie van een invoer waarde. |
| [Voeg](control-flow-expression-language-functions.md#coalesce) | Retourneert de eerste waarde die niet null is van een of meer para meters. |
| [createArray](control-flow-expression-language-functions.md#createArray) | Een matrix van meerdere invoer waarden retour neren. |
| [dataUri](control-flow-expression-language-functions.md#dataUri) | De gegevens-URI voor een invoer waarde Retour neren. |
| [dataUriToBinary](control-flow-expression-language-functions.md#dataUriToBinary) | Retourneert de binaire versie voor een gegevens-URI. |
| [dataUriToString](control-flow-expression-language-functions.md#dataUriToString) | Retourneert de versie van de teken reeks voor een gegevens-URI. |
| [decodeBase64](control-flow-expression-language-functions.md#decodeBase64) | Retourneert de versie van de teken reeks voor een base64-gecodeerde teken reeks. |
| [decodeDataUri](control-flow-expression-language-functions.md#decodeDataUri) | Retourneert de binaire versie voor een gegevens-URI. |
| [decodeUriComponent](control-flow-expression-language-functions.md#decodeUriComponent) | Retourneert een teken reeks waarmee escape tekens worden vervangen door gedecodeerde versies. |
| [encodeUriComponent](control-flow-expression-language-functions.md#encodeUriComponent) | Retourneert een teken reeks waarmee URL-onveilige tekens worden vervangen door Escape-tekens. |
| [float](control-flow-expression-language-functions.md#float) | Retourneert een drijvende-komma waarde voor een invoer waarde. |
| [int](control-flow-expression-language-functions.md#int) | Retourneert de versie met gehele getallen voor een teken reeks. |
| [JSON](control-flow-expression-language-functions.md#json) | De waarde of het object van het type JavaScript Object Notation (JSON) retour neren voor een teken reeks of XML. |
| [tekenreeks](control-flow-expression-language-functions.md#string) | Retourneert de teken reeks versie voor een invoer waarde. |
| [uriComponent](control-flow-expression-language-functions.md#uriComponent) | De versie van de URI-code ring retour neren voor een invoer waarde door onveilige URL-tekens te vervangen door Escape tekens. |
| [uriComponentToBinary](control-flow-expression-language-functions.md#uriComponentToBinary) | Retourneert de binaire versie voor een teken reeks met URI-code ring. |
| [uriComponentToString](control-flow-expression-language-functions.md#uriComponentToString) | Retourneert de versie van de teken reeks voor een teken reeks met URI-code ring. |
| [indeling](control-flow-expression-language-functions.md#xml) | De XML-versie voor een teken reeks retour neren. |
| [XPath](control-flow-expression-language-functions.md#xpath) | Controleer XML voor knoop punten of waarden die overeenkomen met een XPath-expressie (XML-pad) en retour neer de overeenkomende knoop punten of waarden. |

## <a name="math-functions"></a>Wiskundige functies  
 Deze functies kunnen worden gebruikt voor beide typen getallen: **gehele** getallen en **zwevende** waarden.  

| Wiskundige functie | Taak |
| ------------- | ---- |
| [add](control-flow-expression-language-functions.md#add) | Het resultaat van het toevoegen van twee getallen retour neren. |
| [div](control-flow-expression-language-functions.md#div) | Retourneert het resultaat van het delen van twee getallen. |
| [aantal](control-flow-expression-language-functions.md#max) | Retourneert de hoogste waarde van een reeks getallen of een matrix. |
| [Haal](control-flow-expression-language-functions.md#min) | Retourneert de laagste waarde van een reeks getallen of een matrix. |
| [mod](control-flow-expression-language-functions.md#mod) | Retourneert de rest van het delen van twee getallen. |
| [mul](control-flow-expression-language-functions.md#mul) | Retourneert het product van het vermenigvuldigen van twee getallen. |
| [ASELECT](control-flow-expression-language-functions.md#rand) | Retourneert een wille keurig geheel getal uit een opgegeven bereik. |
| [bereik](control-flow-expression-language-functions.md#range) | Retourneert een matrix met gehele getallen die begint met een opgegeven geheel getal. |
| [sub](control-flow-expression-language-functions.md#sub) | Retourneert het resultaat van het aftrekken van het tweede getal uit het eerste getal. |
  
## <a name="date-functions"></a>Datumfuncties  

| De functie datum of tijd | Taak |
| --------------------- | ---- |
| [addDays](control-flow-expression-language-functions.md#addDays) | Voeg een aantal dagen toe aan een tijds tempel. |
| [addHours](control-flow-expression-language-functions.md#addHours) | Voeg een aantal uren toe aan een tijds tempel. |
| [addMinutes](control-flow-expression-language-functions.md#addMinutes) | Voeg een aantal minuten toe aan een tijds tempel. |
| [addSeconds](control-flow-expression-language-functions.md#addSeconds) | Voeg een aantal seconden toe aan een tijds tempel. |
| [addToTime](control-flow-expression-language-functions.md#addToTime) | Voeg een aantal tijds eenheden toe aan een tijds tempel. Zie ook [getFutureTime](control-flow-expression-language-functions.md#getFutureTime). |
| [convertFromUtc](control-flow-expression-language-functions.md#convertFromUtc) | Converteer een tijds tempel van Universal Time Coordinated (UTC) naar de doel tijdzone. |
| [convertTimeZone](control-flow-expression-language-functions.md#convertTimeZone) | Converteer een tijds tempel van de bron tijdzone naar de doel tijdzone. |
| [convertToUtc](control-flow-expression-language-functions.md#convertToUtc) | Een tijds tempel van de bron tijdzone converteren naar Universal Time Coordinated (UTC). |
| [dayOfMonth](control-flow-expression-language-functions.md#dayOfMonth) | Retourneert de dag van het onderdeel month van een tijds tempel. |
| [dayOfWeek](control-flow-expression-language-functions.md#dayOfWeek) | Retourneert de dag van de week component van een tijds tempel. |
| [dayOfYear](control-flow-expression-language-functions.md#dayOfYear) | Retourneert de dag van het jaar component van een tijds tempel. |
| [formatDateTime](control-flow-expression-language-functions.md#formatDateTime) | Retourneert de tijds tempel als een teken reeks in een optionele notatie. |
| [getFutureTime](control-flow-expression-language-functions.md#getFutureTime) | De huidige tijds tempel en de opgegeven tijds eenheden retour neren. Zie ook [addToTime](control-flow-expression-language-functions.md#addToTime). |
| [getPastTime](control-flow-expression-language-functions.md#getPastTime) | Retourneert de huidige tijds tempel min de opgegeven tijds eenheden. Zie ook [subtractFromTime](control-flow-expression-language-functions.md#subtractFromTime). |
| [startOfDay](control-flow-expression-language-functions.md#startOfDay) | Retourneert het begin van de dag voor een tijds tempel. |
| [startOfHour](control-flow-expression-language-functions.md#startOfHour) | Het begin van het uur retour neren voor een tijds tempel. |
| [startOfMonth](control-flow-expression-language-functions.md#startOfMonth) | Retourneert het begin van de maand voor een tijds tempel. |
| [subtractFromTime](control-flow-expression-language-functions.md#subtractFromTime) | Een aantal tijds eenheden aftrekken van een tijds tempel. Zie ook [getPastTime](control-flow-expression-language-functions.md#getPastTime). |
| [Ticks](control-flow-expression-language-functions.md#ticks) | De `ticks` eigenschaps waarde voor een opgegeven tijds tempel retour neren. |
| [utcNow](control-flow-expression-language-functions.md#utcNow) | De huidige tijds tempel retour neren als een teken reeks. |

## <a name="function-reference"></a>Functie verwijzing

In deze sectie worden alle beschik bare functies in alfabetische volg orde weer gegeven.

<a name="add"></a>

### <a name="add"></a>add

Het resultaat van het toevoegen van twee getallen retour neren.

```
add(<summand_1>, <summand_2>)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <<*summand_2* *summand_1*>> | Ja | Geheel getal, vlotter of gemengd | De toe te voegen getallen |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | -----| ----------- |
| <*resultaat-Sum*> | Geheel getal of zwevend | Het resultaat van het toevoegen van de opgegeven getallen |
||||

*Voorbeeld*

In dit voor beeld worden de opgegeven getallen toegevoegd:

```
add(1, 1.5)
```

En retourneert dit resultaat: `2.5`

<a name="addDays"></a>

### <a name="adddays"></a>addDays

Voeg een aantal dagen toe aan een tijds tempel.

```
addDays('<timestamp>', <days>, '<format>'?)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Neem*> | Ja | Tekenreeks | De teken reeks die de tijds tempel bevat |
| <*resterende*> | Ja | Geheel getal | Het positieve of negatieve aantal dagen dat moet worden toegevoegd |
| <*Formatteer*> | Nee | Tekenreeks | Een [enkele indelings aanduiding](/dotnet/standard/base-types/standard-date-and-time-format-strings) of een [aangepast opmaak patroon](/dotnet/standard/base-types/custom-date-and-time-format-strings). De standaard notatie voor de tijds tempel is ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (jjjj-mm-ddTuu: mm: SS: fffffffK), die voldoet aan [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) en tijd zone gegevens behoudt. |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*bijgewerkt-tijds tempel*> | Tekenreeks | De tijds tempel plus het opgegeven aantal dagen  |
||||

*Voorbeeld 1*

In dit voor beeld worden 10 dagen toegevoegd aan de opgegeven tijds tempel:

```
addDays('2018-03-15T13:00:00Z', 10)
```

En retourneert dit resultaat: `"2018-03-25T00:00:0000000Z"`

*Voorbeeld 2*

In dit voor beeld worden vijf dagen afgetrokken van de opgegeven tijds tempel:

```
addDays('2018-03-15T00:00:00Z', -5)
```

En retourneert dit resultaat: `"2018-03-10T00:00:0000000Z"`

<a name="addHours"></a>

### <a name="addhours"></a>addHours

Voeg een aantal uren toe aan een tijds tempel.

```
addHours('<timestamp>', <hours>, '<format>'?)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Neem*> | Ja | Tekenreeks | De teken reeks die de tijds tempel bevat |
| <*loopt*> | Ja | Geheel getal | Het positieve of negatieve aantal uur dat moet worden toegevoegd |
| <*Formatteer*> | Nee | Tekenreeks | Een [enkele indelings aanduiding](/dotnet/standard/base-types/standard-date-and-time-format-strings) of een [aangepast opmaak patroon](/dotnet/standard/base-types/custom-date-and-time-format-strings). De standaard notatie voor de tijds tempel is ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (jjjj-mm-ddTuu: mm: SS: fffffffK), die voldoet aan [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) en tijd zone gegevens behoudt. |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*bijgewerkt-tijds tempel*> | Tekenreeks | De tijds tempel plus het opgegeven aantal uren  |
||||

*Voorbeeld 1*

In dit voor beeld worden 10 uur toegevoegd aan de opgegeven tijds tempel:

```
addHours('2018-03-15T00:00:00Z', 10)
```

En retourneert dit resultaat: `"2018-03-15T10:00:0000000Z"`

*Voorbeeld 2*

In dit voor beeld worden vijf uur afgetrokken van de opgegeven tijds tempel:

```
addHours('2018-03-15T15:00:00Z', -5)
```

En retourneert dit resultaat: `"2018-03-15T10:00:0000000Z"`

<a name="addMinutes"></a>

### <a name="addminutes"></a>addMinutes

Voeg een aantal minuten toe aan een tijds tempel.

```
addMinutes('<timestamp>', <minutes>, '<format>'?)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Neem*> | Ja | Tekenreeks | De teken reeks die de tijds tempel bevat |
| <*wachten*> | Ja | Geheel getal | Het positieve of negatieve aantal minuten dat moet worden toegevoegd |
| <*Formatteer*> | Nee | Tekenreeks | Een [enkele indelings aanduiding](/dotnet/standard/base-types/standard-date-and-time-format-strings) of een [aangepast opmaak patroon](/dotnet/standard/base-types/custom-date-and-time-format-strings). De standaard notatie voor de tijds tempel is ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (jjjj-mm-ddTuu: mm: SS: fffffffK), die voldoet aan [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) en tijd zone gegevens behoudt. |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*bijgewerkt-tijds tempel*> | Tekenreeks | De tijds tempel plus het opgegeven aantal minuten |
||||

*Voorbeeld 1*

In dit voor beeld wordt 10 minuten aan de opgegeven tijds tempel toegevoegd:

```
addMinutes('2018-03-15T00:10:00Z', 10)
```

En retourneert dit resultaat: `"2018-03-15T00:20:00.0000000Z"`

*Voorbeeld 2*

In dit voor beeld worden vijf minuten afgetrokken van de opgegeven tijds tempel:

```
addMinutes('2018-03-15T00:20:00Z', -5)
```

En retourneert dit resultaat: `"2018-03-15T00:15:00.0000000Z"`

<a name="addSeconds"></a>

### <a name="addseconds"></a>addSeconds

Voeg een aantal seconden toe aan een tijds tempel.

```
addSeconds('<timestamp>', <seconds>, '<format>'?)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Neem*> | Ja | Tekenreeks | De teken reeks die de tijds tempel bevat |
| <*waarna*> | Ja | Geheel getal | Het positieve of negatieve aantal seconden dat moet worden toegevoegd |
| <*Formatteer*> | Nee | Tekenreeks | Een [enkele indelings aanduiding](/dotnet/standard/base-types/standard-date-and-time-format-strings) of een [aangepast opmaak patroon](/dotnet/standard/base-types/custom-date-and-time-format-strings). De standaard notatie voor de tijds tempel is ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (jjjj-mm-ddTuu: mm: SS: fffffffK), die voldoet aan [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) en tijd zone gegevens behoudt. |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*bijgewerkt-tijds tempel*> | Tekenreeks | De tijds tempel plus het opgegeven aantal seconden  |
||||

*Voorbeeld 1*

In dit voor beeld wordt 10 seconden toegevoegd aan de opgegeven tijds tempel:

```
addSeconds('2018-03-15T00:00:00Z', 10)
```

En retourneert dit resultaat: `"2018-03-15T00:00:10.0000000Z"`

*Voorbeeld 2*

In dit voor beeld worden vijf seconden afgetrokken van de opgegeven tijds tempel:

```
addSeconds('2018-03-15T00:00:30Z', -5)
```

En retourneert dit resultaat: `"2018-03-15T00:00:25.0000000Z"`

<a name="addToTime"></a>

### <a name="addtotime"></a>addToTime

Voeg een aantal tijds eenheden toe aan een tijds tempel.
Zie ook [getFutureTime ()](#getFutureTime).

```
addToTime('<timestamp>', <interval>, '<timeUnit>', '<format>'?)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Neem*> | Ja | Tekenreeks | De teken reeks die de tijds tempel bevat |
| <*bereik*> | Ja | Geheel getal | Het aantal opgegeven tijds eenheden dat moet worden toegevoegd |
| <*timeUnit*> | Ja | Tekenreeks | De tijds eenheid die moet worden gebruikt met het *interval*: ' seconde ', ' minuut ', ' uur ', ' dag ', ' week ', ' maand ', ' jaar ' |
| <*Formatteer*> | Nee | Tekenreeks | Een [enkele indelings aanduiding](/dotnet/standard/base-types/standard-date-and-time-format-strings) of een [aangepast opmaak patroon](/dotnet/standard/base-types/custom-date-and-time-format-strings). De standaard notatie voor de tijds tempel is ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (jjjj-mm-ddTuu: mm: SS: fffffffK), die voldoet aan [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) en tijd zone gegevens behoudt. |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*bijgewerkt-tijds tempel*> | Tekenreeks | De tijds tempel plus het opgegeven aantal tijds eenheden  |
||||

*Voorbeeld 1*

In dit voor beeld wordt één dag toegevoegd aan de opgegeven tijds tempel:

```
addToTime('2018-01-01T00:00:00Z', 1, 'Day')
```

En retourneert dit resultaat: `"2018-01-02T00:00:00.0000000Z"`

*Voorbeeld 2*

In dit voor beeld wordt één dag toegevoegd aan de opgegeven tijds tempel:

```
addToTime('2018-01-01T00:00:00Z', 1, 'Day', 'D')
```

En retourneert het resultaat met de optionele D-indeling: `"Tuesday, January 2, 2018"`

<a name="and"></a>

### <a name="and"></a>en

Controleer of beide expressies waar zijn.
Retourneert waar als beide expressies waar zijn, of retourneert onwaar als ten minste één expressie onwaar is.

```
and(<expression1>, <expression2>)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*expressie1*>, <*Expressie2*> | Ja | Booleaans | De te controleren expressies |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | -----| ----------- |
| waar of onwaar | Boolean | Retourneert waar als beide expressies waar zijn. Retourneert onwaar wanneer ten minste één expressie onwaar is. |
||||

*Voorbeeld 1*

In deze voor beelden wordt gecontroleerd of de opgegeven Booleaanse waarden beide waar zijn:

```
and(true, true)
and(false, true)
and(false, false)
```

En retourneert deze resultaten:

* Eerste voor beeld: beide expressies zijn waar, dus retourneert `true` .
* Tweede voor beeld: een expressie is onwaar, dus retourneert `false` .
* Derde voor beeld: beide expressies zijn onwaar, dus retourneert `false` .

*Voorbeeld 2*

In deze voor beelden wordt gecontroleerd of de opgegeven expressies beide waar zijn:

```
and(equals(1, 1), equals(2, 2))
and(equals(1, 1), equals(1, 2))
and(equals(1, 2), equals(1, 3))
```

En retourneert deze resultaten:

* Eerste voor beeld: beide expressies zijn waar, dus retourneert `true` .
* Tweede voor beeld: een expressie is onwaar, dus retourneert `false` .
* Derde voor beeld: beide expressies zijn onwaar, dus retourneert `false` .

<a name="array"></a>

### <a name="array"></a>matrix

Een matrix retour neren van een enkele opgegeven invoer.
Zie [createArray ()](#createArray)voor meerdere invoer.

```
array('<value>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Value*> | Ja | Tekenreeks | De teken reeks voor het maken van een matrix |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| [<*waarde*>] | Matrix | Een matrix die de enkele opgegeven invoer bevat |
||||

*Voorbeeld*

In dit voor beeld wordt een matrix gemaakt op basis van de teken reeks ' Hallo ':

```
array('hello')
```

En retourneert dit resultaat: `["hello"]`

<a name="base64"></a>

### <a name="base64"></a>base64

Retourneert de met base64 gecodeerde versie voor een teken reeks.

```
base64('<value>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Value*> | Ja | Tekenreeks | De invoer teken reeks |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*Base64-teken reeks*> | Tekenreeks | De met base64 gecodeerde versie voor de invoer teken reeks |
||||

*Voorbeeld*

In dit voor beeld wordt de teken reeks "Hallo" geconverteerd naar een base64-gecodeerde teken reeks:

```
base64('hello')
```

En retourneert dit resultaat: `"aGVsbG8="`

<a name="base64ToBinary"></a>

### <a name="base64tobinary"></a>base64ToBinary

Retourneert de binaire versie voor een base64-gecodeerde teken reeks.

```
base64ToBinary('<value>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Value*> | Ja | Tekenreeks | De base64-gecodeerde teken reeks die moet worden geconverteerd |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*binary-for-base64-String*> | Tekenreeks | De binaire versie voor de met base64 gecodeerde teken reeks |
||||

*Voorbeeld*

In dit voor beeld wordt de met base64 gecodeerde teken reeks "aGVsbG8 =" geconverteerd naar een binaire teken reeks:

```
base64ToBinary('aGVsbG8=')
```

En retourneert dit resultaat:

`"0110000101000111010101100111001101100010010001110011100000111101"`

<a name="base64ToString"></a>

### <a name="base64tostring"></a>base64ToString

Retourneert de teken reeks versie voor een base64-gecodeerde teken reeks, waardoor de base64-teken reeks effectief wordt gedecodeerd.
Gebruik deze functie in plaats van [decodeBase64 ()](#decodeBase64).
Hoewel beide functies op dezelfde manier werken, verdient de `base64ToString()` voor keur.

```
base64ToString('<value>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Value*> | Ja | Tekenreeks | De met base64 gecodeerde teken reeks die moet worden gedecodeerd |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*gedecodeerd-base64-teken reeks*> | Tekenreeks | De teken reeks versie voor een base64-gecodeerde teken reeks |
||||

*Voorbeeld*

In dit voor beeld wordt de met base64 gecodeerde teken reeks "aGVsbG8 =" geconverteerd naar alleen een teken reeks:

```
base64ToString('aGVsbG8=')
```

En retourneert dit resultaat: `"hello"`

<a name="binary"></a>

### <a name="binary"></a>binair

Retourneert de binaire versie voor een teken reeks.

```
binary('<value>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Value*> | Ja | Tekenreeks | De teken reeks die moet worden geconverteerd |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*binary-for-input-waarde*> | Tekenreeks | De binaire versie voor de opgegeven teken reeks |
||||

*Voorbeeld*

In dit voor beeld wordt de teken reeks "Hallo" geconverteerd naar een binaire teken reeks:

```
binary('hello')
```

En retourneert dit resultaat:

`"0110100001100101011011000110110001101111"`

<a name="bool"></a>

### <a name="bool"></a>booleaans

Retourneert de Booleaanse versie voor een waarde.

```
bool(<value>)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Value*> | Ja | Elk | De waarde die moet worden geconverteerd |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| waar of onwaar | Boolean | De Booleaanse versie voor de opgegeven waarde |
||||

*Voorbeeld*

In deze voor beelden worden de opgegeven waarden geconverteerd naar Boole-waarden:

```
bool(1)
bool(0)
```

En retourneert deze resultaten:

* Eerste voor beeld: `true`
* Tweede voor beeld: `false`

<a name="coalesce"></a>

### <a name="coalesce"></a>Voeg

Retourneert de eerste waarde die niet null is van een of meer para meters.
Lege teken reeksen, lege matrices en lege objecten zijn niet null.

```
coalesce(<object_1>, <object_2>, ...)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*object_1*>, <*object_2*>,... | Ja | Any, kan typen combi neren | Een of meer items die moeten worden gecontroleerd op null |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*eerste niet-null-item*> | Elk | Het eerste item of de waarde die niet null is. Als alle para meters null zijn, retourneert deze functie null. |
||||

*Voorbeeld*

In deze voor beelden wordt de eerste niet-null-waarde uit de opgegeven waarden geretourneerd, of NULL wanneer alle waarden null zijn:

```
coalesce(null, true, false)
coalesce(null, 'hello', 'world')
coalesce(null, null, null)
```

En retourneert deze resultaten:

* Eerste voor beeld: `true`
* Tweede voor beeld: `"hello"`
* Derde voor beeld: `null`

<a name="concat"></a>

### <a name="concat"></a>concat

Combi neer twee of meer teken reeksen en retour neer de gecombineerde teken reeks.

```
concat('<text1>', '<text2>', ...)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*text1*>, <*Tekst2*>,... | Ja | Tekenreeks | Ten minste twee teken reeksen om te combi neren |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*text1text2...*> | Tekenreeks | De teken reeks die is gemaakt op basis van de gecombineerde invoer teken reeksen |
||||

*Voorbeeld*

In dit voor beeld worden de teken reeksen "Hallo" en "wereld" gecombineerd:

```
concat('Hello', 'World')
```

En retourneert dit resultaat: `"HelloWorld"`

<a name="contains"></a>

### <a name="contains"></a>bevat

Controleer of een verzameling een specifiek item heeft.
Retourneert waar wanneer het item is gevonden, of retourneert onwaar als het niet is gevonden.
Deze functie is hoofdletter gevoelig.

```
contains('<collection>', '<value>')
contains([<collection>], '<value>')
```

Deze functie werkt met name voor deze typen verzamelingen:

* Een *teken reeks* om een *subtekenreeks* te vinden
* Een *matrix* om een *waarde* te zoeken
* Een *woorden lijst* om een *sleutel* te vinden

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*innings*> | Ja | Teken reeks, matrix of woorden lijst | De verzameling die moet worden gecontroleerd |
| <*Value*> | Ja | Respectievelijk een teken reeks, matrix of woorden lijst | Het item dat u wilt zoeken |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| waar of onwaar | Boolean | Retourneert waar wanneer het item is gevonden. Retourneert onwaar wanneer deze niet is gevonden. |
||||

*Voorbeeld 1*

In dit voor beeld wordt de teken reeks "Hallo wereld" voor de subtekenreeks "wereld" gecontroleerd en wordt waar geretourneerd:

```
contains('hello world', 'world')
```

*Voorbeeld 2*

In dit voor beeld wordt de teken reeks "Hallo wereld" voor de subtekenreeks "universum" gecontroleerd en wordt false geretourneerd:

```
contains('hello world', 'universe')
```

<a name="convertFromUtc"></a>

### <a name="convertfromutc"></a>convertFromUtc

Converteer een tijds tempel van Universal Time Coordinated (UTC) naar de doel tijdzone.

```
convertFromUtc('<timestamp>', '<destinationTimeZone>', '<format>'?)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Neem*> | Ja | Tekenreeks | De teken reeks die de tijds tempel bevat |
| <*destinationTimeZone*> | Ja | Tekenreeks | De naam voor de tijd zone van het doel. Zie de [micro soft time zone-index waarden](https://support.microsoft.com/help/973627/microsoft-time-zone-index-values)voor tijdzone namen, maar u moet mogelijk alle Lees tekens uit de naam van de tijd zone verwijderen. |
| <*Formatteer*> | Nee | Tekenreeks | Een [enkele indelings aanduiding](/dotnet/standard/base-types/standard-date-and-time-format-strings) of een [aangepast opmaak patroon](/dotnet/standard/base-types/custom-date-and-time-format-strings). De standaard notatie voor de tijds tempel is ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (jjjj-mm-ddTuu: mm: SS: fffffffK), die voldoet aan [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) en tijd zone gegevens behoudt. |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*geconverteerd: tijds tempel*> | Tekenreeks | De tijds tempel die is geconverteerd naar de doel tijdzone |
||||

*Voorbeeld 1*

In dit voor beeld wordt een tijds tempel geconverteerd naar de opgegeven tijd zone:

```
convertFromUtc('2018-01-01T08:00:00.0000000Z', 'Pacific Standard Time')
```

En retourneert dit resultaat: `"2018-01-01T00:00:00Z"`

*Voorbeeld 2*

In dit voor beeld wordt een tijds tempel geconverteerd naar de opgegeven tijd zone en notatie:

```
convertFromUtc('2018-01-01T08:00:00.0000000Z', 'Pacific Standard Time', 'D')
```

En retourneert dit resultaat: `"Monday, January 1, 2018"`

<a name="convertTimeZone"></a>

### <a name="converttimezone"></a>convertTimeZone

Converteer een tijds tempel van de bron tijdzone naar de doel tijdzone.

```
convertTimeZone('<timestamp>', '<sourceTimeZone>', '<destinationTimeZone>', '<format>'?)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Neem*> | Ja | Tekenreeks | De teken reeks die de tijds tempel bevat |
| <*sourceTimeZone*> | Ja | Tekenreeks | De naam voor de bron tijdzone. Zie de [micro soft time zone-index waarden](https://support.microsoft.com/help/973627/microsoft-time-zone-index-values)voor tijdzone namen, maar u moet mogelijk alle Lees tekens uit de naam van de tijd zone verwijderen. |
| <*destinationTimeZone*> | Ja | Tekenreeks | De naam voor de tijd zone van het doel. Zie de [micro soft time zone-index waarden](https://support.microsoft.com/help/973627/microsoft-time-zone-index-values)voor tijdzone namen, maar u moet mogelijk alle Lees tekens uit de naam van de tijd zone verwijderen. |
| <*Formatteer*> | Nee | Tekenreeks | Een [enkele indelings aanduiding](/dotnet/standard/base-types/standard-date-and-time-format-strings) of een [aangepast opmaak patroon](/dotnet/standard/base-types/custom-date-and-time-format-strings). De standaard notatie voor de tijds tempel is ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (jjjj-mm-ddTuu: mm: SS: fffffffK), die voldoet aan [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) en tijd zone gegevens behoudt. |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*geconverteerd: tijds tempel*> | Tekenreeks | De tijds tempel die is geconverteerd naar de doel tijdzone |
||||

*Voorbeeld 1*

In dit voor beeld wordt de bron tijdzone geconverteerd naar de doel tijdzone:

```
convertTimeZone('2018-01-01T08:00:00.0000000Z', 'UTC', 'Pacific Standard Time')
```

En retourneert dit resultaat: `"2018-01-01T00:00:00.0000000"`

*Voorbeeld 2*

In dit voor beeld wordt een tijd zone geconverteerd naar de opgegeven tijd zone en notatie:

```
convertTimeZone('2018-01-01T80:00:00.0000000Z', 'UTC', 'Pacific Standard Time', 'D')
```

En retourneert dit resultaat: `"Monday, January 1, 2018"`

<a name="convertToUtc"></a>

### <a name="converttoutc"></a>convertToUtc

Een tijds tempel van de bron tijdzone converteren naar Universal Time Coordinated (UTC).

```
convertToUtc('<timestamp>', '<sourceTimeZone>', '<format>'?)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Neem*> | Ja | Tekenreeks | De teken reeks die de tijds tempel bevat |
| <*sourceTimeZone*> | Ja | Tekenreeks | De naam voor de bron tijdzone. Zie de [micro soft time zone-index waarden](https://support.microsoft.com/help/973627/microsoft-time-zone-index-values)voor tijdzone namen, maar u moet mogelijk alle Lees tekens uit de naam van de tijd zone verwijderen. |
| <*Formatteer*> | Nee | Tekenreeks | Een [enkele indelings aanduiding](/dotnet/standard/base-types/standard-date-and-time-format-strings) of een [aangepast opmaak patroon](/dotnet/standard/base-types/custom-date-and-time-format-strings). De standaard notatie voor de tijds tempel is ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (jjjj-mm-ddTuu: mm: SS: fffffffK), die voldoet aan [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) en tijd zone gegevens behoudt. |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*geconverteerd: tijds tempel*> | Tekenreeks | De tijds tempel die is geconverteerd naar UTC |
||||

*Voorbeeld 1*

In dit voor beeld wordt een tijds tempel geconverteerd naar UTC:

```
convertToUtc('01/01/2018 00:00:00', 'Pacific Standard Time')
```

En retourneert dit resultaat: `"2018-01-01T08:00:00.0000000Z"`

*Voorbeeld 2*

In dit voor beeld wordt een tijds tempel geconverteerd naar UTC:

```
convertToUtc('01/01/2018 00:00:00', 'Pacific Standard Time', 'D')
```

En retourneert dit resultaat: `"Monday, January 1, 2018"`

<a name="createArray"></a>

### <a name="createarray"></a>createArray

Een matrix van meerdere invoer waarden retour neren.
Zie [matrix ()](#array)voor één invoer matrix.

```
createArray('<object1>', '<object2>', ...)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*object1*>, <*object2*>,... | Ja | Any, maar niet gemengd | Ten minste twee items om de matrix te maken |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| [<*object1*>, <*object2*>,...] | Matrix | De matrix die is gemaakt op basis van alle invoer items |
||||

*Voorbeeld*

In dit voor beeld wordt een matrix gemaakt op basis van deze invoer:

```
createArray('h', 'e', 'l', 'l', 'o')
```

En retourneert dit resultaat: `["h", "e", "l", "l", "o"]`

<a name="dataUri"></a>

### <a name="datauri"></a>dataUri

Een gegevens-URI (Uniform Resource Identifier) retour neren voor een teken reeks.

```
dataUri('<value>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Value*> | Ja | Tekenreeks | De teken reeks die moet worden geconverteerd |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*gegevens-URI*> | Tekenreeks | De gegevens-URI voor de invoer teken reeks |
||||

*Voorbeeld*

In dit voor beeld wordt een gegevens-URI gemaakt voor de teken reeks "Hallo":

```
dataUri('hello')
```

En retourneert dit resultaat: `"data:text/plain;charset=utf-8;base64,aGVsbG8="`

<a name="dataUriToBinary"></a>

### <a name="datauritobinary"></a>dataUriToBinary

Retourneert de binaire versie voor een gegevens-URI (Uniform Resource Identifier).
Gebruik deze functie in plaats van [decodeDataUri ()](#decodeDataUri).
Hoewel beide functies op dezelfde manier werken, verdient de `dataUriBinary()` voor keur.

```
dataUriToBinary('<value>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Value*> | Ja | Tekenreeks | De gegevens-URI die moet worden geconverteerd |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*binary-for-data-URI*> | Tekenreeks | De binaire versie voor de gegevens-URI |
||||

*Voorbeeld*

In dit voor beeld wordt een binaire versie gemaakt voor deze gegevens-URI:

```
dataUriToBinary('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

En retourneert dit resultaat:

`"01100100011000010111010001100001001110100111010001100101011110000111010000101111011100000
1101100011000010110100101101110001110110110001101101000011000010111001001110011011001010111
0100001111010111010101110100011001100010110100111000001110110110001001100001011100110110010
10011011000110100001011000110000101000111010101100111001101100010010001110011100000111101"`

<a name="dataUriToString"></a>

### <a name="datauritostring"></a>dataUriToString

Retourneert de versie van de teken reeks voor een gegevens-URI (Uniform Resource Identifier).

```
dataUriToString('<value>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Value*> | Ja | Tekenreeks | De gegevens-URI die moet worden geconverteerd |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*string-for-data-URI*> | Tekenreeks | De teken reeks versie voor de gegevens-URI |
||||

*Voorbeeld*

In dit voor beeld wordt een teken reeks gemaakt voor deze gegevens-URI:

```
dataUriToString('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

En retourneert dit resultaat: `"hello"`

<a name="dayOfMonth"></a>

### <a name="dayofmonth"></a>dayOfMonth

Retourneert de dag van de maand van een tijds tempel.

```
dayOfMonth('<timestamp>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Neem*> | Ja | Tekenreeks | De teken reeks die de tijds tempel bevat |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*dag van de maand*> | Geheel getal | De dag van de maand van de opgegeven tijds tempel |
||||

*Voorbeeld*

In dit voor beeld wordt het getal voor de dag van de maand uit deze tijds tempel geretourneerd:

```
dayOfMonth('2018-03-15T13:27:36Z')
```

En retourneert dit resultaat: `15`

<a name="dayOfWeek"></a>

### <a name="dayofweek"></a>dayOfWeek

Retourneert de dag van de week van een tijds tempel.

```
dayOfWeek('<timestamp>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Neem*> | Ja | Tekenreeks | De teken reeks die de tijds tempel bevat |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*dag van de week*> | Geheel getal | De dag van de week vanaf de opgegeven tijds tempel waarbij zondag 0 is, maandag 1, enzovoort |
||||

*Voorbeeld*

In dit voor beeld wordt het getal voor de dag van de week uit deze tijds tempel geretourneerd:

```
dayOfWeek('2018-03-15T13:27:36Z')
```

En retourneert dit resultaat: `3`

<a name="dayOfYear"></a>

### <a name="dayofyear"></a>dayOfYear

Retourneert de dag van het jaar van een tijds tempel.

```
dayOfYear('<timestamp>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Neem*> | Ja | Tekenreeks | De teken reeks die de tijds tempel bevat |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*dag van jaar*> | Geheel getal | De dag van het jaar van de opgegeven tijds tempel |
||||

*Voorbeeld*

In dit voor beeld wordt het nummer van de dag van het jaar uit deze tijds tempel geretourneerd:

```
dayOfYear('2018-03-15T13:27:36Z')
```

En retourneert dit resultaat: `74`

<a name="decodeBase64"></a>

### <a name="decodebase64"></a>decodeBase64

Retourneert de teken reeks versie voor een base64-gecodeerde teken reeks, waardoor de base64-teken reeks effectief wordt gedecodeerd.
Overweeg het gebruik van [base64ToString ()](#base64ToString) in plaats van `decodeBase64()` .
Hoewel beide functies op dezelfde manier werken, verdient de `base64ToString()` voor keur.

```
decodeBase64('<value>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Value*> | Ja | Tekenreeks | De met base64 gecodeerde teken reeks die moet worden gedecodeerd |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*gedecodeerd-base64-teken reeks*> | Tekenreeks | De teken reeks versie voor een base64-gecodeerde teken reeks |
||||

*Voorbeeld*

In dit voor beeld wordt een teken reeks gemaakt voor een teken reeks met base64-code ring:

```
decodeBase64('aGVsbG8=')
```

En retourneert dit resultaat: `"hello"`

<a name="decodeDataUri"></a>

### <a name="decodedatauri"></a>decodeDataUri

Retourneert de binaire versie voor een gegevens-URI (Uniform Resource Identifier).
Overweeg het gebruik van [dataUriToBinary ()](#dataUriToBinary)in plaats van `decodeDataUri()` .
Hoewel beide functies op dezelfde manier werken, verdient de `dataUriToBinary()` voor keur.

```
decodeDataUri('<value>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Value*> | Ja | Tekenreeks | De gegevens-URI-teken reeks die moet worden gedecodeerd |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*binary-for-data-URI*> | Tekenreeks | De binaire versie voor een gegevens-URI-teken reeks |
||||

*Voorbeeld*

In dit voor beeld wordt de binaire versie van deze gegevens-URI geretourneerd:

```
decodeDataUri('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

En retourneert dit resultaat:

`"01100100011000010111010001100001001110100111010001100101011110000111010000101111011100000
1101100011000010110100101101110001110110110001101101000011000010111001001110011011001010111
0100001111010111010101110100011001100010110100111000001110110110001001100001011100110110010
10011011000110100001011000110000101000111010101100111001101100010010001110011100000111101"`

<a name="decodeUriComponent"></a>

### <a name="decodeuricomponent"></a>decodeUriComponent

Retourneert een teken reeks waarmee escape tekens worden vervangen door gedecodeerde versies.

```
decodeUriComponent('<value>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Value*> | Ja | Tekenreeks | De teken reeks met de escape-tekens die moeten worden gedecodeerd |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*gedecodeerde URI*> | Tekenreeks | De bijgewerkte teken reeks met de gecodeerde escape tekens |
||||

*Voorbeeld*

In dit voor beeld worden de escape-tekens in deze teken reeks vervangen door gedecodeerde versies:

```
decodeUriComponent('http%3A%2F%2Fcontoso.com')
```

En retourneert dit resultaat: `"https://contoso.com"`

<a name="div"></a>

### <a name="div"></a>div

Retourneert het gehele getal als resultaat van het delen van twee getallen.
Zie [mod ()](#mod)om het rest resultaat op te halen.

```
div(<dividend>, <divisor>)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*dividend*> | Ja | Geheel getal of zwevend | Het getal dat moet worden gedeeld door de *deler* |
| <*reken*> | Ja | Geheel getal of zwevend | Het getal dat het *deelt* deel, maar mag niet 0 zijn |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*quotiënt-resultaat*> | Geheel getal | Het gehele getal als resultaat van het delen van het eerste getal met het tweede getal |
||||

*Voorbeeld*

Beide voor beelden delen het eerste getal door het tweede getal:

```
div(10, 5)
div(11, 5)
```

En retour neren dit resultaat: `2`

<a name="encodeUriComponent"></a>

### <a name="encodeuricomponent"></a>encodeUriComponent

Een gecodeerde URI-versie (Uniform Resource Identifier) retour neren voor een teken reeks door onveilige URL-tekens te vervangen door Escape tekens.
Overweeg het gebruik van [uriComponent ()](#uriComponent)in plaats van `encodeUriComponent()` .
Hoewel beide functies op dezelfde manier werken, verdient de `uriComponent()` voor keur.

```
encodeUriComponent('<value>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Value*> | Ja | Tekenreeks | De teken reeks die moet worden geconverteerd naar een URI-gecodeerde indeling |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*gecodeerde URI*> | Tekenreeks | De teken reeks met URI-code ring met escape tekens |
||||

*Voorbeeld*

In dit voor beeld wordt een met URI gecodeerde versie gemaakt voor deze teken reeks:

```
encodeUriComponent('https://contoso.com')
```

En retourneert dit resultaat: `"http%3A%2F%2Fcontoso.com"`

<a name="empty"></a>

### <a name="empty"></a>leeg

Controleer of een verzameling leeg is.
Retourneert waar als de verzameling leeg is of retourneert onwaar als deze niet leeg is.

```
empty('<collection>')
empty([<collection>])
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*innings*> | Ja | Teken reeks, matrix of object | De verzameling die moet worden gecontroleerd |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| waar of onwaar | Boolean | Retourneert waar als de verzameling leeg is. Retourneert onwaar wanneer niet leeg is. |
||||

*Voorbeeld*

In deze voor beelden wordt gecontroleerd of de opgegeven verzamelingen leeg zijn:

```
empty('')
empty('abc')
```

En retourneert deze resultaten:

* Eerste voor beeld: Hiermee wordt een lege teken reeks door gegeven, waardoor de functie wordt geretourneerd `true` .
* Tweede voor beeld: de teken reeks "ABC" wordt door gegeven, dus de functie retourneert `false` .

<a name="endswith"></a>

### <a name="endswith"></a>endsWith

Controleren of een teken reeks eindigt met een specifieke subtekenreeks.
Retourneert waar als de subtekenreeks wordt gevonden, of retourneert False als het niet is gevonden.
Deze functie is niet hoofdletter gevoelig.

```
endsWith('<text>', '<searchText>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*SMS*> | Ja | Tekenreeks | De teken reeks die moet worden gecontroleerd |
| <*Tekst*> | Ja | Tekenreeks | De laatste subtekenreeks die moet worden gezocht |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| waar of onwaar  | Boolean | Retourneert waar als de laatste subtekenreeks wordt gevonden. Retourneert onwaar wanneer deze niet is gevonden. |
||||

*Voorbeeld 1*

In dit voor beeld wordt gecontroleerd of de teken reeks "Hallo wereld" eindigt op de "wereld"-teken reeks:

```
endsWith('hello world', 'world')
```

En retourneert dit resultaat: `true`

*Voorbeeld 2*

In dit voor beeld wordt gecontroleerd of de teken reeks ' Hallo wereld ' eindigt met de teken reeks ' universum ':

```
endsWith('hello world', 'universe')
```

En retourneert dit resultaat: `false`

<a name="equals"></a>

### <a name="equals"></a>is gelijk aan

Controleer of beide waarden, expressies of objecten gelijkwaardig zijn.
Retourneert waar als beide gelijkwaardig zijn, of retourneert onwaar als deze niet overeenkomen.

```
equals('<object1>', '<object2>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*object1*>, <*object2*> | Ja | Sommige | De waarden, expressies of objecten die u wilt vergelijken |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| waar of onwaar | Boolean | Retourneert waar als beide gelijkwaardig zijn. Retourneert onwaar als dat niet het equivalent is. |
||||

*Voorbeeld*

In deze voor beelden wordt gecontroleerd of de opgegeven invoer gelijkwaardig zijn.

```
equals(true, 1)
equals('abc', 'abcd')
```

En retourneert deze resultaten:

* Eerste voor beeld: beide waarden zijn gelijkwaardig, waardoor de functie wordt geretourneerd `true` .
* Tweede voor beeld: beide waarden zijn niet gelijk aan, dus retourneert de functie `false` .

<a name="first"></a>

### <a name="first"></a>instantie

Het eerste item van een teken reeks of matrix retour neren.

```
first('<collection>')
first([<collection>])
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*innings*> | Ja | Teken reeks of matrix | De verzameling waar het eerste item moet worden gevonden |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*eerste verzameling-item*> | Elk | Het eerste item in de verzameling |
||||

*Voorbeeld*

In deze voor beelden vindt u het eerste item in deze verzamelingen:

```
first('hello')
first(createArray(0, 1, 2))
```

En retour neren deze resultaten:

* Eerste voor beeld: `"h"`
* Tweede voor beeld: `0`

<a name="float"></a>

### <a name="float"></a>float

Een teken reeks versie voor een drijvende-komma getal converteren naar een werkelijk drijvende-komma getal.

```
float('<value>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Value*> | Ja | Tekenreeks | De teken reeks met een geldig drijvende-komma getal dat moet worden geconverteerd |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*float-waarde*> | Float | Het drijvende-komma getal voor de opgegeven teken reeks |
||||

*Voorbeeld*

In dit voor beeld wordt een teken reeks versie gemaakt voor dit getal met drijvende komma:

```
float('10.333')
```

En retourneert dit resultaat: `10.333`

<a name="formatDateTime"></a>

### <a name="formatdatetime"></a>formatDateTime

Een tijds tempel retour neren in de opgegeven notatie.

```
formatDateTime('<timestamp>', '<format>'?)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Neem*> | Ja | Tekenreeks | De teken reeks die de tijds tempel bevat |
| <*Formatteer*> | Nee | Tekenreeks | Een [enkele indelings aanduiding](/dotnet/standard/base-types/standard-date-and-time-format-strings) of een [aangepast opmaak patroon](/dotnet/standard/base-types/custom-date-and-time-format-strings). De standaard notatie voor de tijds tempel is ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (jjjj-mm-ddTuu: mm: SS: fffffffK), die voldoet aan [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) en tijd zone gegevens behoudt. |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*opnieuw geformatteerd-tijds tempel*> | Tekenreeks | De bijgewerkte tijds tempel in de opgegeven indeling |
||||

*Voorbeeld*

In dit voor beeld wordt een tijds tempel geconverteerd naar de opgegeven notatie:

```
formatDateTime('03/15/2018 12:00:00', 'yyyy-MM-ddTHH:mm:ss')
```

En retourneert dit resultaat: `"2018-03-15T12:00:00"`

<a name="getFutureTime"></a>

### <a name="getfuturetime"></a>getFutureTime

De huidige tijds tempel en de opgegeven tijds eenheden retour neren.

```
getFutureTime(<interval>, <timeUnit>, <format>?)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*bereik*> | Ja | Geheel getal | Het aantal opgegeven tijds eenheden dat moet worden toegevoegd |
| <*timeUnit*> | Ja | Tekenreeks | De tijds eenheid die moet worden gebruikt met het *interval*: ' seconde ', ' minuut ', ' uur ', ' dag ', ' week ', ' maand ', ' jaar ' |
| <*Formatteer*> | Nee | Tekenreeks | Een [enkele indelings aanduiding](/dotnet/standard/base-types/standard-date-and-time-format-strings) of een [aangepast opmaak patroon](/dotnet/standard/base-types/custom-date-and-time-format-strings). De standaard notatie voor de tijds tempel is ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (jjjj-mm-ddTuu: mm: SS: fffffffK), die voldoet aan [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) en tijd zone gegevens behoudt. |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*bijgewerkt-tijds tempel*> | Tekenreeks | De huidige tijds tempel plus het opgegeven aantal tijds eenheden |
||||

*Voorbeeld 1*

Stel dat het huidige tijds tempel ' 2018-03-01T00:00:00.0000000 Z ' is.
In dit voor beeld worden vijf dagen toegevoegd aan die tijds tempel:

```
getFutureTime(5, 'Day')
```

En retourneert dit resultaat: `"2018-03-06T00:00:00.0000000Z"`

*Voorbeeld 2*

Stel dat het huidige tijds tempel ' 2018-03-01T00:00:00.0000000 Z ' is.
In dit voor beeld worden vijf dagen toegevoegd en wordt het resultaat geconverteerd naar D-indeling:

```
getFutureTime(5, 'Day', 'D')
```

En retourneert dit resultaat: `"Tuesday, March 6, 2018"`

<a name="getPastTime"></a>

### <a name="getpasttime"></a>getPastTime

Retourneert de huidige tijds tempel min de opgegeven tijds eenheden.

```
getPastTime(<interval>, <timeUnit>, <format>?)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*bereik*> | Ja | Geheel getal | Het aantal opgegeven tijds eenheden dat moet worden afgetrokken |
| <*timeUnit*> | Ja | Tekenreeks | De tijds eenheid die moet worden gebruikt met het *interval*: ' seconde ', ' minuut ', ' uur ', ' dag ', ' week ', ' maand ', ' jaar ' |
| <*Formatteer*> | Nee | Tekenreeks | Een [enkele indelings aanduiding](/dotnet/standard/base-types/standard-date-and-time-format-strings) of een [aangepast opmaak patroon](/dotnet/standard/base-types/custom-date-and-time-format-strings). De standaard notatie voor de tijds tempel is ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (jjjj-mm-ddTuu: mm: SS: fffffffK), die voldoet aan [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) en tijd zone gegevens behoudt. |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*bijgewerkt-tijds tempel*> | Tekenreeks | De huidige tijds tempel min het opgegeven aantal tijds eenheden |
||||

*Voorbeeld 1*

Stel dat het huidige tijds tempel ' 2018-02-01T00:00:00.0000000 Z ' is.
In dit voor beeld worden vijf dagen afgetrokken van die tijds tempel:

```
getPastTime(5, 'Day')
```

En retourneert dit resultaat: `"2018-01-27T00:00:00.0000000Z"`

*Voorbeeld 2*

Stel dat het huidige tijds tempel ' 2018-02-01T00:00:00.0000000 Z ' is.
In dit voor beeld worden vijf dagen afgetrokken en wordt het resultaat geconverteerd naar D-indeling:

```
getPastTime(5, 'Day', 'D')
```

En retourneert dit resultaat: `"Saturday, January 27, 2018"`

<a name="greater"></a>

### <a name="greater"></a>greater

Controleer of de eerste waarde groter is dan de tweede waarde.
Retourneert waar als de eerste waarde meer is of retourneert onwaar wanneer er minder wordt geretourneerd.

```
greater(<value>, <compareTo>)
greater('<value>', '<compareTo>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Value*> | Ja | Geheel getal, float of teken reeks | De eerste waarde die moet worden gecontroleerd of deze hoger is dan de tweede waarde |
| <*compareTo*> | Ja | Respectievelijk geheel getal, float of teken reeks | De vergelijkings waarde |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| waar of onwaar | Boolean | Retourneert waar als de eerste waarde groter is dan de tweede waarde. Retourneert onwaar als de eerste waarde gelijk is aan of kleiner is dan de tweede waarde. |
||||

*Voorbeeld*

Deze voor beelden controleren of de eerste waarde groter is dan de tweede waarde:

```
greater(10, 5)
greater('apple', 'banana')
```

En retour neren deze resultaten:

* Eerste voor beeld: `true`
* Tweede voor beeld: `false`

<a name="greaterOrEquals"></a>

### <a name="greaterorequals"></a>greaterOrEquals

Controleer of de eerste waarde groter is dan of gelijk is aan de tweede waarde.
Retourneert waar als de eerste waarde groter of gelijk is, of retourneert onwaar als de eerste waarde kleiner is.

```
greaterOrEquals(<value>, <compareTo>)
greaterOrEquals('<value>', '<compareTo>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Value*> | Ja | Geheel getal, float of teken reeks | De eerste waarde om te controleren of deze groter dan of gelijk is aan de tweede waarde |
| <*compareTo*> | Ja | Respectievelijk geheel getal, float of teken reeks | De vergelijkings waarde |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| waar of onwaar | Boolean | Retourneert waar als de eerste waarde groter is dan of gelijk is aan de tweede waarde. Retourneert onwaar als de eerste waarde lager is dan de tweede waarde. |
||||

*Voorbeeld*

In deze voor beelden wordt gecontroleerd of de eerste waarde groter is dan of gelijk is aan de tweede waarde:

```
greaterOrEquals(5, 5)
greaterOrEquals('apple', 'banana')
```

En retour neren deze resultaten:

* Eerste voor beeld: `true`
* Tweede voor beeld: `false`

<a name="guid"></a>

### <a name="guid"></a>guid

Genereer een Globally Unique Identifier (GUID) als een teken reeks, bijvoorbeeld ' c2ecc88d-88c8-4096-912c-d6f2e2b138ce ':

```
guid()
```

U kunt ook een andere notatie voor de GUID opgeven dan de standaard indeling, D, die bestaat uit 32 cijfers, gescheiden door afbreek streepjes.

```
guid('<format>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Formatteer*> | Nee | Tekenreeks | Een enkele [indelings aanduiding](/dotnet/api/system.guid.tostring) voor de geretourneerde GUID. De notatie is standaard ingesteld op D, maar u kunt N, D, B, P of X gebruiken ("nb"). |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*GUID-waarde*> | Tekenreeks | Een wille keurig gegenereerde GUID |
||||

*Voorbeeld*

In dit voor beeld wordt dezelfde GUID gegenereerd, maar als 32 cijfers, gescheiden door afbreek streepjes en tussen haakjes:

```
guid('P')
```

En retourneert dit resultaat: `"(c2ecc88d-88c8-4096-912c-d6f2e2b138ce)"`

<a name="if"></a>

### <a name="if"></a>if

Controleer of een expressie True of False is.
Retour neer een opgegeven waarde op basis van het resultaat.

```
if(<expression>, <valueIfTrue>, <valueIfFalse>)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*expressie*> | Ja | Booleaans | De expressie die moet worden gecontroleerd |
| <*valueIfTrue*> | Ja | Elk | De waarde die moet worden geretourneerd wanneer de expressie waar is |
| <*valueIfFalse*> | Ja | Elk | De waarde die moet worden geretourneerd wanneer de expressie onwaar is |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*opgegeven-retourneert-waarde*> | Elk | De opgegeven waarde die wordt geretourneerd op basis van het feit of de expressie waar of onwaar is |
||||

*Voorbeeld*

In dit voor beeld wordt geretourneerd `"yes"` omdat de opgegeven expressie True retourneert.
Anders wordt het volgende geretourneerd `"no"` :

```
if(equals(1, 1), 'yes', 'no')
```

<a name="indexof"></a>

### <a name="indexof"></a>indexOf

Retourneert de start positie of index waarde voor een subtekenreeks.
Deze functie is niet hoofdletter gevoelig en indexen beginnen met het cijfer 0.

```
indexOf('<text>', '<searchText>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*SMS*> | Ja | Tekenreeks | De teken reeks die de subtekenreeks bevat die moet worden gezocht |
| <*Tekst*> | Ja | Tekenreeks | De subtekenreeks die u wilt zoeken |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*index-waarde*>| Geheel getal | De start positie of index waarde voor de opgegeven subtekenreeks. <p>Als de teken reeks niet wordt gevonden, retourneert u het getal-1. |
||||

*Voorbeeld*

In dit voor beeld wordt gezocht naar de start index-waarde voor de subtekenreeks ' World ' in de teken reeks ' Hallo wereld ':

```
indexOf('hello world', 'world')
```

En retourneert dit resultaat: `6`

<a name="int"></a>

### <a name="int"></a>int

Retourneert de versie met gehele getallen voor een teken reeks.

```
int('<value>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Value*> | Ja | Tekenreeks | De teken reeks die moet worden geconverteerd |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*geheel getal-resultaat*> | Geheel getal | De versie van het gehele getal voor de opgegeven teken reeks |
||||

*Voorbeeld*

In dit voor beeld wordt een versie met gehele getallen gemaakt voor de teken reeks "10":

```
int('10')
```

En retourneert dit resultaat: `10`

<a name="json"></a>

### <a name="json"></a>json

De waarde of het object van het type JavaScript Object Notation (JSON) retour neren voor een teken reeks of XML.

```
json('<value>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Value*> | Ja | Teken reeks of XML | De teken reeks of XML die moet worden geconverteerd |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*JSON-resultaat*> | Systeem eigen JSON-type of-object | De waarde of het object van het type JSON voor de opgegeven teken reeks of XML. Als de teken reeks null is, retourneert de functie een leeg object. |
||||

*Voorbeeld 1*

In dit voor beeld wordt deze teken reeks geconverteerd naar de JSON-waarde:

```
json('[1, 2, 3]')
```

En retourneert dit resultaat: `[1, 2, 3]`

*Voorbeeld 2*

In dit voor beeld wordt deze teken reeks geconverteerd naar JSON:

```
json('{"fullName": "Sophia Owen"}')
```

En retourneert dit resultaat:

```
{
  "fullName": "Sophia Owen"
}
```

*Voorbeeld 3*

In dit voor beeld wordt deze XML geconverteerd naar JSON:

```
json(xml('<?xml version="1.0"?> <root> <person id='1'> <name>Sophia Owen</name> <occupation>Engineer</occupation> </person> </root>'))
```

En retourneert dit resultaat:

```json
{
   "?xml": { "@version": "1.0" },
   "root": {
      "person": [ {
         "@id": "1",
         "name": "Sophia Owen",
         "occupation": "Engineer"
      } ]
   }
}
```

<a name="intersection"></a>

### <a name="intersection"></a>Snij punt

Een verzameling retour neren die *alleen* de gemeen schappelijke items in de opgegeven verzamelingen heeft.
Om weer te geven in het resultaat moet een item worden weer gegeven in alle verzamelingen die aan deze functie worden door gegeven.
Als een of meer items dezelfde naam hebben, wordt het laatste item met die naam weer gegeven in het resultaat.

```
intersection([<collection1>], [<collection2>], ...)
intersection('<collection1>', '<collection2>', ...)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*verzameling1*>, <*Collection2*>,... | Ja | Matrix of object, maar niet beide | De verzamelingen van waaruit u *alleen* de algemene items wilt |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*algemeen: items*> | Respectievelijk matrix of object | Een verzameling met alleen de gemeen schappelijke items in de opgegeven verzamelingen |
||||

*Voorbeeld*

In dit voor beeld vindt u de algemene items voor deze matrices:

```
intersection(createArray(1, 2, 3), createArray(101, 2, 1, 10), createArray(6, 8, 1, 2))
```

En retourneert alleen een matrix met *alleen* deze items: `[1, 2]`

<a name="join"></a>

### <a name="join"></a>join

Retourneert een teken reeks die alle items uit een matrix bevat en die elk teken gescheiden door een *scheidings* teken.

```
join([<collection>], '<delimiter>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*innings*> | Ja | Matrix | De matrix waarvan de items moeten worden toegevoegd |
| <*vorm*> | Ja | Tekenreeks | Het scheidings teken dat wordt weer gegeven tussen elk teken in de resulterende teken reeks |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*char1* >< *scheidings teken* >< *char2* >< *scheidings*>... | Tekenreeks | De resulterende teken reeks die is gemaakt op basis van alle items in de opgegeven matrix |
||||

*Voorbeeld*

In dit voor beeld wordt een teken reeks gemaakt van alle items in deze matrix met het opgegeven teken als scheidings teken:

```
join(createArray('a', 'b', 'c'), '.')
```

En retourneert dit resultaat: `"a.b.c"`

<a name="last"></a>

### <a name="last"></a>duren

Het laatste item van een verzameling retour neren.

```
last('<collection>')
last([<collection>])
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*innings*> | Ja | Teken reeks of matrix | De verzameling waar het laatste item moet worden gevonden |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*laatste verzameling-item*> | Respectievelijk een teken reeks of matrix | Het laatste item in de verzameling |
||||

*Voorbeeld*

In deze voor beelden vindt u het laatste item in deze verzamelingen:

```
last('abcd')
last(createArray(0, 1, 2, 3))
```

En retourneert deze resultaten:

* Eerste voor beeld: `"d"`
* Tweede voor beeld: `3`

<a name="lastindexof"></a>

### <a name="lastindexof"></a>lastIndexOf

De start positie of index waarde Retour neren voor het laatste exemplaar van een subtekenreeks.
Deze functie is niet hoofdletter gevoelig en indexen beginnen met het cijfer 0.

```
lastIndexOf('<text>', '<searchText>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*SMS*> | Ja | Tekenreeks | De teken reeks die de subtekenreeks bevat die moet worden gezocht |
| <*Tekst*> | Ja | Tekenreeks | De subtekenreeks die u wilt zoeken |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*eind index-waarde*> | Geheel getal | De start positie of index waarde voor het laatste exemplaar van de opgegeven subtekenreeks. <p>Als de teken reeks niet wordt gevonden, retourneert u het getal-1. |
||||

*Voorbeeld*

In dit voor beeld wordt gezocht naar de begin index waarde voor het laatste exemplaar van de subtekenreeks ' World ' in de teken reeks ' Hallo wereld ':

```
lastIndexOf('hello world', 'world')
```

En retourneert dit resultaat: `6`

<a name="length"></a>

### <a name="length"></a>lengte

Retourneert het aantal items in een verzameling.

```
length('<collection>')
length([<collection>])
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*innings*> | Ja | Teken reeks of matrix | De verzameling met de items die moeten worden geteld |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*lengte-of-aantal*> | Geheel getal | Het aantal items in de verzameling |
||||

*Voorbeeld*

In deze voor beelden wordt het aantal items in deze verzamelingen geteld:

```
length('abcd')
length(createArray(0, 1, 2, 3))
```

En retour neren dit resultaat: `4`

<a name="less"></a>

### <a name="less"></a>less

Controleer of de eerste waarde lager is dan de tweede waarde.
Retourneert waar wanneer de eerste waarde kleiner is, of retourneert False als de eerste waarde meer is.

```
less(<value>, <compareTo>)
less('<value>', '<compareTo>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Value*> | Ja | Geheel getal, float of teken reeks | De eerste waarde die moet worden gecontroleerd of minder dan de tweede waarde |
| <*compareTo*> | Ja | Respectievelijk geheel getal, float of teken reeks | Het vergelijkings item |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| waar of onwaar | Boolean | Retourneert waar als de eerste waarde lager is dan de tweede waarde. Retourneert onwaar als de eerste waarde gelijk is aan of groter is dan de tweede waarde. |
||||

*Voorbeeld*

In deze voor beelden wordt gecontroleerd of de eerste waarde lager is dan de tweede waarde.

```
less(5, 10)
less('banana', 'apple')
```

En retour neren deze resultaten:

* Eerste voor beeld: `true`
* Tweede voor beeld: `false`

<a name="lessOrEquals"></a>

### <a name="lessorequals"></a>lessOrEquals

Controleer of de eerste waarde kleiner is dan of gelijk is aan de tweede waarde.
Retourneert waar als de eerste waarde kleiner is dan of gelijk is aan of retourneert onwaar als de eerste waarde meer is.

```
lessOrEquals(<value>, <compareTo>)
lessOrEquals('<value>', '<compareTo>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Value*> | Ja | Geheel getal, float of teken reeks | De eerste waarde om te controleren of deze kleiner dan of gelijk is aan de tweede waarde |
| <*compareTo*> | Ja | Respectievelijk geheel getal, float of teken reeks | Het vergelijkings item |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| waar of onwaar  | Boolean | Retourneert waar als de eerste waarde kleiner is dan of gelijk is aan de tweede waarde. Retourneert onwaar als de eerste waarde groter is dan de tweede waarde. |
||||

*Voorbeeld*

In deze voor beelden wordt gecontroleerd of de eerste waarde kleiner of gelijk is aan de tweede waarde.

```
lessOrEquals(10, 10)
lessOrEquals('apply', 'apple')
```

En retour neren deze resultaten:

* Eerste voor beeld: `true`
* Tweede voor beeld: `false`

<a name="max"></a>

### <a name="max"></a>max

Retourneert de hoogste waarde van een lijst of matrix met getallen die aan beide uiteinden zijn gekoppeld.

```
max(<number1>, <number2>, ...)
max([<number1>, <number2>, ...])
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*getal1*>, <*getal2*>,... | Ja | Geheel getal, float of beide | De verzameling getallen waarvan u de hoogste waarde wilt |
| [<*getal1*>, <*getal2*>,...] | Ja | Matrix: geheel getal, float of beide | De matrix van getallen waarvan u de hoogste waarde wilt |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*Max-waarde*> | Geheel getal of zwevend | De hoogste waarde in de opgegeven matrix of set getallen |
||||

*Voorbeeld*

In deze voor beelden wordt de hoogste waarde uit de set getallen en de matrix opgehaald:

```
max(1, 2, 3)
max(createArray(1, 2, 3))
```

En retour neren dit resultaat: `3`

<a name="min"></a>

### <a name="min"></a>min.

Retourneert de laagste waarde van een reeks getallen of een matrix.

```
min(<number1>, <number2>, ...)
min([<number1>, <number2>, ...])
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*getal1*>, <*getal2*>,... | Ja | Geheel getal, float of beide | De verzameling getallen waarvan u de laagste waarde wilt |
| [<*getal1*>, <*getal2*>,...] | Ja | Matrix: geheel getal, float of beide | De matrix met getallen waarvan u de laagste waarde wilt |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*min-waarde*> | Geheel getal of zwevend | De laagste waarde in de opgegeven reeks getallen of de opgegeven matrix |
||||

*Voorbeeld*

In deze voor beelden wordt de laagste waarde in de set met getallen en de matrix opgehaald:

```
min(1, 2, 3)
min(createArray(1, 2, 3))
```

En retour neren dit resultaat: `1`

<a name="mod"></a>

### <a name="mod"></a>mod

Retourneert de rest van het delen van twee getallen.
Zie [div ()](#div)om het resultaat van het gehele getal op te halen.

```
mod(<dividend>, <divisor>)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*dividend*> | Ja | Geheel getal of zwevend | Het getal dat moet worden gedeeld door de *deler* |
| <*reken*> | Ja | Geheel getal of zwevend | Het getal dat het *deelt* deel, maar mag niet 0 zijn. |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*modulo-resultaat*> | Geheel getal of zwevend | De rest van het delen van het eerste getal met het tweede getal |
||||

*Voorbeeld*

In het volgende voor beeld wordt het eerste getal in het tweede getal gedeeld:

```
mod(3, 2)
```

En retour neren dit resultaat: `1`

<a name="mul"></a>

### <a name="mul"></a>mul

Retourneert het product van het vermenigvuldigen van twee getallen.

```
mul(<multiplicand1>, <multiplicand2>)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*multiplicand1*> | Ja | Geheel getal of zwevend | Het getal waarmee u wilt vermenigvuldigen met *multiplicand2* |
| <*multiplicand2*> | Ja | Geheel getal of zwevend | Het getal dat meerdere *multiplicand1* |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*product-resultaat*> | Geheel getal of zwevend | Het product van het eerste getal vermenigvuldigen met het tweede getal |
||||

*Voorbeeld*

In deze voor beelden wordt het eerste getal vermenigvuldigd met het tweede getal:

```
mul(1, 2)
mul(1.5, 2)
```

En retour neren deze resultaten:

* Eerste voor beeld: `2`
* Tweede voor beeld `3`

<a name="not"></a>

### <a name="not"></a>not

Controleer of een expressie onwaar is.
Retourneert waar als de expressie onwaar is, of retourneert False als ' True '.

```json
not(<expression>)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*expressie*> | Ja | Booleaans | De expressie die moet worden gecontroleerd |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| waar of onwaar | Boolean | Retourneert waar als de expressie onwaar is. Retourneert onwaar als de expressie waar is. |
||||

*Voorbeeld 1*

In deze voor beelden wordt gecontroleerd of de opgegeven expressies onwaar zijn:

```json
not(false)
not(true)
```

En retour neren deze resultaten:

* Eerste voor beeld: de expressie is onwaar, waardoor de functie wordt geretourneerd `true` .
* Tweede voor beeld: de expressie is waar, dus retourneert de functie `false` .

*Voorbeeld 2*

In deze voor beelden wordt gecontroleerd of de opgegeven expressies onwaar zijn:

```json
not(equals(1, 2))
not(equals(1, 1))
```

En retour neren deze resultaten:

* Eerste voor beeld: de expressie is onwaar, waardoor de functie wordt geretourneerd `true` .
* Tweede voor beeld: de expressie is waar, dus retourneert de functie `false` .

<a name="or"></a>

### <a name="or"></a>of

Controleer of ten minste één expressie waar is.
Retourneert waar als ten minste één expressie waar is, of retourneert False als beide zijn ingesteld op ONWAAR.

```
or(<expression1>, <expression2>)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*expressie1*>, <*Expressie2*> | Ja | Booleaans | De te controleren expressies |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| waar of onwaar | Boolean | Retourneert waar als ten minste één expressie waar is. Retourneert onwaar als beide expressies onwaar zijn. |
||||

*Voorbeeld 1*

Deze voor beelden controleren of ten minste één expressie waar is:

```json
or(true, false)
or(false, false)
```

En retour neren deze resultaten:

* Eerste voor beeld: ten minste één expressie is waar, dus retourneert de functie `true` .
* Tweede voor beeld: beide expressies zijn onwaar, waardoor de functie wordt geretourneerd `false` .

*Voorbeeld 2*

Deze voor beelden controleren of ten minste één expressie waar is:

```json
or(equals(1, 1), equals(1, 2))
or(equals(1, 2), equals(1, 3))
```

En retour neren deze resultaten:

* Eerste voor beeld: ten minste één expressie is waar, dus retourneert de functie `true` .
* Tweede voor beeld: beide expressies zijn onwaar, waardoor de functie wordt geretourneerd `false` .

<a name="rand"></a>

### <a name="rand"></a>ASELECT

Retourneert een wille keurig geheel getal uit een opgegeven bereik, dat alleen aan het begin einde is inbegrepen.

```
rand(<minValue>, <maxValue>)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*minValue*> | Ja | Geheel getal | Het kleinste gehele getal in het bereik |
| <*maxValue*> | Ja | Geheel getal | Het gehele getal dat volgt op het hoogste gehele getal in het bereik dat de functie kan retour neren |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*wille keurig resultaat*> | Geheel getal | Het wille keurig geheel getal dat is geretourneerd uit het opgegeven bereik |
||||

*Voorbeeld*

In dit voor beeld wordt een wille keurig geheel getal opgehaald uit het opgegeven bereik, met uitzonde ring van de maximum waarde:

```
rand(1, 5)
```

En retourneert een van deze getallen als resultaat: `1` , `2` , `3` , of `4`

<a name="range"></a>

### <a name="range"></a>bereik

Retourneert een matrix met gehele getallen die begint met een opgegeven geheel getal.

```
range(<startIndex>, <count>)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Start index*> | Ja | Geheel getal | Een geheel getal dat de matrix als het eerste item start |
| <*aantal*> | Ja | Geheel getal | Het aantal gehele getallen in de matrix |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| [<*bereik-resultaat*>] | Matrix | De matrix met gehele getallen vanaf de opgegeven index |
||||

*Voorbeeld*

In dit voor beeld wordt een matrix met gehele getallen gemaakt die begint met de opgegeven index en het opgegeven aantal gehele getallen bevat:

```
range(1, 4)
```

En retourneert dit resultaat: `[1, 2, 3, 4]`

<a name="replace"></a>

### <a name="replace"></a>vervangen

Vervang een subtekenreeks door de opgegeven teken reeks en retourneert de resultaat teken reeks. Deze functie is hoofdletter gevoelig.

```
replace('<text>', '<oldText>', '<newText>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*SMS*> | Ja | Tekenreeks | De teken reeks met de subtekenreeks die moet worden vervangen |
| <*oldText*> | Ja | Tekenreeks | De subtekenreeks die moet worden vervangen |
| <*newText*> | Ja | Tekenreeks | De vervangende teken reeks |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*bijgewerkt-tekst*> | Tekenreeks | De bijgewerkte teken reeks na het vervangen van de subtekenreeks <p>Als de subtekenreeks niet wordt gevonden, retourneert u de oorspronkelijke teken reeks. |
||||

*Voorbeeld*

In dit voor beeld wordt de subtekenreeks ' old ' in ' Old string ' gezocht en vervangen door ' nieuw ':

```
replace('the old string', 'old', 'new')
```

En retourneert dit resultaat: `"the new string"`

<a name="skip"></a>

### <a name="skip"></a>skip

Verwijder items van de voor kant van een verzameling en retour neer *alle andere* items.

```
skip([<collection>], <count>)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*innings*> | Ja | Matrix | De verzameling waarvan u de items wilt verwijderen |
| <*aantal*> | Ja | Geheel getal | Een positief geheel getal voor het aantal items dat aan de voor grond moet worden verwijderd |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| [<*bijgewerkt-verzameling*>] | Matrix | De bijgewerkte verzameling na het verwijderen van de opgegeven items |
||||

*Voorbeeld*

In dit voor beeld wordt één item, het cijfer 0, van de voor kant van de opgegeven matrix verwijderd:

```
skip(createArray(0, 1, 2, 3), 1)
```

En retourneert deze matrix met de resterende items: `[1,2,3]`

<a name="split"></a>

### <a name="split"></a>split

Retourneert een matrix die subtekenreeksen bevat, gescheiden door komma's, op basis van het opgegeven scheidings teken in de oorspronkelijke teken reeks.

```
split('<text>', '<delimiter>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*SMS*> | Ja | Tekenreeks | De teken reeks die moet worden gescheiden in subtekenreeksen op basis van het opgegeven scheidings teken in de oorspronkelijke teken reeks |
| <*vorm*> | Ja | Tekenreeks | Het teken in de oorspronkelijke teken reeks dat moet worden gebruikt als scheidings teken |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| [<*subtekenreeks1*>, <*substring2*>,...] | Matrix | Een matrix die subtekenreeksen uit de oorspronkelijke teken reeks bevat, gescheiden door komma's |
||||

*Voorbeeld*

In dit voor beeld wordt een matrix gemaakt met subtekenreeksen uit de opgegeven teken reeks op basis van het opgegeven teken als scheidings tekens:

```
split('a_b_c', '_')
```

En retourneert deze matrix als het resultaat: `["a","b","c"]`

<a name="startOfDay"></a>

### <a name="startofday"></a>startOfDay

Retourneert het begin van de dag voor een tijds tempel.

```
startOfDay('<timestamp>', '<format>'?)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Neem*> | Ja | Tekenreeks | De teken reeks die de tijds tempel bevat |
| <*Formatteer*> | Nee | Tekenreeks | Een [enkele indelings aanduiding](/dotnet/standard/base-types/standard-date-and-time-format-strings) of een [aangepast opmaak patroon](/dotnet/standard/base-types/custom-date-and-time-format-strings). De standaard notatie voor de tijds tempel is ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (jjjj-mm-ddTuu: mm: SS: fffffffK), die voldoet aan [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) en tijd zone gegevens behoudt. |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*bijgewerkt-tijds tempel*> | Tekenreeks | Het opgegeven tijds tempel, maar beginnend bij het lege uur voor de dag |
||||

*Voorbeeld*

In dit voor beeld wordt gezocht naar het begin van de dag voor deze tijds tempel:

```
startOfDay('2018-03-15T13:30:30Z')
```

En retourneert dit resultaat: `"2018-03-15T00:00:00.0000000Z"`

<a name="startOfHour"></a>

### <a name="startofhour"></a>startOfHour

Het begin van het uur retour neren voor een tijds tempel.

```
startOfHour('<timestamp>', '<format>'?)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Neem*> | Ja | Tekenreeks | De teken reeks die de tijds tempel bevat |
| <*Formatteer*> | Nee | Tekenreeks | Een [enkele indelings aanduiding](/dotnet/standard/base-types/standard-date-and-time-format-strings) of een [aangepast opmaak patroon](/dotnet/standard/base-types/custom-date-and-time-format-strings). De standaard notatie voor de tijds tempel is ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (jjjj-mm-ddTuu: mm: SS: fffffffK), die voldoet aan [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) en tijd zone gegevens behoudt. |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*bijgewerkt-tijds tempel*> | Tekenreeks | Het opgegeven tijds tempel, maar beginnend bij het nul-minuten teken voor het uur |
||||

*Voorbeeld*

In dit voor beeld vindt u het begin van het uur voor deze tijds tempel:

```
startOfHour('2018-03-15T13:30:30Z')
```

En retourneert dit resultaat: `"2018-03-15T13:00:00.0000000Z"`

<a name="startOfMonth"></a>

### <a name="startofmonth"></a>startOfMonth

Retourneert het begin van de maand voor een tijds tempel.

```
startOfMonth('<timestamp>', '<format>'?)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Neem*> | Ja | Tekenreeks | De teken reeks die de tijds tempel bevat |
| <*Formatteer*> | Nee | Tekenreeks | Een [enkele indelings aanduiding](/dotnet/standard/base-types/standard-date-and-time-format-strings) of een [aangepast opmaak patroon](/dotnet/standard/base-types/custom-date-and-time-format-strings). De standaard notatie voor de tijds tempel is ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (jjjj-mm-ddTuu: mm: SS: fffffffK), die voldoet aan [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) en tijd zone gegevens behoudt. |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*bijgewerkt-tijds tempel*> | Tekenreeks | Het opgegeven tijds tempel dat begint op de eerste dag van de maand met het nul-uur |
||||

*Voorbeeld*

In dit voor beeld wordt het begin van de maand voor deze tijds tempel geretourneerd:

```
startOfMonth('2018-03-15T13:30:30Z')
```

En retourneert dit resultaat: `"2018-03-01T00:00:00.0000000Z"`

<a name="startswith"></a>

### <a name="startswith"></a>startsWith

Controleren of een teken reeks begint met een specifieke subtekenreeks.
Retourneert waar als de subtekenreeks wordt gevonden, of retourneert False als het niet is gevonden.
Deze functie is niet hoofdletter gevoelig.

```
startsWith('<text>', '<searchText>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*SMS*> | Ja | Tekenreeks | De teken reeks die moet worden gecontroleerd |
| <*Tekst*> | Ja | Tekenreeks | De begin teken reeks die moet worden gezocht |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| waar of onwaar  | Boolean | Retourneert waar als de eerste subtekenreeks wordt gevonden. Retourneert onwaar wanneer deze niet is gevonden. |
||||

*Voorbeeld 1*

In dit voor beeld wordt gecontroleerd of de teken reeks "Hallo wereld" begint met de subtekenreeks "Hallo":

```
startsWith('hello world', 'hello')
```

En retourneert dit resultaat: `true`

*Voorbeeld 2*

In dit voor beeld wordt gecontroleerd of de teken reeks "Hallo wereld" begint met de subtekenreeks "Greetings":

```
startsWith('hello world', 'greetings')
```

En retourneert dit resultaat: `false`

<a name="string"></a>

### <a name="string"></a>tekenreeks

Retourneert de teken reeks versie voor een waarde.

```
string(<value>)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Value*> | Ja | Elk | De waarde die moet worden geconverteerd |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*teken reeks-waarde*> | Tekenreeks | De teken reeks versie voor de opgegeven waarde |
||||

*Voorbeeld 1*

In dit voor beeld wordt de teken reeks versie gemaakt voor dit nummer:

```
string(10)
```

En retourneert dit resultaat: `"10"`

*Voorbeeld 2*

In dit voor beeld wordt een teken reeks gemaakt voor het opgegeven JSON-object en wordt de back slash ( \\ ) gebruikt als escape teken voor het dubbele aanhalings tekens (").

```
string( { "name": "Sophie Owen" } )
```

En retourneert dit resultaat: `"{ \\"name\\": \\"Sophie Owen\\" }"`

<a name="sub"></a>

### <a name="sub"></a>sub

Retourneert het resultaat van het aftrekken van het tweede getal uit het eerste getal.

```
sub(<minuend>, <subtrahend>)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*aftrek getal*> | Ja | Geheel getal of zwevend | Het getal waaruit de *aftrekker* moet worden afgetrokken |
| <*aftrekker*> | Ja | Geheel getal of zwevend | Het getal dat moet worden afgetrokken van de *aftrek getal* |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*Daardoor*> | Geheel getal of zwevend | Het resultaat van het aftrekken van het tweede getal uit het eerste getal |
||||

*Voorbeeld*

In dit voor beeld wordt het tweede getal afgetrokken van het eerste getal:

```
sub(10.3, .3)
```

En retourneert dit resultaat: `10`

<a name="substring"></a>

### <a name="substring"></a>subtekenreeks

Retourneert tekens uit een teken reeks, beginnend bij de opgegeven positie of index.
Index waarden beginnen met het cijfer 0.

```
substring('<text>', <startIndex>, <length>)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*SMS*> | Ja | Tekenreeks | De teken reeks waarvan u de tekens wilt |
| <*Start index*> | Ja | Geheel getal | Een positief getal gelijk aan of groter dan 0 dat u wilt gebruiken als de begin positie of index waarde |
| <*lange*> | Ja | Geheel getal | Een positief aantal tekens dat u wilt in de subtekenreeks |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*subtekenreeks-resultaat*> | Tekenreeks | Een subtekenreeks met het opgegeven aantal tekens, beginnend bij de opgegeven index positie in de bron teken reeks |
||||

*Voorbeeld*

In dit voor beeld wordt een subtekenreeks van vijf tekens gemaakt op basis van de opgegeven teken reeks, beginnend bij de index waarde 6:

```
substring('hello world', 6, 5)
```

En retourneert dit resultaat: `"world"`

<a name="subtractFromTime"></a>

### <a name="subtractfromtime"></a>subtractFromTime

Een aantal tijds eenheden aftrekken van een tijds tempel.
Zie ook [getPastTime](#getPastTime).

```
subtractFromTime('<timestamp>', <interval>, '<timeUnit>', '<format>'?)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Neem*> | Ja | Tekenreeks | De teken reeks die de tijds tempel bevat |
| <*bereik*> | Ja | Geheel getal | Het aantal opgegeven tijds eenheden dat moet worden afgetrokken |
| <*timeUnit*> | Ja | Tekenreeks | De tijds eenheid die moet worden gebruikt met het *interval*: ' seconde ', ' minuut ', ' uur ', ' dag ', ' week ', ' maand ', ' jaar ' |
| <*Formatteer*> | Nee | Tekenreeks | Een [enkele indelings aanduiding](/dotnet/standard/base-types/standard-date-and-time-format-strings) of een [aangepast opmaak patroon](/dotnet/standard/base-types/custom-date-and-time-format-strings). De standaard notatie voor de tijds tempel is ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (jjjj-mm-ddTuu: mm: SS: fffffffK), die voldoet aan [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) en tijd zone gegevens behoudt. |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*bijgewerkt-tijds tempel*> | Tekenreeks | De tijds tempel min het opgegeven aantal tijds eenheden |
||||

*Voorbeeld 1*

In dit voor beeld wordt één dag afgetrokken van deze tijds tempel:

```
subtractFromTime('2018-01-02T00:00:00Z', 1, 'Day')
```

En retourneert dit resultaat: `"2018-01-01T00:00:00:0000000Z"`

*Voorbeeld 2*

In dit voor beeld wordt één dag afgetrokken van deze tijds tempel:

```
subtractFromTime('2018-01-02T00:00:00Z', 1, 'Day', 'D')
```

En retourneert dit resultaat met de optionele D-indeling: `"Monday, January, 1, 2018"`

<a name="take"></a>

### <a name="take"></a>take

Items van de voor grond van een verzameling retour neren.

```
take('<collection>', <count>)
take([<collection>], <count>)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*innings*> | Ja | Teken reeks of matrix | De verzameling waarvan u de items wilt |
| <*aantal*> | Ja | Geheel getal | Een positief geheel getal voor het aantal items dat u wilt van de voor grond |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*subset*> of [<*subset*>] | Respectievelijk een teken reeks of matrix | Een teken reeks of matrix met het opgegeven aantal items van de voor grond van de oorspronkelijke verzameling |
||||

*Voorbeeld*

In deze voor beelden wordt het opgegeven aantal items van de voor grond van deze verzamelingen opgehaald:

```
take('abcde', 3)
take(createArray(0, 1, 2, 3, 4), 3)
```

En retour neren deze resultaten:

* Eerste voor beeld: `"abc"`
* Tweede voor beeld: `[0, 1, 2]`

<a name="ticks"></a>

### <a name="ticks"></a>Ticks

De `ticks` eigenschaps waarde voor een opgegeven tijds tempel retour neren.
Een *Tick* is een nano seconden-interval van 100.

```
ticks('<timestamp>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Neem*> | Ja | Tekenreeks | De teken reeks voor een tijds tempel |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*maten-nummer*> | Geheel getal | Het aantal maat streepjes sinds de opgegeven tijds tempel |
||||

<a name="toLower"></a>

### <a name="tolower"></a>toLower

Retourneert een teken reeks met de indeling kleine letters. Als een teken in de teken reeks geen kleine versie heeft, blijft dat teken ongewijzigd in de geretourneerde teken reeks.

```
toLower('<text>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*SMS*> | Ja | Tekenreeks | De teken reeks die moet worden geretourneerd met een kleine letter notatie |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*kleine letters*> | Tekenreeks | De oorspronkelijke teken reeks in kleine letters |
||||

*Voorbeeld*

In dit voor beeld wordt deze teken reeks geconverteerd naar kleine letters:

```
toLower('Hello World')
```

En retourneert dit resultaat: `"hello world"`

<a name="toUpper"></a>

### <a name="toupper"></a>toUpper

Retourneert een teken reeks met een hoofd letter. Als een teken in de teken reeks geen hoofd versie heeft, blijft dat teken ongewijzigd in de geretourneerde teken reeks.

```
toUpper('<text>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*SMS*> | Ja | Tekenreeks | De teken reeks die moet worden geretourneerd met een hoofd letter |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*hoofd letters: tekst*> | Tekenreeks | De oorspronkelijke teken reeks in hoofd letters |
||||

*Voorbeeld*

In dit voor beeld wordt deze teken reeks geconverteerd naar hoofd letters:

```
toUpper('Hello World')
```

En retourneert dit resultaat: `"HELLO WORLD"`

<a name="trim"></a>

### <a name="trim"></a>interne

Verwijder voor loop-en volg spaties uit een teken reeks en retour neer de bijgewerkte teken reeks.

```
trim('<text>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*SMS*> | Ja | Tekenreeks | De teken reeks met de voor loop-en volg spaties die moeten worden verwijderd |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*updatedText*> | Tekenreeks | Een bijgewerkte versie van de oorspronkelijke teken reeks zonder voor loop-of volg spaties |
||||

*Voorbeeld*

In dit voor beeld worden de voor loop-en volg spaties uit de teken reeks "Hallo wereld" verwijderd:

```
trim(' Hello World  ')
```

En retourneert dit resultaat: `"Hello World"`

<a name="union"></a>

### <a name="union"></a>Réunion

Een verzameling retour neren die *alle* items uit de opgegeven verzamelingen bevat.
Om weer te geven in het resultaat, kan een item worden weer gegeven in een verzameling die aan deze functie wordt door gegeven. Als een of meer items dezelfde naam hebben, wordt het laatste item met die naam weer gegeven in het resultaat.

```
union('<collection1>', '<collection2>', ...)
union([<collection1>], [<collection2>], ...)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*verzameling1*>, <*Collection2*>,...  | Ja | Matrix of object, maar niet beide | De verzamelingen van waaruit u wilt dat *alle* items |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*updatedCollection*> | Respectievelijk matrix of object | Een verzameling met alle items uit de opgegeven verzamelingen-geen duplicaten |
||||

*Voorbeeld*

In dit voor beeld worden *alle* items van deze verzamelingen opgehaald:

```
union(createArray(1, 2, 3), createArray(1, 2, 10, 101))
```

En retourneert dit resultaat: `[1, 2, 3, 10, 101]`

<a name="uriComponent"></a>

### <a name="uricomponent"></a>uriComponent

Een gecodeerde URI-versie (Uniform Resource Identifier) retour neren voor een teken reeks door onveilige URL-tekens te vervangen door Escape tekens.
Gebruik deze functie in plaats van [encodeUriComponent ()](#encodeUriComponent).
Hoewel beide functies op dezelfde manier werken, verdient de `uriComponent()` voor keur.

```
uriComponent('<value>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Value*> | Ja | Tekenreeks | De teken reeks die moet worden geconverteerd naar een URI-gecodeerde indeling |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*gecodeerde URI*> | Tekenreeks | De teken reeks met URI-code ring met escape tekens |
||||

*Voorbeeld*

In dit voor beeld wordt een met URI gecodeerde versie gemaakt voor deze teken reeks:

```
uriComponent('https://contoso.com')
```

En retourneert dit resultaat: `"http%3A%2F%2Fcontoso.com"`

<a name="uriComponentToBinary"></a>

### <a name="uricomponenttobinary"></a>uriComponentToBinary

Retourneert de binaire versie voor een onderdeel van een Uniform Resource Identifier (URI).

```
uriComponentToBinary('<value>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Value*> | Ja | Tekenreeks | De URI-gecodeerde teken reeks die moet worden geconverteerd |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*binair-voor-gecodeerde-URI*> | Tekenreeks | De binaire versie voor de teken reeks met URI-code ring. De binaire inhoud is base64-gecodeerd en vertegenwoordigd door `$content` . |
||||

*Voorbeeld*

In dit voor beeld wordt de binaire versie gemaakt voor deze teken reeks met URI-code ring:

```
uriComponentToBinary('http%3A%2F%2Fcontoso.com')
```

En retourneert dit resultaat:

`"001000100110100001110100011101000111000000100101001100
11010000010010010100110010010001100010010100110010010001
10011000110110111101101110011101000110111101110011011011
110010111001100011011011110110110100100010"`

<a name="uriComponentToString"></a>

### <a name="uricomponenttostring"></a>uriComponentToString

Retourneert de teken reeks versie voor een gecodeerde URI-teken reeks (Uniform Resource Identifier), waardoor de gecodeerde URI-teken reeks effectief wordt gedecodeerd.

```
uriComponentToString('<value>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Value*> | Ja | Tekenreeks | De met URI gecodeerde teken reeks die moet worden gedecodeerd |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*gedecodeerde URI*> | Tekenreeks | De gedecodeerde versie voor de teken reeks met URI-code ring |
||||

*Voorbeeld*

In dit voor beeld wordt de gedecodeerde teken reeks versie gemaakt voor deze teken reeks met URI-code ring:

```
uriComponentToString('http%3A%2F%2Fcontoso.com')
```

En retourneert dit resultaat: `"https://contoso.com"`

<a name="utcNow"></a>

### <a name="utcnow"></a>utcNow

De huidige tijds tempel retour neren.

```
utcNow('<format>')
```

U kunt desgewenst een andere indeling opgeven met de <*notatie*> para meter.

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Formatteer*> | Nee | Tekenreeks | Een [enkele indelings aanduiding](/dotnet/standard/base-types/standard-date-and-time-format-strings) of een [aangepast opmaak patroon](/dotnet/standard/base-types/custom-date-and-time-format-strings). De standaard notatie voor de tijds tempel is ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (jjjj-mm-ddTuu: mm: SS: fffffffK), die voldoet aan [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) en tijd zone gegevens behoudt. |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*huidige-tijds tempel*> | Tekenreeks | De huidige datum en tijd |
||||

*Voorbeeld 1*

Stel dat vandaag 15 april 2018 om 1:00:00 uur.
In dit voor beeld wordt het huidige tijds tempel opgehaald:

```
utcNow()
```

En retourneert dit resultaat: `"2018-04-15T13:00:00.0000000Z"`

*Voorbeeld 2*

Stel dat vandaag 15 april 2018 om 1:00:00 uur.
In dit voor beeld wordt de huidige tijds tempel opgehaald met de optionele D-indeling:

```
utcNow('D')
```

En retourneert dit resultaat: `"Sunday, April 15, 2018"`

<a name="xml"></a>

### <a name="xml"></a>xml

De XML-versie retour neren voor een teken reeks die een JSON-object bevat.

```
xml('<value>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Value*> | Ja | Tekenreeks | De teken reeks met het JSON-object dat moet worden geconverteerd <p>Het JSON-object mag slechts één hoofd eigenschap hebben, die geen matrix kan zijn. <br>Gebruik de back slash ( \\ ) als escape-teken voor het dubbele aanhalings teken ("). |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*XML-versie*> | Object | De gecodeerde XML voor de opgegeven teken reeks of het JSON-object |
||||

*Voorbeeld 1*

In dit voor beeld wordt de XML-versie gemaakt voor deze teken reeks, die een JSON-object bevat:

`xml(json('{ \"name\": \"Sophia Owen\" }'))`

En retourneert deze XML-resultaten:

```xml
<name>Sophia Owen</name>
```

*Voorbeeld 2*

Stel dat u dit JSON-object hebt:

```json
{
  "person": {
    "name": "Sophia Owen",
    "city": "Seattle"
  }
}
```

In dit voor beeld wordt XML gemaakt voor een teken reeks die dit JSON-object bevat:

`xml(json('{\"person\": {\"name\": \"Sophia Owen\", \"city\": \"Seattle\"}}'))`

En retourneert deze XML-resultaten:

```xml
<person>
  <name>Sophia Owen</name>
  <city>Seattle</city>
<person>
```

<a name="xpath"></a>

### <a name="xpath"></a>XPath

Controleer XML voor knoop punten of waarden die overeenkomen met een XPath-expressie (XML-pad) en retour neer de overeenkomende knoop punten of waarden. Een XPath-expressie of alleen XPath, helpt u bij het navigeren door een XML-document structuur, zodat u knoop punten of reken waarden kunt selecteren in de XML-inhoud.

```
xpath('<xml>', '<xpath>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*indeling*> | Ja | Elk | De XML-teken reeks die moet worden gezocht naar knoop punten of waarden die overeenkomen met een XPath-expressie waarde |
| <*XPath*> | Ja | Elk | De XPath-expressie die wordt gebruikt voor het zoeken van overeenkomende XML-knoop punten of-waarden |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*XML-knoop punt*> | XML | Een XML-knoop punt wanneer slechts één knoop punt overeenkomt met de opgegeven XPath-expressie |
| <*Value*> | Elk | De waarde van een XML-knoop punt wanneer er slechts één waarde overeenkomt met de opgegeven XPath-expressie |
| [<*XML-knooppunt1*>, <*xml-Knooppunt2*>,...] </br>-of- </br>[<*waarde1*>, <*Value2*>,...] | Matrix | Een matrix met XML-knoop punten of-waarden die overeenkomen met de opgegeven XPath-expressie |
||||

*Voorbeeld 1*

In voor beeld 1: in dit voor beeld worden knoop punten gevonden die overeenkomen met het `<count></count>` knoop punt en worden deze knooppunt waarden toegevoegd aan de `sum()` functie.

`xpath(xml(parameters('items')), 'sum(/produce/item/count)')`

En retourneert dit resultaat: `30`

*Voorbeeld 2*

Voor dit voor beeld zoeken beide expressies knoop punten die overeenkomen met het `<location></location>` knoop punt, in de opgegeven argumenten, die XML met een naam ruimte bevatten. De expressies gebruiken de back slash ( \\ ) als escape-teken voor het dubbele aanhalings teken (").

* *Expressie 1*

  `xpath(xml(body('Http')), '/*[name()=\"file\"]/*[name()=\"location\"]')`

* *Expressie 2*

  `xpath(xml(body('Http')), '/*[local-name()=\"file\" and namespace-uri()=\"http://contoso.com\"]/*[local-name()=\"location\"]')`

Dit zijn de argumenten:

* Dit XML-bestand, dat de XML-document naam ruimte bevat, `xmlns="http://contoso.com"` :

  ```xml
  <?xml version="1.0"?> <file xmlns="http://contoso.com"> <location>Paris</location> </file>
  ```

* Een van beide XPath-expressies:

  * `/*[name()=\"file\"]/*[name()=\"location\"]`

  * `/*[local-name()=\"file\" and namespace-uri()=\"http://contoso.com\"]/*[local-name()=\"location\"]`

Dit is het knoop punt resultaat dat overeenkomt met het `<location></location>` knoop punt:

```xml
<location xmlns="https://contoso.com">Paris</location>
```

*Voorbeeld 3*

In het volgende voor beeld 3 wordt in dit voor beeld de waarde in het `<location></location>` knoop punt gezocht:

`xpath(xml(body('Http')), 'string(/*[name()=\"file\"]/*[name()=\"location\"])')`

En retourneert dit resultaat: `"Paris"`

## <a name="next-steps"></a>Volgende stappen
Zie [systeem variabelen](control-flow-system-variables.md)voor een lijst met systeem variabelen die u in expressies kunt gebruiken.