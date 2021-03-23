---
title: Plannen en uitvoeren met Data Factory
description: Meer informatie over plannings-en uitvoerings aspecten van Azure Data Factory toepassings model.
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: f613a14f84999fced888ae72de11328bc7c90811
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104780001"
---
# <a name="data-factory-scheduling-and-execution"></a>Planning en uitvoering Data Factory
> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. Als u de huidige versie van de Data Factory-service gebruikt, raadpleegt u het artikel [pijplijn uitvoering en triggers](../concepts-pipeline-execution-triggers.md) .

In dit artikel wordt uitleg gegeven over de plannings- en uitvoeringsaspecten van het Azure Data Factory-toepassingsmodel. In dit artikel wordt ervan uitgegaan dat u de basis beginselen van Data Factory toepassings model concepten begrijpt, waaronder activiteiten, pijp lijnen, gekoppelde services en gegevens sets. Raadpleeg de volgende artikelen voor basis concepten van Azure Data Factory:

* [Inleiding tot Data Factory](data-factory-introduction.md)
* [Pijplijnen](data-factory-create-pipelines.md)
* [Gegevenssets](data-factory-create-datasets.md) 

## <a name="start-and-end-times-of-pipeline"></a>Begin-en eind tijd van de pijp lijn
Een pijp lijn is alleen actief tussen de **begin** tijd en **eind** tijd. Deze wordt niet uitgevoerd vóór de begin tijd of na de eind tijd. Als de pijp lijn is onderbroken, wordt deze niet uitgevoerd, ongeacht de begin-en eind tijd. Om een pijp lijn uit te voeren, mag deze niet worden onderbroken. U vindt deze instellingen (begin, einde, onderbroken) in de pijplijn definitie: 

```json
"start": "2017-04-01T08:00:00Z",
"end": "2017-04-01T11:00:00Z"
"isPaused": false
```

Zie het artikel [pipelines maken](data-factory-create-pipelines.md) voor meer informatie over deze eigenschappen. 


## <a name="specify-schedule-for-an-activity"></a>Schema voor een activiteit opgeven
Het is niet de pijp lijn die wordt uitgevoerd. Het zijn de activiteiten in de pijp lijn die worden uitgevoerd in de algehele context van de pijp lijn. U kunt een terugkerend schema voor een activiteit opgeven met behulp van de **scheduler** -sectie van de JSON van de activiteit. U kunt bijvoorbeeld een activiteit zodanig plannen dat elk uur als volgt wordt uitgevoerd:  

```json
"scheduler": {
    "frequency": "Hour",
    "interval": 1
},
```

Zoals in het volgende diagram wordt weer gegeven, wordt met een planning voor een activiteit een reeks tumblingvenstertriggers-vensters gemaakt met de begin-en eind tijd van de pijp lijn. Tumblingvenstertriggers Windows is een reeks niet-overlappende, aaneengesloten tijds intervallen. Deze logische tumblingvenstertriggers-Vensters voor een activiteit worden **activiteit Vensters** genoemd.

![Activity Scheduler-voor beeld](media/data-factory-scheduling-and-execution/scheduler-example.png)

De **scheduler** -eigenschap voor een activiteit is optioneel. Als u deze eigenschap opgeeft, moet deze overeenkomen met de uitgebracht die u opgeeft in de definitie van de uitvoer gegevensset voor de activiteit. Momenteel is de uitvoergegevensset dat wat de planning aanstuurt. Daarom moet u een uitvoer gegevensset maken, zelfs als de activiteit geen uitvoer produceert. 

## <a name="specify-schedule-for-a-dataset"></a>Schema voor een gegevensset opgeven
Een activiteit in een Data Factory pijp lijn kan nul of meer invoer **gegevens sets** hebben en een of meer uitvoer gegevens sets produceren. Voor een activiteit kunt u de uitgebracht opgeven waarop de invoer gegevens beschikbaar zijn of de uitvoer gegevens worden geproduceerd met behulp van de sectie **Beschik baarheid** in de definitie van de gegevensset. 

De **frequentie** in het gedeelte **Beschik baarheid** specificeert de tijds eenheid. De toegestane waarden voor de frequentie zijn: minuut, uur, dag, week en maand. De eigenschap **interval** in het gedeelte Beschik baarheid geeft een vermenigvuldigings factor voor frequentie op. Bijvoorbeeld: als de frequentie is ingesteld op dag en interval is ingesteld op 1 voor een uitvoer gegevensset, worden de uitvoer gegevens dagelijks geproduceerd. Als u de frequentie opgeeft als minuut, raden we u aan het interval in te stellen op ten minste 15. 

In het volgende voor beeld zijn de invoer gegevens elk uur beschikbaar en worden de uitvoer gegevens elk uur geproduceerd ( `"frequency": "Hour", "interval": 1` ). 

**Invoer gegevensset:** 

```json
{
    "name": "AzureSqlInput",
    "properties": {
        "published": false,
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}
```


**Uitvoer gegevensset**

```json
{
    "name": "AzureBlobOutput",
    "properties": {
        "published": false,
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "mypath/{Year}/{Month}/{Day}/{Hour}",
            "format": {
                "type": "TextFormat"
            },
            "partitionedBy": [
                { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
                { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
                { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
                { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "HH" }}
            ]
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

Op dit moment wordt **het schema door de uitvoer gegevensset gestuurd**. Met andere woorden, het schema dat is opgegeven voor de uitvoer gegevensset wordt gebruikt voor het uitvoeren van een activiteit tijdens runtime. Daarom moet u een uitvoer gegevensset maken, zelfs als de activiteit geen uitvoer produceert. Als er voor de activiteit geen invoer nodig is, kunt u het maken van de invoergegevensset overslaan. 

In de volgende pijplijn definitie wordt de **scheduler** -eigenschap gebruikt om schema op te geven voor de activiteit. Deze eigenschap is optioneel. Op dit moment moet het schema voor de activiteit overeenkomen met het schema dat is opgegeven voor de uitvoer gegevensset.
 
```json
{
    "name": "SamplePipeline",
    "properties": {
        "description": "copy activity",
        "activities": [
            {
                "type": "Copy",
                "name": "AzureSQLtoBlob",
                "description": "copy activity",
                "typeProperties": {
                    "source": {
                        "type": "SqlSource",
                        "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 100000,
                        "writeBatchTimeout": "00:05:00"
                    }
                },
                "inputs": [
                    {
                        "name": "AzureSQLInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutput"
                    }
                ],
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                }
            }
        ],
        "start": "2017-04-01T08:00:00Z",
        "end": "2017-04-01T11:00:00Z"
    }
}
```

In dit voor beeld wordt de activiteit elk uur uitgevoerd tussen de begin-en eind tijd van de pijp lijn. De uitvoer gegevens worden elk uur geproduceerd voor een Windows-systeem met een tijd van drie uur (8 AM-9 AM, 9 AM-10 uur en 10 AM 11 uur). 

Elke gegevens eenheid die wordt verbruikt of geproduceerd door een uitvoering van een activiteit, wordt een **gegevens segment** genoemd. Het volgende diagram toont een voor beeld van een activiteit met één invoer gegevensset en één uitvoer gegevensset: 

![Beschikbaarheids planner](./media/data-factory-scheduling-and-execution/availability-scheduler.png)

Het diagram toont de gegevens segmenten per uur voor de gegevensset invoer en uitvoer. Het diagram toont drie invoer segmenten die gereed zijn om te worden verwerkt. De 10-11 uur-activiteit wordt uitgevoerd en produceert het 10-11 AM-uitvoer segment. 

U kunt toegang krijgen tot het tijds interval dat is gekoppeld aan het huidige segment in de JSON van de gegevensset met behulp van variabelen: [slice start](data-factory-functions-variables.md#data-factory-system-variables) en [SliceEnd](data-factory-functions-variables.md#data-factory-system-variables). Op dezelfde manier kunt u toegang krijgen tot het tijds interval dat is gekoppeld aan een activiteiten venster met behulp van de WindowStart en WindowEnd. Het schema van een activiteit moet overeenkomen met het schema van de uitvoer gegevensset voor de activiteit. Daarom zijn de waarden slice start en SliceEnd gelijk aan respectievelijk WindowStart-en WindowEnd-waarden. Zie [Data Factory functies en systeem variabelen](data-factory-functions-variables.md#data-factory-system-variables) artikelen voor meer informatie over deze variabelen.  

U kunt deze variabelen gebruiken voor verschillende doel einden in de JSON van de activiteit. U kunt ze bijvoorbeeld gebruiken om gegevens te selecteren uit invoer-en uitvoer gegevens sets die de tijdreeks gegevens weer geven (bijvoorbeeld: 8 AM tot 9 uur). In dit voor beeld wordt ook **WindowStart** en **WindowEnd** gebruikt om relevante gegevens voor een uitvoering van een activiteit te selecteren en deze te kopiëren naar een blob met de juiste **FolderPath**. De **FolderPath** is para meters voor een afzonderlijke map voor elk uur.  

In het vorige voor beeld is het schema dat is opgegeven voor de invoer-en uitvoer gegevens sets hetzelfde (elk uur). Als de invoer gegevensset voor de activiteit beschikbaar is op een andere frequentie, zegt u elke 15 minuten. de activiteit die deze uitvoer gegevensset produceert, wordt slechts één keer per uur uitgevoerd als de uitvoer gegevensset die het activiteiten schema aanstuurt. Zie [model gegevens sets met verschillende frequenties](#model-datasets-with-different-frequencies)voor meer informatie.

## <a name="dataset-availability-and-policies"></a>Beschik baarheid van gegevensset en beleid
U hebt het gebruik van frequentie-en interval eigenschappen gezien in het gedeelte Beschik baarheid van de definitie van de gegevensset. Er zijn enkele andere eigenschappen die van invloed zijn op de planning en uitvoering van een activiteit. 

### <a name="dataset-availability"></a>Beschik baarheid van gegevensset 
In de volgende tabel worden de eigenschappen beschreven die u kunt gebruiken in het gedeelte **Beschik baarheid** :

| Eigenschap | Beschrijving | Vereist | Standaard |
| --- | --- | --- | --- |
| frequency |Hiermee geeft u de tijds eenheid voor de segment productie van gegevensset op.<br/><br/><b>Ondersteunde frequentie</b>: minuut, uur, dag, week, maand |Ja |NA |
| interval |Hiermee geeft u een vermenigvuldigings factor voor frequentie<br/><br/>"Frequentie x-interval" bepaalt hoe vaak het segment wordt geproduceerd.<br/><br/>Als u wilt dat de gegevensset op elk uur wordt gesegmenteerd, stelt u de <b>frequentie</b> in op <b>uur</b>en het <b>interval</b> in op <b>1</b>.<br/><br/><b>Opmerking</b>: als u een frequentie opgeeft als minuut, raden we u aan het interval in te stellen op niet minder dan 15 |Ja |NA |
| stijl |Hiermee geeft u op of het segment moet worden geproduceerd aan het begin/einde van het interval.<ul><li>StartOfInterval</li><li>EndOfInterval</li></ul><br/><br/>Als de frequentie is ingesteld op month en style is ingesteld op EndOfInterval, wordt het segment gemaakt op de laatste dag van de maand. Als de stijl is ingesteld op StartOfInterval, wordt het segment gegenereerd op de eerste dag van de maand.<br/><br/>Als de frequentie is ingesteld op dag en stijl is ingesteld op EndOfInterval, wordt het segment gemaakt in het afgelopen uur van de dag.<br/><br/>Als de frequentie is ingesteld op uur en de stijl is ingesteld op EndOfInterval, wordt het segment aan het einde van het uur geproduceerd. Bijvoorbeeld, voor een segment voor 1 uur – 2 uur, het segment wordt geproduceerd op 2 uur. |Nee |EndOfInterval |
| anchorDateTime |Definieert de absolute positie in de tijd die door scheduler wordt gebruikt om segment grenzen van het gegevensset te berekenen. <br/><br/><b>Opmerking</b>: als de AnchorDateTime datum delen heeft die nauw keuriger zijn dan de frequentie, worden de gedetailleerde onderdelen genegeerd. <br/><br/>Als het <b>interval</b> bijvoorbeeld <b>elk uur</b> is (frequentie: uur en interval: 1) en de <b>AnchorDateTime</b> <b>minuten en seconden</b>bevat, worden de <b>minuten en seconden</b> delen van de AnchorDateTime genegeerd. |Nee |01/01/0001 |
| offset |Tijds duur waarmee het begin en einde van alle segmenten van de gegevensset worden verschoven. <br/><br/><b>Opmerking</b>: als zowel anchorDateTime als offset zijn opgegeven, is het resultaat de gecombineerde werk tijd. |Nee |NA |

### <a name="offset-example"></a>offset-voor beeld
Standaard worden op basis `"frequency": "Day", "interval": 1` van 12 uur UTC-tijd (middernacht) dagelijks () segmenten gestart. Als u wilt dat de begin tijd 6 uur UTC-tijd in plaats daarvan instelt, stelt u de verschuiving in zoals weer gegeven in het volgende code fragment: 

```json
"availability":
{
    "frequency": "Day",
    "interval": 1,
    "offset": "06:00:00"
}
```
### <a name="anchordatetime-example"></a>anchorDateTime-voor beeld
In het volgende voor beeld wordt de gegevensset elke 23 uur gemaakt. Het eerste segment begint op het tijdstip dat is opgegeven door de anchorDateTime, die is ingesteld op `2017-04-19T08:00:00` (UTC-tijd).

```json
"availability":    
{    
    "frequency": "Hour",        
    "interval": 23,    
    "anchorDateTime":"2017-04-19T08:00:00"    
}
```

### <a name="offsetstyle-example"></a>Voor beeld van verschuiving/stijl
De volgende gegevensset is een maandelijkse gegevensset en wordt geproduceerd op derde van elke maand om 8:00 uur ( `3.08:00:00` ):

```json
"availability": {
    "frequency": "Month",
    "interval": 1,
    "offset": "3.08:00:00", 
    "style": "StartOfInterval"
}
```

### <a name="dataset-policy"></a>Beleid voor gegevensset
Voor een gegevensset kan een validatie beleid worden gedefinieerd waarmee wordt aangegeven hoe de gegevens die door een segment bewerking worden gegenereerd, kunnen worden gevalideerd voordat deze gereed zijn voor gebruik. In dergelijke gevallen, nadat het segment is voltooid, wordt de status van het uitvoer segment gewijzigd in **wachtend** met een substatus van **validatie**. Nadat de segmenten zijn gevalideerd, wordt de segment status gewijzigd in **gereed**. Als er een gegevens segment is gemaakt maar de validatie niet heeft door lopen, worden er geen activiteit uitgevoerd voor downstream-segmenten die afhankelijk zijn van dit segment. [Pijp lijnen bewaken en beheren](data-factory-monitor-manage-pipelines.md) bedekt de verschillende statussen van gegevens segmenten in Data Factory.

De **beleids** sectie in de definitie van de gegevensset definieert de criteria of de voor waarde waaraan de segmenten van de gegevensset moeten voldoen. In de volgende tabel worden de eigenschappen beschreven die u kunt gebruiken in de sectie **beleid** :

| Policy Name | Beschrijving | Toegepast op | Vereist | Standaard |
| --- | --- | --- | --- | --- |
| minimumSizeMB | Valideert dat de gegevens in een **Azure-Blob** voldoen aan de minimale grootte vereisten (in mega bytes). |Azure Blob |Nee |NA |
| minimumRows | Valideert dat de gegevens in **Azure SQL database** of **Azure-tabel** het minimum aantal rijen bevatten. |<ul><li>Azure SQL Database</li><li>Azure Table</li></ul> |Nee |NA |

#### <a name="examples"></a>Voorbeelden
**minimumSizeMB:**

```json
"policy":

{
    "validation":
    {
        "minimumSizeMB": 10.0
    }
}
```

**minimumRows**

```json
"policy":
{
    "validation":
    {
        "minimumRows": 100
    }
}
```

Zie het artikel [gegevens sets maken](data-factory-create-datasets.md) voor meer informatie over deze eigenschappen en voor beelden. 

## <a name="activity-policies"></a>Activiteitbeleid
Beleids regels zijn van invloed op het runtime gedrag van een activiteit, in het bijzonder wanneer het segment van een tabel wordt verwerkt. De volgende tabel bevat de details.

| Eigenschap | Toegestane waarden | Standaardwaarde | Beschrijving |
| --- | --- | --- | --- |
| gelijktijdigheid |Geheel getal <br/><br/>Maximum waarde: 10 |1 |Aantal gelijktijdige uitvoeringen van de activiteit.<br/><br/>Hiermee wordt het aantal uitvoeringen van parallelle activiteit bepaald dat op verschillende segmenten kan plaatsvinden. Als een activiteit bijvoorbeeld een grote set beschik bare gegevens moet door lopen, kan de gegevens verwerking worden versneld met een grotere gelijktijdige waarde. |
| executionPriorityOrder |NewestFirst<br/><br/>OldestFirst |OldestFirst |Bepaalt de volg orde van de gegevens segmenten die worden verwerkt.<br/><br/>Als u bijvoorbeeld 2 segmenten hebt (één op 4 p.m. en een andere op tot 17:00 uur), en beide in behandeling zijn. Als u de executionPriorityOrder instelt op NewestFirst, wordt het segment op 5 PM eerst verwerkt. Op dezelfde manier als u de executionPriorityORder instelt op OldestFIrst, wordt het segment op 4 uur verwerkt. |
| retry |Geheel getal<br/><br/>De maximale waarde mag 10 zijn |0 |Aantal nieuwe pogingen voordat de gegevens verwerking voor het segment als mislukt wordt gemarkeerd. Voor het uitvoeren van de activiteit voor een gegevens segment wordt opnieuw geprobeerd tot het opgegeven aantal nieuwe pogingen. De nieuwe poging wordt zo snel mogelijk na de fout uitgevoerd. |
| timeout |TimeSpan |00:00:00 |Time-out voor de activiteit. Voor beeld: 00:10:00 (impliceert een time-out van 10 minuten)<br/><br/>Als er geen waarde is opgegeven of 0, is de time-out oneindig.<br/><br/>Als de verwerkings tijd van de gegevens op een segment de time-outwaarde overschrijdt, wordt deze geannuleerd en probeert het systeem de verwerking opnieuw uit te voeren. Het aantal nieuwe pogingen is afhankelijk van de eigenschap retry. Als er een time-out optreedt, wordt de status ingesteld op out. |
| spoedig |TimeSpan |00:00:00 |Geef de vertraging op voordat de gegevens verwerking van het segment wordt gestart.<br/><br/>De uitvoering van activiteit voor een gegevens segment wordt gestart nadat de vertraging voorbij de verwachte uitvoerings tijd ligt.<br/><br/>Voor beeld: 00:10:00 (impliceert vertraging van 10 minuten) |
| longRetry |Geheel getal<br/><br/>Maximum waarde: 10 |1 |Het aantal lange nieuwe pogingen voordat de segment uitvoering is mislukt.<br/><br/>longRetry-pogingen zijn gespatieerd door longRetryInterval. Als u dus een tijd tussen nieuwe pogingen wilt opgeven, gebruikt u longRetry. Als zowel een nieuwe poging als een longRetry wordt opgegeven, bevat elke longRetry-poging nieuwe pogingen en het maximum aantal pogingen * longRetry.<br/><br/>Als we bijvoorbeeld de volgende instellingen in het activiteiten beleid hebben:<br/>Opnieuw proberen: 3<br/>longRetry: 2<br/>longRetryInterval: 01:00:00<br/><br/>Stel dat er slechts één segment moet worden uitgevoerd (status is in afwachting) en dat de uitvoering van de activiteit elke keer mislukt. In eerste instantie zou er drie opeenvolgende uitvoerings pogingen kunnen worden uitgevoerd. Na elke poging zou de segment status opnieuw proberen. Nadat de eerste 3 pogingen zijn voltooid, wordt de segment status LongRetry.<br/><br/>Na een uur (dat wil zeggen longRetryInteval waarde), is er een andere set van drie opeenvolgende uitvoerings pogingen. Daarna zou de status van het segment mislukken en worden er geen nieuwe pogingen gedaan. Daarom zijn er in totaal 6 pogingen gedaan.<br/><br/>Als de uitvoering is geslaagd, zou de segment status gereed zijn en worden er geen nieuwe pogingen meer geprobeerd.<br/><br/>longRetry kan worden gebruikt in situaties waar afhankelijke gegevens op niet-deterministische tijden arriveren of de algehele omgeving Flaky is waaronder gegevens verwerking plaatsvindt. In dergelijke gevallen is het mogelijk dat er een nieuwe poging wordt gedaan om het opnieuw te proberen na een tijds interval, waardoor de gewenste uitvoer wordt bereikt.<br/><br/>Woord van voorzichtig: Stel geen hoge waarden in voor longRetry of longRetryInterval. Een hogere waarde impliceert meestal andere systeem problemen. |
| longRetryInterval |TimeSpan |00:00:00 |De vertraging tussen nieuwe pogingen |

Zie het artikel [pijp lijnen](data-factory-create-pipelines.md) voor meer informatie. 

## <a name="parallel-processing-of-data-slices"></a>Parallelle verwerking van gegevens segmenten
U kunt de begin datum voor de pijp lijn in het verleden instellen. Wanneer u dit doet, berekent Data Factory automatisch (achtergrond opvullingen) alle gegevens segmenten in het verleden en begint deze te verwerken. Bijvoorbeeld: als u een pijp lijn maakt met de begin datum 2017-04-01 en de huidige datum 2017-04-10. Als de uitgebracht van de uitvoer gegevensset dagelijks is, begint Data Factory de verwerking van alle segmenten van 2017-04-01 tot 2017-04-09 onmiddellijk uit te voeren, omdat de begin datum in het verleden ligt. Het segment van 2017-04-10 is nog niet verwerkt, omdat de waarde van de eigenschap Style in de sectie Availability standaard EndOfInterval is. Het oudste segment wordt als eerste verwerkt, omdat de standaard waarde van executionPriorityOrder OldestFirst is. Zie de sectie [Beschik baarheid van gegevensset](#dataset-availability) voor een beschrijving van de stijl eigenschap. Zie de sectie [Activity policies](#activity-policies) (Engelstalig) voor een beschrijving van de sectie executionPriorityOrder. 

U kunt back-gevulde gegevens segmenten zo configureren dat ze parallel worden verwerkt door de eigenschap **gelijktijdigheids** in het gedeelte **beleid** van de JSON van de activiteit in te stellen. Deze eigenschap bepaalt het aantal uitvoeringen van parallelle activiteit dat kan plaatsvinden op verschillende segmenten. De standaard waarde voor de gelijktijdigheids eigenschap is 1. Daarom wordt er standaard één segment verwerkt op een tijdstip. De maximum waarde is 10. Wanneer een pijp lijn een grote set beschik bare gegevens moet door lopen, kan de gegevens verwerking worden versneld met een grotere gelijktijdige waarde. 

## <a name="rerun-a-failed-data-slice"></a>Een gegevens segment met fout opnieuw uitvoeren
Als er een fout optreedt tijdens het verwerken van een gegevens segment, kunt u nagaan waarom de verwerking van een segment is mislukt met behulp van Azure Portal-Blades of door de app te controleren en te beheren. Zie [pijp lijnen bewaken en beheren met Azure Portal Blades](data-factory-monitor-manage-pipelines.md) of de [bewakings-en beheer-app](data-factory-monitor-manage-app.md) voor meer informatie.

Bekijk het volgende voor beeld, waarin twee activiteiten worden weer gegeven. Activiteit1 en activiteit 2. Activiteit1 verbruikt een segment van Dataset1 en produceert een segment van Dataset2, dat wordt gebruikt als invoer door Activiteit2 om een segment van de laatste gegevensset te produceren.

![Mislukt segment](./media/data-factory-scheduling-and-execution/failed-slice.png)

In het diagram ziet u dat er een fout is opgetreden bij het genereren van het 9-10 AM-segment voor Dataset2. Data Factory houdt automatisch afhankelijkheid bij voor de gegevensset van de tijd reeks. Als gevolg hiervan wordt de uitvoering van de activiteit niet gestart voor het 9-10 uur downstream-segment.

Met Data Factory hulpprogram ma's voor controle en beheer kunt u de diagnostische logboeken voor het mislukte segment inzoomen om de hoofd oorzaak van het probleem eenvoudig te vinden en deze op te lossen. Nadat u het probleem hebt opgelost, kunt u eenvoudig de uitvoering van de activiteit starten om het defecte segment te maken. Zie [pijp lijnen bewaken en beheren met Azure Portal Blades](data-factory-monitor-manage-pipelines.md) of de [app voor bewaking en beheer](data-factory-monitor-manage-app.md)voor meer informatie over het opnieuw uitvoeren en begrijpen van status overgangen voor gegevens segmenten.

Nadat u het 9-10 AM-segment voor **Dataset2** opnieuw hebt uitgevoerd, start Data Factory de uitvoering van het 9-10-afhankelijke segment op de laatste gegevensset.

![Mislukt segment opnieuw uitvoeren](./media/data-factory-scheduling-and-execution/rerun-failed-slice.png)

## <a name="multiple-activities-in-a-pipeline"></a>Meerdere activiteiten in een pijplijn
Een pijplijn kan echter meer dan één activiteit hebben. Als u meerdere activiteiten in een pijp lijn hebt en de uitvoer van een activiteit geen invoer is van een andere activiteit, kunnen de activiteiten parallel worden uitgevoerd als de invoer gegevens segmenten voor de activiteiten gereed zijn.

U kunt twee activiteiten koppelen (de ene activiteit na de andere laten uitvoeren) door de uitvoergegevensset van één activiteit in te stellen als invoergegevensset voor een andere activiteit. De activiteiten kunnen zich in dezelfde pijp lijn of in verschillende pijp lijnen bevindt. De tweede activiteit wordt alleen uitgevoerd wanneer de eerste wordt voltooid.

Bekijk bijvoorbeeld het volgende geval waarbij een pijp lijn twee activiteiten heeft:

1. Activiteit a1 waarvoor externe invoer gegevensset D1 is vereist en die uitvoer gegevensset D2 produceert.
2. Activity a2 waarvoor invoer van gegevensset D2 is vereist en produceert de uitvoer gegevensset D3.

In dit scenario bevinden activiteiten a1 en a2 zich in dezelfde pijp lijn. De activiteit a1 wordt uitgevoerd wanneer de externe gegevens beschikbaar zijn en de frequentie van de geplande Beschik baarheid wordt bereikt. De activiteit a2 wordt uitgevoerd wanneer de geplande segmenten van D2 beschikbaar worden en de frequentie van de geplande Beschik baarheid wordt bereikt. Als er een fout optreedt in een van de segmenten in de gegevensset D2, wordt a2 niet uitgevoerd voor dat segment totdat het beschikbaar wordt.

De diagram weergave met beide activiteiten in dezelfde pijp lijn ziet eruit als in het volgende diagram:

![De activiteiten in dezelfde pijp lijn koppelen](./media/data-factory-scheduling-and-execution/chaining-one-pipeline.png)

Zoals eerder vermeld, kunnen de activiteiten zich in verschillende pijp lijnen bevindt. In een dergelijk scenario zou de diagram weergave eruitzien als in het volgende diagram:

![Activiteiten in twee pijp lijnen koppelen](./media/data-factory-scheduling-and-execution/chaining-two-pipelines.png)

Zie de sectie opeenvolgend kopiëren in de bijlage voor een voor beeld.

## <a name="model-datasets-with-different-frequencies"></a>Model gegevens sets met verschillende frequenties
In de voor beelden zijn de frequenties voor invoer-en uitvoer gegevens sets en het venster activiteiten schema hetzelfde. Bij sommige scenario's is het mogelijk om uitvoer te produceren met een frequentie die afwijkt van de frequentie van een of meer invoer waarden. Data Factory ondersteunt het model leren van deze scenario's.

### <a name="sample-1-produce-a-daily-output-report-for-input-data-that-is-available-every-hour"></a>Voor beeld 1: een dagelijks uitvoer rapport maken voor invoer gegevens die elk uur beschikbaar zijn
Denk na over een scenario waarin u de invoer meet gegevens van Sens oren elk uur in Azure Blob-opslag hebt beschikbaar. U wilt een dagelijks geaggregeerd rapport maken met statistieken zoals het gemiddelde, het maximum en het minimum voor de dag met [Data Factory Hive-activiteit](data-factory-hive-activity.md).

U kunt dit scenario als volgt model leren met Data Factory:

**Invoer gegevensset**

De invoer bestanden per uur worden in de map voor de opgegeven dag verwijderd. De beschik baarheid voor invoer is ingesteld op **uur** (frequentie: uur, interval: 1).

```json
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
      "partitionedBy": [
        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "MM"}},
        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "dd"}}
      ],
      "format": {
        "type": "TextFormat"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```
**Uitvoer gegevensset**

Er wordt elke dag een uitvoer bestand gemaakt in de map van de dag. De beschik baarheid van de uitvoer is ingesteld op **dag** (frequentie: Day en interval: 1).

```json
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
      "partitionedBy": [
        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "MM"}},
        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "dd"}}
      ],
      "format": {
        "type": "TextFormat"
      }
    },
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```

**Activiteit: Hive-activiteit in een pijp lijn**

Het Hive-script ontvangt de juiste *DateTime* -gegevens als para meters die gebruikmaken van de variabele **WindowStart** , zoals weer gegeven in het volgende code fragment. In het Hive-script wordt deze variabele gebruikt om de gegevens uit de juiste map voor de dag te laden en de aggregatie uit te voeren om de uitvoer te genereren.

```json
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2015-01-01T08:00:00",
    "end":"2015-01-01T11:00:00",
    "description":"hive activity",
    "activities": [
        {
            "name": "SampleHiveActivity",
            "inputs": [
                {
                    "name": "AzureBlobInput"
                }
            ],
            "outputs": [
                {
                    "name": "AzureBlobOutput"
                }
            ],
            "linkedServiceName": "HDInsightLinkedService",
            "type": "HDInsightHive",
            "typeProperties": {
                "scriptPath": "adftutorial\\hivequery.hql",
                "scriptLinkedService": "StorageLinkedService",
                "defines": {
                    "Year": "$$Text.Format('{0:yyyy}',WindowStart)",
                    "Month": "$$Text.Format('{0:MM}',WindowStart)",
                    "Day": "$$Text.Format('{0:dd}',WindowStart)"
                }
            },
            "scheduler": {
                "frequency": "Day",
                "interval": 1
            },            
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 2,
                "timeout": "01:00:00"
            }
         }
     ]
   }
}
```

In het volgende diagram ziet u het scenario van een weergave punt van de gegevens afhankelijkheid.

![Gegevens afhankelijkheid](./media/data-factory-scheduling-and-execution/data-dependency.png)

Het uitvoer segment voor elke dag is afhankelijk van 24 uur segmenten van een invoer-gegevensset. Data Factory deze afhankelijkheden automatisch berekent door de invoer gegevens segmenten te bepalen die binnen dezelfde periode vallen als het uitvoer segment dat moet worden geproduceerd. Als een van de 24 invoer segmenten niet beschikbaar is, Data Factory wacht totdat het invoer segment gereed is voordat de dagelijkse activiteit wordt uitgevoerd.

### <a name="sample-2-specify-dependency-with-expressions-and-data-factory-functions"></a>Voor beeld 2: afhankelijkheid opgeven met expressies en Data Factory functies
Laten we eens kijken naar een ander scenario. Stel dat u een Hive-activiteit hebt waarmee twee invoer gegevens sets worden verwerkt. Een van de gebruikers heeft dagelijks nieuwe gegevens, maar een daarvan krijgt elke week nieuwe gegevens. Stel dat u een koppeling wilt maken tussen de twee invoer en elke dag een uitvoer produceert.

De eenvoudige benadering waarin Data Factory automatisch de juiste invoer segmenten afgeeft om te verwerken door af te stemmen op de tijds periode van het uitvoer gegevens segment, werkt niet.

U moet opgeven dat voor elke uitvoering van de activiteit de Data Factory de gegevens segment van de afgelopen week moet gebruiken voor de wekelijkse invoer gegevensset. U gebruikt Azure Data Factory-functies, zoals wordt weer gegeven in het volgende code fragment om dit gedrag te implementeren.

**Input1: Azure-Blob**

De eerste invoer is de Azure-Blob die dagelijks wordt bijgewerkt.

```json
{
  "name": "AzureBlobInputDaily",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
      "partitionedBy": [
        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "MM"}},
        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "dd"}}
      ],
      "format": {
        "type": "TextFormat"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```

**Input2: Azure-Blob**

Input2 is de Azure-Blob die wekelijks wordt bijgewerkt.

```json
{
  "name": "AzureBlobInputWeekly",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
      "partitionedBy": [
        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "MM"}},
        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "dd"}}
      ],
      "format": {
        "type": "TextFormat"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Day",
      "interval": 7
    }
  }
}
```

**Uitvoer: Azure-Blob**

Er wordt elke dag een uitvoer bestand gemaakt in de map voor de dag. De beschik baarheid van uitvoer is ingesteld op **dag** (frequentie: Day, interval: 1).

```json
{
  "name": "AzureBlobOutputDaily",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
      "partitionedBy": [
        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "MM"}},
        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "dd"}}
      ],
      "format": {
        "type": "TextFormat"
      }
    },
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```

**Activiteit: Hive-activiteit in een pijp lijn**

De Hive-activiteit neemt de twee invoer en produceert elke dag een uitvoer segment. U kunt het uitvoer segment van elke dag opgeven, afhankelijk van het invoer segment van de vorige week voor wekelijkse invoer als volgt.

```json
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2015-01-01T08:00:00",
    "end":"2015-01-01T11:00:00",
    "description":"hive activity",
    "activities": [
      {
        "name": "SampleHiveActivity",
        "inputs": [
          {
            "name": "AzureBlobInputDaily"
          },
          {
            "name": "AzureBlobInputWeekly",
            "startTime": "Date.AddDays(SliceStart, - Date.DayOfWeek(SliceStart))",
            "endTime": "Date.AddDays(SliceEnd,  -Date.DayOfWeek(SliceEnd))"  
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutputDaily"
          }
        ],
        "linkedServiceName": "HDInsightLinkedService",
        "type": "HDInsightHive",
        "typeProperties": {
          "scriptPath": "adftutorial\\hivequery.hql",
          "scriptLinkedService": "StorageLinkedService",
          "defines": {
            "Year": "$$Text.Format('{0:yyyy}',WindowStart)",
            "Month": "$$Text.Format('{0:MM}',WindowStart)",
            "Day": "$$Text.Format('{0:dd}',WindowStart)"
          }
        },
        "scheduler": {
          "frequency": "Day",
          "interval": 1
        },            
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 2,  
          "timeout": "01:00:00"
        }
       }
     ]
   }
}
```

Zie [Data Factory-functies en systeem variabelen](data-factory-functions-variables.md) voor een lijst met functies en systeem variabelen die door Data Factory worden ondersteund.

## <a name="appendix"></a>Bijlage

### <a name="example-copy-sequentially"></a>Voor beeld: kopiëren opeenvolgend
Het is mogelijk om meerdere Kopieer bewerkingen achter elkaar op een sequentieve of bestelde manier uit te voeren. U kunt bijvoorbeeld twee Kopieer activiteiten in een pijp lijn (bestand copyactivity1 en CopyActivity2) hebben met de volgende invoer gegevens sets van uitvoer:   

Bestand copyactivity1

Invoer: gegevensset. Uitvoer: Dataset2.

CopyActivity2

Invoer: Dataset2.  Uitvoer: Dataset3.

CopyActivity2 wordt alleen uitgevoerd als de bestand copyactivity1 is uitgevoerd en Dataset2 beschikbaar is.

Hier volgt de JSON van de voorbeeld pijplijn:

```json
{
    "name": "ChainActivities",
    "properties": {
        "description": "Run activities in sequence",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "copyBehavior": "PreserveHierarchy",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "Dataset1"
                    }
                ],
                "outputs": [
                    {
                        "name": "Dataset2"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00"
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "CopyFromBlob1ToBlob2",
                "description": "Copy data from a blob to another"
            },
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "Dataset2"
                    }
                ],
                "outputs": [
                    {
                        "name": "Dataset3"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00"
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "CopyFromBlob2ToBlob3",
                "description": "Copy data from a blob to another"
            }
        ],
        "start": "2016-08-25T01:00:00Z",
        "end": "2016-08-25T01:00:00Z",
        "isPaused": false
    }
}
```

U ziet dat in het voor beeld de uitvoer gegevensset van de eerste Kopieer activiteit (Dataset2) is opgegeven als invoer voor de tweede activiteit. De tweede activiteit wordt daarom alleen uitgevoerd wanneer de uitvoer gegevensset van de eerste activiteit gereed is.  

In het voor beeld kan CopyActivity2 een andere invoer hebben, zoals Dataset3, maar u geeft Dataset2 op als invoer voor CopyActivity2, zodat de activiteit niet wordt uitgevoerd totdat bestand copyactivity1 is voltooid. Bijvoorbeeld:

Bestand copyactivity1

Invoer: Dataset1. Uitvoer: Dataset2.

CopyActivity2

Invoer: Dataset3, Dataset2. Uitvoer: Dataset4.

```json
{
    "name": "ChainActivities",
    "properties": {
        "description": "Run activities in sequence",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "copyBehavior": "PreserveHierarchy",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "Dataset1"
                    }
                ],
                "outputs": [
                    {
                        "name": "Dataset2"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00"
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "CopyFromBlobToBlob",
                "description": "Copy data from a blob to another"
            },
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "Dataset3"
                    },
                    {
                        "name": "Dataset2"
                    }
                ],
                "outputs": [
                    {
                        "name": "Dataset4"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00"
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "CopyFromBlob3ToBlob4",
                "description": "Copy data from a blob to another"
            }
        ],
        "start": "2017-04-25T01:00:00Z",
        "end": "2017-04-25T01:00:00Z",
        "isPaused": false
    }
}
```

U ziet dat in het voor beeld twee invoer gegevens sets zijn opgegeven voor de tweede Kopieer activiteit. Als er meerdere invoer waarden zijn opgegeven, wordt alleen de eerste invoer-gegevensset gebruikt voor het kopiëren van gegevens, maar andere gegevens sets worden gebruikt als afhankelijkheden. CopyActivity2 wordt pas gestart als aan de volgende voor waarden wordt voldaan:

* Bestand copyactivity1 is voltooid en Dataset2 is beschikbaar. Deze gegevensset wordt niet gebruikt bij het kopiëren van gegevens naar Dataset4. Het fungeert alleen als een plannings afhankelijkheid voor CopyActivity2.   
* Dataset3 is beschikbaar. Deze gegevensset vertegenwoordigt de gegevens die naar de doel locatie worden gekopieerd. 
