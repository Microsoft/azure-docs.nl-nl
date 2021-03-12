---
title: Para meters en expressies gebruiken in Azure Data Factory
description: In dit artikel vindt u informatie over expressies en functies die u kunt gebruiken om data factory entiteiten te maken.
author: ssabat
ms.author: susabat
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/08/2020
ms.openlocfilehash: 8f22645eafa0969eac3d6c4c0645909f8c650cad
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/12/2021
ms.locfileid: "103199818"
---
# <a name="how-to-use-parameters-expressions-and-functions-in-azure-data-factory"></a>Para meters, expressies en functies gebruiken in Azure Data Factory

> [!div class="op_single_selector" title1="Selecteer de versie van de Data Factory-service die u gebruikt:"]
> * [Versie 1:](v1/data-factory-functions-variables.md)
> * [Huidige versie](how-to-expression-language-functions.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In dit document wordt hoofd zakelijk aandacht besteed aan het leren van basis concepten met verschillende voor beelden om de mogelijkheid te bieden om gegevens pijplijnen met para meters te maken in Azure Data Factory. Parameterisering en dynamische expressies zijn dergelijke belang rijke toevoegingen aan ADF, omdat ze een enorme tijd kunnen besparen en een veel flexibele oplossing voor het uitpakken, transformeren, laden (ETL) of extract, Load, trans formatie (ELT) hebben, waardoor de kosten voor het onderhoud van oplossingen aanzienlijk worden verminderd en de implementatie van nieuwe functies in bestaande pijp lijnen kan worden versneld. Deze winsten zijn omdat parameterisering de hoeveelheid harde code ring minimaliseert en het aantal herbruikbare objecten en processen in een oplossing verhoogt.

## <a name="azure-data-factory-ui-and-parameters"></a>Gebruikers interface en-para meters van Azure data factory

Als u geen ervaring hebt met het gebruik van Azure data factory-para meters in de gebruikers interface van ADF, raadpleegt u [Data Factory-UI voor gekoppelde services met para meters](https://docs.microsoft.com/azure/data-factory/parameterize-linked-services#data-factory-ui)  en [Data Factory-gebruikers interface voor door meta gegevens gestuurde pijp lijnen met para meters](https://docs.microsoft.com/azure/data-factory/how-to-use-trigger-parameterization#data-factory-ui) voor visuele

## <a name="parameter-and-expression-concepts"></a>Para meter-en expressie concepten 

U kunt para meters gebruiken om externe waarden door te geven aan pijp lijnen, gegevens sets, gekoppelde services en gegevens stromen. Zodra de para meter is door gegeven aan de resource, kan deze niet meer worden gewijzigd. Door parameterizing resources kunt u ze elke keer opnieuw gebruiken met verschillende waarden. Para meters kunnen afzonderlijk of als onderdeel van expressies worden gebruikt. JSON-waarden in de definitie kunnen letterlijk zijn of expressies die tijdens runtime worden geëvalueerd.

Bijvoorbeeld:  
  
```json
"name": "value"
```

 of  
  
```json
"name": "@pipeline().parameters.password"
```

Expressies kunnen ergens in een JSON-teken reeks waarde worden weer gegeven en hebben altijd een andere JSON-waarde. Hier is het *wacht woord* een pijplijn parameter in de expressie. Als een JSON-waarde een expressie is, wordt de hoofd tekst van de expressie geëxtraheerd door de at-teken ( \@ ) te verwijderen. Als er een letterlijke teken reeks nodig is die begint met \@ , moet deze worden ontsnapd met behulp van \@ \@ . In de volgende voor beelden ziet u hoe expressies worden geëvalueerd.  
  
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

## <a name="examples-of-using-parameters-in-expressions"></a>Voor beelden van het gebruik van para meters in expressies 

### <a name="complex-expression-example"></a>Voor beeld van complexe expressie
In het onderstaande voor beeld ziet u een complex voor beeld dat verwijst naar een diepe subveld van de uitvoer van de activiteit. Als u wilt verwijzen naar een pijplijn parameter die naar een subveld wordt geëvalueerd, gebruikt u de syntaxis [] in plaats van een punt (.)-operator (zoals in het geval van subveld1 en subveld2)

`@activity('*activityName*').output.*subfield1*.*subfield2*[pipeline().parameters.*subfield3*].*subfield4*`

### <a name="dynamic-content-editor"></a>Editor voor dynamische inhoud

De editor voor dynamische inhoud plaatst automatisch tekens in uw inhoud wanneer u klaar bent met het bewerken. De volgende inhoud in de inhouds editor is bijvoorbeeld een interpolatie van de teken reeks met twee expressie functies. 

```json
{ 
  "type": "@{if(equals(1, 2), 'Blob', 'Table' )}",
  "name": "@{toUpper('myData')}"
}
```

De editor voor dynamische inhoud converteert de inhoud naar een expressie `"{ \n  \"type\": \"@{if(equals(1, 2), 'Blob', 'Table' )}\",\n  \"name\": \"@{toUpper('myData')}\"\n}"` . Het resultaat van deze expressie is een JSON-indelings teken reeks die hieronder wordt weer gegeven.

```json
{
  "type": "Table",
  "name": "MYDATA"
}
```

### <a name="a-dataset-with--parameters"></a>Een gegevensset met para meters

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

### <a name="a-pipeline-with--parameters"></a>Een pijp lijn met para meters

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

  
## <a name="calling-functions-within-expressions"></a>Functies aanroepen binnen expressies 

U kunt functies aanroepen binnen expressies. De volgende secties bevatten informatie over de functies die kunnen worden gebruikt in een expressie.  

### <a name="string-functions"></a>Tekenreeksfuncties  

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

### <a name="collection-functions"></a>Verzamelingsfuncties

Als u wilt werken met verzamelingen, meestal matrices, teken reeksen en soms, woorden lijsten, kunt u deze verzamelings functies gebruiken.

| Functie verzameling | Taak |
| ------------------- | ---- |
| [daarin](control-flow-expression-language-functions.md#contains) | Controleer of een verzameling een specifiek item heeft. |
| [leeg](control-flow-expression-language-functions.md#empty) | Controleer of een verzameling leeg is. |
| [instantie](control-flow-expression-language-functions.md#first) | Het eerste item van een verzameling retour neren. |
| [Snij punt](control-flow-expression-language-functions.md#intersection) | Een verzameling retour neren die *alleen* de gemeen schappelijke items in de opgegeven verzamelingen heeft. |
| [Jointypen](control-flow-expression-language-functions.md#join) | Retourneert een teken reeks met *alle* items uit een matrix, gescheiden door het opgegeven teken. |
| [duren](control-flow-expression-language-functions.md#last) | Het laatste item van een verzameling retour neren. |
| [length](control-flow-expression-language-functions.md#length) | Retourneert het aantal items in een teken reeks of matrix. |
| [verdergaan](control-flow-expression-language-functions.md#skip) | Verwijder items van de voor kant van een verzameling en retour neer *alle andere* items. |
| [Houd](control-flow-expression-language-functions.md#take) | Items van de voor grond van een verzameling retour neren. |
| [Réunion](control-flow-expression-language-functions.md#union) | Een verzameling retour neren die *alle* items uit de opgegeven verzamelingen bevat. | 

### <a name="logical-functions"></a>Logische functies  

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
  
### <a name="conversion-functions"></a>Conversiefuncties  

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

### <a name="math-functions"></a>Wiskundige functies  
 Deze functies kunnen worden gebruikt voor beide typen getallen: **gehele** getallen en **zwevende** waarden.  

| Wiskundige functie | Taak |
| ------------- | ---- |
| [add](control-flow-expression-language-functions.md#add) | Het resultaat van het toevoegen van twee getallen retour neren. |
| [div](control-flow-expression-language-functions.md#div) | Retourneert het resultaat van het delen van twee getallen. |
| [aantal](control-flow-expression-language-functions.md#max) | Retourneert de hoogste waarde van een reeks getallen of een matrix. |
| [min.](control-flow-expression-language-functions.md#min) | Retourneert de laagste waarde van een reeks getallen of een matrix. |
| [mod](control-flow-expression-language-functions.md#mod) | Retourneert de rest van het delen van twee getallen. |
| [mul](control-flow-expression-language-functions.md#mul) | Retourneert het product van het vermenigvuldigen van twee getallen. |
| [ASELECT](control-flow-expression-language-functions.md#rand) | Retourneert een wille keurig geheel getal uit een opgegeven bereik. |
| [bereik](control-flow-expression-language-functions.md#range) | Retourneert een matrix met gehele getallen die begint met een opgegeven geheel getal. |
| [sub](control-flow-expression-language-functions.md#sub) | Retourneert het resultaat van het aftrekken van het tweede getal uit het eerste getal. |
  
### <a name="date-functions"></a>Datumfuncties  

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

## <a name="detailed-examples-for-practice"></a>Gedetailleerde voor beelden voor de praktijk

### <a name="detailed-azure-data-factory-copy-pipeline-with-parameters"></a>Gedetailleerde Azure data factory Copy-pijp lijn met para meters 

In deze [zelf studie voor het door geven van de Azure Data Factory-pipeline-para meter](https://azure.microsoft.com/mediahandler/files/resourcefiles/azure-data-factory-passing-parameters/Azure%20data%20Factory-Whitepaper-PassingParameters.pdf) wordt uitgelegd hoe u para meters tussen een pijp lijn en activiteit en tussen de activiteiten kunt door geven.

### <a name="detailed--mapping-data-flow-pipeline-with-parameters"></a>Gegevens stroom pijplijn voor gedetailleerde toewijzing met para meters 

Volg de [toewijzings gegevens stroom met para meters](https://docs.microsoft.com/azure/data-factory/parameters-data-flow) voor een uitgebreid voor beeld over het gebruik van para meters in de gegevens stroom.

### <a name="detailed-metadata-driven-pipeline-with-parameters"></a>Gedetailleerde, door Meta gegevensgestuurde pijp lijn met para meters

Volg de [META gegevensgestuurde pijp lijn met para meters](https://docs.microsoft.com/azure/data-factory/how-to-use-trigger-parameterization) voor meer informatie over het gebruik van para meters voor het ontwerpen van door Meta gegevensgestuurde pijp lijnen. Dit is een veelgebruikte use-case voor para meters.


## <a name="next-steps"></a>Volgende stappen
Zie [systeem variabelen](control-flow-system-variables.md)voor een lijst met systeem variabelen die u in expressies kunt gebruiken.
