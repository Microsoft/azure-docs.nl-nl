---
title: Naslag informatie voor JSON-script Azure Data Factory
description: Voorziet in JSON-schema's voor Data Factory entiteiten.
services: data-factory
documentationcenter: ''
author: dcstwh
ms.author: weetok
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 41ba08aef7aed761c3c6063f97768f22bffe3a36
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/15/2020
ms.locfileid: "97508489"
---
# <a name="azure-data-factory---json-scripting-reference"></a>Naslag informatie voor JSON-script Azure Data Factory
> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory.


Dit artikel bevat JSON-schema's en voor beelden voor het definiëren van Azure Data Factory entiteiten (pijp lijn, activiteit, gegevensset en gekoppelde service).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="pipeline"></a>Pijplijn
De structuur op hoog niveau voor een pijplijn definitie is als volgt:

```json
{
  "name": "SamplePipeline",
  "properties": {
    "description": "Describe what pipeline does",
    "activities": [
    ],
    "start": "2016-07-12T00:00:00",
    "end": "2016-07-13T00:00:00"
  }
}
```

In de volgende tabel worden de eigenschappen in de JSON-definitie van de pijp lijn beschreven:

| Eigenschap | Beschrijving | Vereist
-------- | ----------- | --------
| naam | Naam van de pijplijn. Geef een naam op die de actie vertegenwoordigt die de activiteit of pijp lijn heeft geconfigureerd<br/><ul><li>Maximum aantal tekens: 260</li><li>Moet beginnen met een letter nummer of een onderstrepings teken ( \_ )</li><li>De volgende tekens zijn niet toegestaan: '. ', ' + ', '? ', '/', ' < ', ' > ', ' * ', '% ', ' & ' \\ , ': ', ' '</li></ul> |Yes |
| beschrijving |Tekst waarin wordt beschreven waarvoor de activiteit of pijp lijn wordt gebruikt | No |
| activities | Bevat een lijst met activiteiten. | Yes |
| starten |De begin datum/-tijd voor de pijp lijn. Moet de [ISO-indeling](https://en.wikipedia.org/wiki/ISO_8601)hebben. Bijvoorbeeld: 2014-10-14T16:32:41. <br/><br/>Het is mogelijk om een lokale tijd op te geven, bijvoorbeeld een EST-tijd. Hier volgt een voor beeld: `2016-02-27T06:00:00**-05:00` , die 6 am EST is.<br/><br/>Met de eigenschappen Start en end geeft u de actieve periode voor de pijp lijn op. Uitvoer segmenten worden alleen geproduceerd in deze actieve periode. |No<br/><br/>Als u een waarde voor de eigenschap end opgeeft, moet u een waarde voor de eigenschap Start opgeven.<br/><br/>De begin-en eind tijd kunnen beide leeg zijn om een pijp lijn te maken. U moet beide waarden opgeven om een actieve periode in te stellen voor het uitvoeren van de pijp lijn. Als u geen begin-en eind tijden opgeeft bij het maken van een pijp lijn, kunt u ze later instellen met de cmdlet Set-AzDataFactoryPipelineActivePeriod. |
| beëindigen |Eind datum-tijd voor de pijp lijn. Indien opgegeven moet de ISO-indeling hebben. Bijvoorbeeld: 2014-10-14T17:32:41 <br/><br/>Het is mogelijk om een lokale tijd op te geven, bijvoorbeeld een EST-tijd. Hier volgt een voor beeld: `2016-02-27T06:00:00**-05:00` , die 6 am EST is.<br/><br/>Als u de pijplijn voor onbepaalde tijd wilt uitvoeren, geeft u 9999-09-09 op als waarde voor de eigenschap end. |No <br/><br/>Als u een waarde voor de eigenschap Start opgeeft, moet u een waarde voor de eigenschap End opgeven.<br/><br/>Zie opmerkingen voor de eigenschap **Start** . |
| isPaused |Als deze eigenschap is ingesteld op True, wordt de pijp lijn niet uitgevoerd. Standaard waarde = false. U kunt deze eigenschap gebruiken om in of uit te scha kelen. |No |
| pipelineMode |De methode voor het plannen van uitvoeringen voor de pijp lijn. Toegestane waarden zijn: gepland (standaard), eenmalige.<br/><br/>' Gepland ' geeft aan dat de pijp lijn wordt uitgevoerd op een opgegeven tijds interval op basis van de actieve periode (begin-en eind tijd). ' Eenmalige ' geeft aan dat de pijp lijn slechts één keer wordt uitgevoerd. Eenmalige-pijp lijnen kunnen op dit moment niet worden gewijzigd of bijgewerkt. Zie [eenmalige-pijp lijn](data-factory-create-pipelines.md#onetime-pipeline) voor meer informatie over de instelling van eenmalige. |No |
| expirationTime |De duur van de periode na het maken van de pijp lijn, en deze moet ingericht blijven. Als het geen actieve, mislukte of uitgevoerde uitvoeringen heeft, wordt de pijp lijn automatisch verwijderd zodra de verloop tijd is bereikt. |No |


## <a name="activity"></a>Activiteit
De structuur op hoog niveau voor een activiteit binnen een pijplijn definitie (element activities) is als volgt:

```json
{
    "name": "ActivityName",
    "description": "description",
    "type": "<ActivityType>",
    "inputs":  "[]",
    "outputs":  "[]",
    "linkedServiceName": "MyLinkedService",
    "typeProperties":
    {

    },
    "policy":
    {
    },
    "scheduler":
    {
    }
}
```

De volgende tabel beschrijft de eigenschappen in de JSON-definitie van de activiteit:

| Label | Beschrijving | Vereist |
| --- | --- | --- |
| naam |De naam van de activiteit. Geef een naam op die staat voor de actie die door de activiteit wordt geconfigureerd<br/><ul><li>Maximum aantal tekens: 260</li><li>Moet beginnen met een letter nummer of een onderstrepings teken ( \_ )</li><li>De volgende tekens zijn niet toegestaan: '. ', ' + ', '? ', '/', ' < ', ' > ', ' * ', '% ', ' & ' \\ , ': ', ' '</li></ul> |Yes |
| beschrijving |Tekst waarin wordt beschreven waarvoor de activiteit wordt gebruikt. |No |
| type |Hiermee geeft u het type activiteit op. Zie de secties [data stores](#data-stores) en [Data Transformation activities](#data-transformation-activities) voor verschillende typen activiteiten. |Yes |
| invoer |Invoer tabellen die worden gebruikt door de activiteit<br/><br/>`// one input table`<br/>`"inputs":  [ { "name": "inputtable1"  } ],`<br/><br/>`// two input tables` <br/>`"inputs":  [ { "name": "inputtable1"  }, { "name": "inputtable2"  } ],` |Geen voor HDInsightStreaming-en SqlServerStoredProcedure-activiteiten <br/> <br/> Ja voor alle anderen |
| uitvoer |Uitvoer tabellen die worden gebruikt door de activiteit.<br/><br/>`// one output table`<br/>`"outputs":  [ { "name": “outputtable1” } ],`<br/><br/>`//two output tables`<br/>`"outputs":  [ { "name": “outputtable1” }, { "name": “outputtable2” }  ],` |Yes |
| linkedServiceName |De naam van de gekoppelde service die door de activiteit wordt gebruikt. <br/><br/>Een activiteit kan vereisen dat u de gekoppelde service opgeeft die is gekoppeld aan de vereiste rekenomgeving. |Ja voor HDInsight-activiteiten, Azure Machine Learning Studio (klassieke) activiteiten en opgeslagen procedure-activiteiten. <br/><br/>Nee voor alle andere |
| typeProperties |De eigenschappen in de sectie typeProperties zijn afhankelijk van het type van de activiteit. |No |
| policy |Beleidsregels die van invloed zijn op het runtimegedrag van de activiteit. Als deze niet is opgegeven, wordt standaard beleid gebruikt. |No |
| scheduler |de eigenschap scheduler wordt gebruikt voor het definiëren van de gewenste planning voor de activiteit. De subeigenschappen zijn hetzelfde als die in de [eigenschap Beschik baarheid in een gegevensset](data-factory-create-datasets.md#dataset-availability). |No |

### <a name="policies"></a>Beleidsregels
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

### <a name="typeproperties-section"></a>de sectie typeProperties
De sectie typeProperties verschilt voor elke activiteit. Transformatie activiteiten hebben alleen de type-eigenschappen. Zie de sectie [activiteiten voor gegevens transformatie](#data-transformation-activities) in dit artikel voor json-voor beelden waarmee transformatie activiteiten in een pijp lijn worden gedefinieerd.

De **Kopieer activiteit** heeft twee subsecties in de sectie typeProperties: **bron** en **sink**. Zie de sectie [gegevens archieven](#data-stores) in dit artikel voor json-voor beelden die laten zien hoe u een gegevens archief kunt gebruiken als bron en/of sink.

### <a name="sample-copy-pipeline"></a>Voorbeeld van kopieerpijplijn
De volgende voorbeeldpijplijn bevat een activiteit van het type **Copy** in de sectie **activities**. In dit voor beeld kopieert de [Kopieer activiteit](data-factory-data-movement-activities.md) gegevens van een Azure Blob-opslag naar Azure SQL database.

```json
{
  "name": "CopyPipeline",
  "properties": {
    "description": "Copy data from a blob to Azure SQL table",
    "activities": [
      {
        "name": "CopyFromBlobToSQL",
        "type": "Copy",
        "inputs": [
          {
            "name": "InputDataset"
          }
        ],
        "outputs": [
          {
            "name": "OutputDataset"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "SqlSink",
            "writeBatchSize": 10000,
            "writeBatchTimeout": "60:00:00"
          }
        },
        "Policy": {
          "concurrency": 1,
          "executionPriorityOrder": "NewestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
    ],
    "start": "2016-07-12T00:00:00",
    "end": "2016-07-13T00:00:00"
  }
}
```

Houd rekening met de volgende punten:

* In het gedeelte Activiteiten is er slechts één activiteit waarvan **type** is ingesteld op **Copy**.
* De invoer voor de activiteit is ingesteld op **InputDataset** en de uitvoer voor de activiteit is ingesteld op **OutputDataset**.
* In het gedeelte **typeProperties** is **BlobSource** opgegeven als het brontype en **SqlSink** als het sink-type.

Zie de sectie [gegevens archieven](#data-stores) in dit artikel voor json-voor beelden die laten zien hoe u een gegevens archief kunt gebruiken als bron en/of sink.

Zie [zelf studie: gegevens kopiëren van Blob Storage naar SQL database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)voor een volledig overzicht van het maken van deze pijp lijn.

### <a name="sample-transformation-pipeline"></a>Voorbeeld van pijplijn voor transformatie
De volgende voorbeeldpijplijn bevat een activiteit van het type **HDInsightHive** in de sectie **activities**. In dit voorbeeld transformeert de [HDInsight Hive-activiteit](data-factory-hive-activity.md) gegevens uit een Azure-blobopslag door een Hive-scriptbestand uit te voeren op een Azure HDInsight Hadoop-cluster.

```json
{
    "name": "TransformPipeline",
    "properties": {
        "description": "My first Azure Data Factory pipeline",
        "activities": [
            {
                "type": "HDInsightHive",
                "typeProperties": {
                    "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
                    "scriptLinkedService": "AzureStorageLinkedService",
                    "defines": {
                        "inputtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/inputdata",
                        "partitionedtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/partitioneddata"
                    }
                },
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
                "policy": {
                    "concurrency": 1,
                    "retry": 3
                },
                "scheduler": {
                    "frequency": "Month",
                    "interval": 1
                },
                "name": "RunSampleHiveActivity",
                "linkedServiceName": "HDInsightOnDemandLinkedService"
            }
        ],
        "start": "2016-04-01T00:00:00",
        "end": "2016-04-02T00:00:00",
        "isPaused": false
    }
}
```

Houd rekening met de volgende punten:

* In het gedeelte activities is er slechts één activiteit waarvan **type** is ingesteld op **HDInsightHive**.
* Het Hive-scriptbestand **partitionweblogs.hql** wordt opgeslagen in het Azure-opslagaccount (opgegeven door de scriptLinkedService met de naam **AzureStorageLinkedService**) en in de map **script** van de container **adfgetstarted**.
* De sectie **definieert** wordt gebruikt om de runtime-instellingen op te geven die worden door gegeven aan het Hive-script als Hive-configuratie waarden (bijvoorbeeld `${hiveconf:inputtable}` `${hiveconf:partitionedtable}` ).

Zie de sectie [activiteiten voor gegevens transformatie](#data-transformation-activities) in dit artikel voor json-voor beelden waarmee transformatie activiteiten in een pijp lijn worden gedefinieerd.

Zie [zelf studie: uw eerste pijp lijn maken om gegevens te verwerken met een Hadoop-cluster](data-factory-build-your-first-pipeline.md)voor een volledig overzicht van het maken van deze pijp lijn.

## <a name="linked-service"></a>Gekoppelde service
De structuur op hoog niveau voor een gekoppelde service definitie is als volgt:

```json
{
    "name": "<name of the linked service>",
    "properties": {
        "type": "<type of the linked service>",
        "typeProperties": {
        }
    }
}
```

De volgende tabel beschrijft de eigenschappen in de JSON-definitie van de activiteit:

| Eigenschap | Beschrijving | Vereist |
| -------- | ----------- | -------- |
| naam | De naam van de gekoppelde service. | Yes |
| eigenschappen: type | Het type van de gekoppelde service. Bijvoorbeeld: Azure Storage, Azure SQL Database. |
| typeProperties | De sectie typeProperties bevat elementen die verschillend zijn voor elke gegevens opslag of COMPUTE-omgeving. Zie de sectie gegevens archieven voor alle gekoppelde Data Store-Services en [reken omgevingen](#compute-environments) voor alle gekoppelde reken Services |

## <a name="dataset"></a>Gegevensset
Een gegevensset in Azure Data Factory wordt als volgt gedefinieerd:

```json
{
    "name": "<name of dataset>",
    "properties": {
        "type": "<type of dataset: AzureBlob, AzureSql etc...>",
        "external": <boolean flag to indicate external data. only for input datasets>,
        "linkedServiceName": "<Name of the linked service that refers to a data store.>",
        "structure": [
            {
                "name": "<Name of the column>",
                "type": "<Name of the type>"
            }
        ],
        "typeProperties": {
            "<type specific property>": "<value>",
            "<type specific property 2>": "<value 2>",
        },
        "availability": {
            "frequency": "<Specifies the time unit for data slice production. Supported frequency: Minute, Hour, Day, Week, Month>",
            "interval": "<Specifies the interval within the defined frequency. For example, frequency set to 'Hour' and interval set to 1 indicates that new data slices should be produced hourly>"
        },
        "policy":
        {
        }
    }
}
```

In de volgende tabel worden de eigenschappen in de bovenstaande JSON beschreven:

| Eigenschap | Beschrijving | Vereist | Standaard |
| --- | --- | --- | --- |
| naam | De naam van de gegevensset. Zie [Azure Data Factory naamgevings regels](data-factory-naming-rules.md) voor naamgevings regels. |Yes |NA |
| type | Het type van de gegevensset. Geef een van de typen op die worden ondersteund door Azure Data Factory (bijvoorbeeld: AzureBlob, AzureSqlTable). Zie de sectie [gegevens archieven](#data-stores) voor alle gegevens archieven en gegevensset typen die door Data Factory worden ondersteund. |
| structuur | Schema van de gegevensset. Het bevat kolommen, hun typen, enzovoort. | No |NA |
| typeProperties | Eigenschappen die overeenkomen met het geselecteerde type. Zie de sectie [gegevens archieven](#data-stores) voor ondersteunde typen en de bijbehorende eigenschappen. |Yes |NA |
| external | Een Booleaanse vlag die aangeeft of een gegevensset expliciet wordt geproduceerd door een data factory pijp lijn of niet. |No |onjuist |
| availability | Hiermee wordt het verwerkings venster of het segment model gedefinieerd voor de productie van de gegevensset. Zie voor meer informatie over het segmenteren van gegevensset het [plannen en uitvoeren](data-factory-scheduling-and-execution.md) van het artikel. |Yes |NA |
| policy |Hiermee worden de criteria gedefinieerd of de voor waarde waaraan de segmenten van de gegevensset moeten voldoen. <br/><br/>Zie het gedeelte gegevensset Policy voor meer informatie. |No |NA |

Elke kolom in de sectie **structure** bevat de volgende eigenschappen:

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| naam |De naam van de kolom. |Yes |
| type |Het gegevens type van de kolom.  |No |
| culturele |Op .NET gebaseerde cultuur die moet worden gebruikt wanneer type is opgegeven en .NET-type is `Datetime` of `Datetimeoffset` . De standaardinstelling is `en-us`. |No |
| indeling |Indelings teken reeks die moet worden gebruikt wanneer type is opgegeven en .NET-type `Datetime` of is `Datetimeoffset` . |No |

In het volgende voor beeld heeft de gegevensset drie kolommen `slicetimestamp` , en `projectname` `pageviews` en deze zijn van het type: teken reeks, teken reeks en decimaal.

```json
structure:
[
    { "name": "slicetimestamp", "type": "String"},
    { "name": "projectname", "type": "String"},
    { "name": "pageviews", "type": "Decimal"}
]
```

In de volgende tabel worden de eigenschappen beschreven die u kunt gebruiken in het gedeelte **Beschik baarheid** :

| Eigenschap | Beschrijving | Vereist | Standaard |
| --- | --- | --- | --- |
| frequency |Hiermee geeft u de tijds eenheid voor de segment productie van gegevensset op.<br/><br/><b>Ondersteunde frequentie</b>: minuut, uur, dag, week, maand |Yes |NA |
| interval |Hiermee geeft u een vermenigvuldigings factor voor frequentie<br/><br/>"Frequentie x-interval" bepaalt hoe vaak het segment wordt geproduceerd.<br/><br/>Als u wilt dat de gegevensset op elk uur wordt gesegmenteerd, stelt u de <b>frequentie</b> in op <b>uur</b>en het <b>interval</b> in op <b>1</b>.<br/><br/><b>Opmerking</b>: als u een frequentie opgeeft als minuut, raden we u aan het interval in te stellen op niet minder dan 15 |Yes |NA |
| stijl |Hiermee geeft u op of het segment moet worden geproduceerd aan het begin/einde van het interval.<ul><li>StartOfInterval</li><li>EndOfInterval</li></ul><br/><br/>Als de frequentie is ingesteld op month en style is ingesteld op EndOfInterval, wordt het segment gemaakt op de laatste dag van de maand. Als de stijl is ingesteld op StartOfInterval, wordt het segment gegenereerd op de eerste dag van de maand.<br/><br/>Als de frequentie is ingesteld op dag en stijl is ingesteld op EndOfInterval, wordt het segment gemaakt in het afgelopen uur van de dag.<br/><br/>Als de frequentie is ingesteld op uur en de stijl is ingesteld op EndOfInterval, wordt het segment aan het einde van het uur geproduceerd. Bijvoorbeeld, voor een segment voor 1 uur – 2 uur, het segment wordt geproduceerd op 2 uur. |No |EndOfInterval |
| anchorDateTime |Definieert de absolute positie in de tijd die door scheduler wordt gebruikt om segment grenzen van het gegevensset te berekenen. <br/><br/><b>Opmerking</b>: als de AnchorDateTime datum delen heeft die nauw keuriger zijn dan de frequentie, worden de gedetailleerde onderdelen genegeerd. <br/><br/>Als het <b>interval</b> bijvoorbeeld <b>elk uur</b> is (frequentie: uur en interval: 1) en de <b>AnchorDateTime</b> <b>minuten en seconden</b> bevat, worden de <b>minuten en seconden</b> delen van de AnchorDateTime genegeerd. |No |01/01/0001 |
| offset |Tijds duur waarmee het begin en einde van alle segmenten van de gegevensset worden verschoven. <br/><br/><b>Opmerking</b>: als zowel anchorDateTime als offset zijn opgegeven, is het resultaat de gecombineerde werk tijd. |No |NA |

In de volgende sectie Beschik baarheid wordt aangegeven dat de uitvoer gegevensset per uur wordt geproduceerd (of) de ingevoerde gegevensset is elk uur beschikbaar:

```json
"availability":
{
    "frequency": "Hour",
    "interval": 1
}
```

De **beleids** sectie in de definitie van de gegevensset definieert de criteria of de voor waarde waaraan de segmenten van de gegevensset moeten voldoen.

| Policy Name | Beschrijving | Toegepast op | Vereist | Standaard |
| --- | --- | --- | --- | --- |
| minimumSizeMB |Valideert dat de gegevens in een **Azure-Blob** voldoen aan de minimale grootte vereisten (in mega bytes). |Azure Blob |No |NA |
| minimumRows |Valideert dat de gegevens in **Azure SQL database** of een **Azure-tabel** het minimum aantal rijen bevatten. |<ul><li>Azure SQL Database</li><li>Azure Table</li></ul> |No |NA |

**Voorbeeld:**

```json
"policy":

{
    "validation":
    {
        "minimumSizeMB": 10.0
    }
}
```

Tenzij er een gegevensset wordt geproduceerd door Azure Data Factory, moet deze worden gemarkeerd als **extern**. Deze instelling is in het algemeen van toepassing op de invoer van de eerste activiteit in een pijp lijn tenzij activiteit of pijplijn keten wordt gebruikt.

| Naam | Beschrijving | Vereist | Standaardwaarde |
| --- | --- | --- | --- |
| dataDelay |Tijd om de controle over de beschik baarheid van de externe gegevens voor het opgegeven segment te vertragen. Als de gegevens bijvoorbeeld elk uur beschikbaar zijn, is de controle om te zien of de externe gegevens beschikbaar zijn en kan het bijbehorende segment worden uitgesteld met behulp van dataDelay.<br/><br/>Is alleen van toepassing op de huidige tijd.  Als het bijvoorbeeld 1:00 PM nu is en deze waarde 10 minuten is, begint de validatie om 1:10 PM.<br/><br/>Deze instelling heeft geen invloed op segmenten in het verleden (segmenten met eind tijd segment + dataDelay < nu) worden zonder vertraging verwerkt.<br/><br/>De tijd die groter is dan 23:59 uur, moet worden opgegeven met de `day.hours:minutes:seconds` notatie. Als u bijvoorbeeld 24 uur wilt opgeven, gebruikt u 24:00:00 niet. gebruik in plaats daarvan 1,00:00:00. Als u 24:00:00 gebruikt, wordt dit beschouwd als 24 dagen (24.00:00:00). Geef voor 1 dag en 4 uur 1:04:00:00 op. |No |0 |
| retryInterval |De wacht tijd tussen een storing en de volgende nieuwe poging. Als een poging mislukt, wordt de volgende poging na retryInterval. <br/><br/>Als deze 1:00 PM nu is, beginnen we met de eerste try. Als de duur voor het volt ooien van de eerste validatie controle 1 minuut is en de bewerking is mislukt, is de volgende nieuwe poging om 1:00 + 1 min (duur) + 1 min (interval voor opnieuw proberen) = 1:02 PM. <br/><br/>Voor segmenten in het verleden is er geen vertraging. De nieuwe poging vindt direct plaats. |No |00:01:00 (1 minuut) |
| retryTimeout |De time-out voor elke nieuwe poging.<br/><br/>Als deze eigenschap is ingesteld op 10 minuten, moet de validatie binnen tien minuten worden voltooid. Als het langer dan tien minuten duurt om de validatie uit te voeren, treedt er een time-out op voor de poging.<br/><br/>Als alle pogingen voor de validatie een time-out hebben, wordt het segment gemarkeerd als out. |No |00:10:00 (10 minuten) |
| maximumRetry |Aantal keer dat er moet worden gecontroleerd op de beschik baarheid van de externe gegevens. De toegestane maximum waarde is 10. |No |3 |


## <a name="data-stores"></a>GEGEVENS ARCHIEVEN
De sectie [gekoppelde service](#linked-service) heeft beschrijvingen gegeven voor json-elementen die gemeen schappelijk zijn voor alle typen gekoppelde services. Deze sectie bevat informatie over de JSON-elementen die specifiek zijn voor elk gegevens archief.

De sectie [gegevensset](#dataset) heeft beschrijvingen gegeven voor json-elementen die gemeen schappelijk zijn voor alle typen gegevens sets. Deze sectie bevat informatie over de JSON-elementen die specifiek zijn voor elk gegevens archief.

In het gedeelte [activiteit](#activity) worden beschrijvingen voor json-elementen beschreven die gemeen schappelijk zijn voor alle typen activiteiten. Deze sectie bevat informatie over de JSON-elementen die specifiek zijn voor elk gegevens archief wanneer het wordt gebruikt als bron/sink in een Kopieer activiteit.

Klik op de koppeling voor de winkel waarin u bent geïnteresseerd om de JSON-schema's voor de gekoppelde service, de gegevensset en de bron/Sink voor de Kopieer activiteit te bekijken.

| Categorie | Gegevensarchief
|:--- |:--- |
| **Azure** |[Azure Blob Storage](#azure-blob-storage) |
| &nbsp; |Azure Data Lake Store |
| &nbsp; |[Azure Cosmos DB](#azure-cosmos-db) |
| &nbsp; |[Azure SQL Database](#azure-sql-database) |
| &nbsp; |[Azure Synapse Analytics](#azure-synapse-analytics) |
| &nbsp; |[Azure Cognitive Search](#azure-cognitive-search) |
| &nbsp; |[Azure Table storage](#azure-table-storage) |
| **Databases** |[Amazon Redshift](#amazon-redshift) |
| &nbsp; |[IBM DB2](#ibm-db2) |
| &nbsp; |[MySQL](#mysql) |
| &nbsp; |[Oracle](#oracle) |
| &nbsp; |[PostgreSQL](#postgresql) |
| &nbsp; |[SAP Business Warehouse](#sap-business-warehouse) |
| &nbsp; |[SAP HANA](#sap-hana) |
| &nbsp; |[SQL Server](#sql-server) |
| &nbsp; |[Sybase](#sybase) |
| &nbsp; |[Teradata](#teradata) |
| **NoSQL** |[Cassandra](#cassandra) |
| &nbsp; |[MongoDB](#mongodb) |
| **File** |[Amazon S3](#amazon-s3) |
| &nbsp; |[Bestands systeem](#file-system) |
| &nbsp; |[FTP](#ftp) |
| &nbsp; |[HDFS](#hdfs) |
| &nbsp; |[SFTP](#sftp) |
| **Overige** |[HTTP](#http) |
| &nbsp; |[OData](#odata) |
| &nbsp; |[ODBC](#odbc) |
| &nbsp; |[Salesforce](#salesforce) |
| &nbsp; |Webtabel |

## <a name="azure-blob-storage"></a>Azure Blob Storage

### <a name="linked-service"></a>Gekoppelde service
Er zijn twee soorten gekoppelde services: Azure Storage gekoppelde service en Azure Storage SAS gekoppelde service.

#### <a name="azure-storage-linked-service"></a>Een gekoppelde Azure Storage-service
Als u uw Azure Storage-account wilt koppelen aan een data factory met behulp van de **account sleutel**, maakt u een Azure Storage gekoppelde service. Als u een Azure Storage gekoppelde service wilt definiëren, stelt u het **type** van de gekoppelde service in op **opslag**. Vervolgens kunt u de volgende eigenschappen opgeven in de sectie **typeProperties** :

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| connectionString |Geef de gegevens op die nodig zijn om verbinding te maken met Azure Storage voor de connectionString-eigenschap. |Ja |

##### <a name="example"></a>Voorbeeld

```json
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
```

#### <a name="azure-storage-sas-linked-service"></a>Azure Storage SAS-gekoppelde service
Met de gekoppelde Azure Storage SAS-service kunt u een Azure Storage-account koppelen aan een Azure-data factory met behulp van een Shared Access Signature (SAS). Het biedt de data factory met beperkte/tijdgebonden toegang tot alle/specifieke resources (blob/container) in de opslag. Als u uw Azure Storage-account wilt koppelen aan een data factory met behulp van Shared Access Signature, maakt u een gekoppelde Azure Storage SAS-service. Als u een gekoppelde Azure Storage SAS-service wilt definiëren, stelt u het **type** van de gekoppelde service in op **azurestoragesas zijn**. Vervolgens kunt u de volgende eigenschappen opgeven in de sectie **typeProperties** :

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| sasUri |Geef Shared Access Signature-URI op voor de Azure Storage-resources, zoals blob, container of tabel. |Ja |

##### <a name="example"></a>Voorbeeld

```json
{
    "name": "StorageSasLinkedService",
    "properties": {
        "type": "AzureStorageSas",
        "typeProperties": {
            "sasUri": "<storageUri>?<sasToken>"
        }
    }
}
```

Zie het artikel over [Azure Blob Storage-connector](data-factory-azure-blob-connector.md#linked-service-properties) voor meer informatie over deze gekoppelde services.

### <a name="dataset"></a>Gegevensset
Als u een Azure Blob-gegevensset wilt definiëren, stelt u het **type** van de gegevensset in op **AzureBlob**. Geef vervolgens de volgende specifieke Azure Blob-eigenschappen op in de sectie **typeProperties** :

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| folderPath |Pad naar de container en de map in de Blob-opslag. Voor beeld: myblobcontainer\myblobfolder\ |Yes |
| fileName |De naam van de blob. Bestands naam is optioneel en hoofdletter gevoelig.<br/><br/>Als u een bestands naam opgeeft, werkt de activiteit (inclusief kopie) voor de specifieke blob.<br/><br/>Als er geen bestands naam is opgegeven, wordt met Copy alle blobs in de folderPath voor invoer gegevensset opgenomen.<br/><br/>Als er geen bestands naam is opgegeven voor een uitvoer gegevensset, zou de naam van het gegenereerde bestand de volgende indeling hebben: `Data.<Guid>.txt` (bijvoorbeeld:: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |No |
| partitionedBy |partitionedBy is een optionele eigenschap. U kunt deze gebruiken om een dynamische folderPath en een bestands naam op te geven voor time series-gegevens. Zo kan folderPath voor elk uur aan gegevens worden para meters. |No |
| indeling | De volgende indelings typen worden ondersteund: **TextFormat**, **JsonFormat**, **Avro Format**, **OrcFormat**, **ParquetFormat**. Stel de eigenschap **type** onder indeling in op een van deze waarden. Zie voor meer informatie secties [tekst indeling](data-factory-supported-file-and-compression-formats.md#text-format), [JSON-indeling](data-factory-supported-file-and-compression-formats.md#json-format), [Avro](data-factory-supported-file-and-compression-formats.md#avro-format)-indeling, [Orc-indeling](data-factory-supported-file-and-compression-formats.md#orc-format)en Parquet- [indeling](data-factory-supported-file-and-compression-formats.md#parquet-format) . <br><br> Als u bestanden wilt **kopiëren als-zich bevindt** tussen archieven op basis van bestanden (binaire kopie), slaat u de sectie indeling in de gegevensset voor invoer en uitvoer over. |No |
| compressie | Geef het type en compressie niveau voor de gegevens op. Ondersteunde typen zijn: **gzip**, **Deflate**, **bzip2** en **ZipDeflate**. Ondersteunde niveaus zijn: **optimaal** en **snelst**. Zie [Bestands-en compressie-indelingen in azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support)voor meer informatie. |No |

#### <a name="example"></a>Voorbeeld

```json
{
    "name": "AzureBlobInput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "fileName": "input.log",
            "folderPath": "adfgetstarted/inputdata",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability": {
            "frequency": "Month",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}
 ```


Zie het artikel over [Azure Blob-connector](data-factory-azure-blob-connector.md#dataset-properties) voor meer informatie.

### <a name="blobsource-in-copy-activity"></a>BlobSource in Kopieer activiteit
Als u gegevens uit een Azure-Blob Storage kopieert, stelt u het **bron type** van de Kopieer activiteit in op **BlobSource** en geeft u de volgende eigenschappen op in de sectie **bron** :

| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| recursieve |Geeft aan of de gegevens recursief worden gelezen uit de submappen of alleen vanuit de opgegeven map. |True (standaard waarde), False |No |

#### <a name="example-blobsource"></a>Voor beeld: **BlobSource**
```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoSQL",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": "AzureSqlOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "BlobSource"
                },
                "sink": {
                    "type": "SqlSink"
                }
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```
### <a name="blobsink-in-copy-activity"></a>BlobSink in Kopieer activiteit
Als u gegevens kopieert naar een Azure-Blob Storage, stelt u het **sink-type** van de Kopieer activiteit in op **BlobSink** en geeft u de volgende eigenschappen op in de sectie **sink** :

| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| copyBehavior |Hiermee wordt het Kopieer gedrag gedefinieerd wanneer de bron BlobSource of File System is. |<b>PreserveHierarchy</b>: behoudt de bestands hiërarchie in de doelmap. Het relatieve pad van het bron bestand naar de bronmap is identiek aan het relatieve pad van het doel bestand naar de doelmap.<br/><br/><b>FlattenHierarchy</b>: alle bestanden in de bronmap bevinden zich in het eerste niveau van de doelmap. De doel bestanden hebben een automatisch gegenereerde naam. <br/><br/><b>MergeFiles (standaard):</b> alle bestanden van de bronmap worden samengevoegd met één bestand. Als de naam van het bestand of de blob is opgegeven, is de naam van het samengevoegde bestand de opgegeven naam. anders wordt de bestands naam automatisch gegenereerd. |No |

#### <a name="example-blobsink"></a>Voor beeld: BlobSink

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "AzureSQLtoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureSQLInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "SqlSource",
                    "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Zie het artikel over [Azure Blob-connector](data-factory-azure-blob-connector.md#copy-activity-properties) voor meer informatie.

## <a name="azure-data-lake-store"></a>Azure Data Lake Store

### <a name="linked-service"></a>Gekoppelde service
Als u een Azure Data Lake Store gekoppelde service wilt definiëren, stelt u het type van de gekoppelde service in op **AzureDataLakeStore** en geeft u de volgende eigenschappen op in de sectie **typeProperties** :

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type moet worden ingesteld op: **AzureDataLakeStore** | Yes |
| dataLakeStoreUri | Geef informatie op over het Azure Data Lake Store-account. Deze heeft de volgende indeling: `https://[accountname].azuredatalakestore.net/webhdfs/v1` of `adl://[accountname].azuredatalakestore.net/` . | Yes |
| subscriptionId | De Azure-abonnements-ID waartoe Data Lake Store behoort. | Vereist voor Sink |
| resourceGroupName | De naam van de Azure-resource groep waartoe Data Lake Store behoort. | Vereist voor Sink |
| servicePrincipalId | Geef de client-ID van de toepassing op. | Ja (voor Service-Principal-verificatie) |
| servicePrincipalKey | Geef de sleutel van de toepassing op. | Ja (voor Service-Principal-verificatie) |
| tenant | Geef de Tenant gegevens op (domein naam of Tenant-ID) waaronder uw toepassing zich bevindt. U kunt deze ophalen door de muis in de rechter bovenhoek van de Azure Portal aan te wijzen. | Ja (voor Service-Principal-verificatie) |
| autorisatie | Klik op de knop **machtigen** in de **Data Factory editor** en voer uw referenties in waarmee de automatisch GEGENEREERDe autorisatie-URL wordt toegewezen aan deze eigenschap. | Ja (voor verificatie van de gebruikers referenties)|
| sessionId | OAuth-sessie-ID van de OAuth-autorisatie sessie. Elke sessie-ID is uniek en kan slechts één keer worden gebruikt. Deze instelling wordt automatisch gegenereerd wanneer u Data Factory editor gebruikt. | Ja (voor verificatie van de gebruikers referenties) |

#### <a name="example-using-service-principal-authentication"></a>Voor beeld: Service-Principal-verificatie gebruiken
```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info. Example: microsoft.onmicrosoft.com>"
        }
    }
}
```

#### <a name="example-using-user-credential-authentication"></a>Voor beeld: verificatie van gebruikers referenties gebruiken
```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "sessionId": "<session ID>",
            "authorization": "<authorization URL>",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        }
    }
}
```

Zie [Azure data Lake Store connector](data-factory-azure-datalake-connector.md#linked-service-properties) -artikel voor meer informatie.

### <a name="dataset"></a>Gegevensset
Als u een Azure Data Lake Store gegevensset wilt definiëren, stelt u het **type** van de gegevensset in op **AzureDataLakeStore** en geeft u de volgende eigenschappen op in de sectie **typeProperties** :

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| folderPath |Het pad naar de container en de map in het Azure Data Lake-archief. |Yes |
| fileName |De naam van het bestand in het Azure Data Lake-archief. Bestands naam is optioneel en hoofdletter gevoelig. <br/><br/>Als u een bestands naam opgeeft, werkt de activiteit (inclusief kopie) voor het specifieke bestand.<br/><br/>Als er geen bestands naam is opgegeven, kopiëren bevat alle bestanden in de folderPath voor invoer gegevensset.<br/><br/>Als er geen bestands naam is opgegeven voor een uitvoer gegevensset, zou de naam van het gegenereerde bestand de volgende indeling hebben: `Data.<Guid>.txt` (bijvoorbeeld:: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |No |
| partitionedBy |partitionedBy is een optionele eigenschap. U kunt deze gebruiken om een dynamische folderPath en een bestands naam op te geven voor time series-gegevens. Zo kan folderPath voor elk uur aan gegevens worden para meters. |No |
| indeling | De volgende indelings typen worden ondersteund: **TextFormat**, **JsonFormat**, **Avro Format**, **OrcFormat**, **ParquetFormat**. Stel de eigenschap **type** onder indeling in op een van deze waarden. Zie voor meer informatie secties [tekst indeling](data-factory-supported-file-and-compression-formats.md#text-format), [JSON-indeling](data-factory-supported-file-and-compression-formats.md#json-format), [Avro](data-factory-supported-file-and-compression-formats.md#avro-format)-indeling, [Orc-indeling](data-factory-supported-file-and-compression-formats.md#orc-format)en Parquet- [indeling](data-factory-supported-file-and-compression-formats.md#parquet-format) . <br><br> Als u bestanden wilt **kopiëren als-zich bevindt** tussen archieven op basis van bestanden (binaire kopie), slaat u de sectie indeling in de gegevensset voor invoer en uitvoer over. |No |
| compressie | Geef het type en compressie niveau voor de gegevens op. Ondersteunde typen zijn: **gzip**, **Deflate**, **bzip2** en **ZipDeflate**. Ondersteunde niveaus zijn: **optimaal** en **snelst**. Zie [Bestands-en compressie-indelingen in azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support)voor meer informatie. |No |

#### <a name="example"></a>Voorbeeld
```json
{
    "name": "AzureDataLakeStoreInput",
    "properties": {
        "type": "AzureDataLakeStore",
        "linkedServiceName": "AzureDataLakeStoreLinkedService",
        "typeProperties": {
            "folderPath": "datalake/input/",
            "fileName": "SearchLog.tsv",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            }
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Zie [Azure data Lake Store connector](data-factory-azure-datalake-connector.md#dataset-properties) -artikel voor meer informatie.

### <a name="azure-data-lake-store-source-in-copy-activity"></a>Azure Data Lake Store bron in Kopieer activiteit
Als u gegevens uit een Azure Data Lake Store kopieert, stelt u het **bron type** van de Kopieer activiteit in op **AzureDataLakeStoreSource** en geeft u de volgende eigenschappen op in de sectie **bron** :

**AzureDataLakeStoreSource** ondersteunt de volgende eigenschappen **typeProperties** sectie:

| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| recursieve |Geeft aan of de gegevens recursief worden gelezen uit de submappen of alleen vanuit de opgegeven map. |True (standaard waarde), False |No |

#### <a name="example-azuredatalakestoresource"></a>Voor beeld: AzureDataLakeStoreSource

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "AzureDakeLaketoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureDataLakeStoreInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "AzureDataLakeStoreSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Zie [Azure data Lake Store connector](data-factory-azure-datalake-connector.md#copy-activity-properties) -artikel voor meer informatie.

### <a name="azure-data-lake-store-sink-in-copy-activity"></a>Sink Azure Data Lake Store in Kopieer activiteit
Als u gegevens naar een Azure Data Lake Store kopieert, stelt u het **sink-type** van de Kopieer activiteit in op **AzureDataLakeStoreSink** en geeft u de volgende eigenschappen op in de sectie **sink** :

| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| copyBehavior |Hiermee geeft u het Kopieer gedrag op. |<b>PreserveHierarchy</b>: behoudt de bestands hiërarchie in de doelmap. Het relatieve pad van het bron bestand naar de bronmap is identiek aan het relatieve pad van het doel bestand naar de doelmap.<br/><br/><b>FlattenHierarchy</b>: alle bestanden in de bronmap worden gemaakt in het eerste niveau van de doelmap. De doel bestanden worden gemaakt met een automatisch gegenereerde naam.<br/><br/><b>MergeFiles</b>: alle bestanden van de bronmap worden samengevoegd met één bestand. Als de naam van het bestand of de blob is opgegeven, is de naam van het samengevoegde bestand de opgegeven naam. anders wordt de bestands naam automatisch gegenereerd. |No |

#### <a name="example-azuredatalakestoresink"></a>Voor beeld: AzureDataLakeStoreSink
```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoDataLake",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": "AzureDataLakeStoreOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "BlobSource"
                },
                "sink": {
                    "type": "AzureDataLakeStoreSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Zie [Azure data Lake Store connector](data-factory-azure-datalake-connector.md#copy-activity-properties) -artikel voor meer informatie.

## <a name="azure-cosmos-db"></a>Azure Cosmos DB

### <a name="linked-service"></a>Gekoppelde service
Als u een Azure Cosmos DB gekoppelde service wilt definiëren, stelt u het **type** van de gekoppelde service in op **DocumentDb** en geeft u de volgende eigenschappen op in de sectie **typeProperties** :

| **Eigenschap** | **Beschrijving** | **Vereist** |
| --- | --- | --- |
| connectionString |Geef de gegevens op die nodig zijn om verbinding te maken met Azure Cosmos DB Data Base. |Yes |

#### <a name="example"></a>Voorbeeld

```json
{
    "name": "CosmosDBLinkedService",
    "properties": {
        "type": "DocumentDb",
        "typeProperties": {
            "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
        }
    }
}
```
Zie [Azure Cosmos DB connector](data-factory-azure-documentdb-connector.md#linked-service-properties) -artikel voor meer informatie.

### <a name="dataset"></a>Gegevensset
Als u een Azure Cosmos DB gegevensset wilt definiëren, stelt u het **type** van de gegevensset in op **DocumentDbCollection** en geeft u de volgende eigenschappen op in de sectie **typeProperties** :

| **Eigenschap** | **Beschrijving** | **Vereist** |
| --- | --- | --- |
| collectionName |De naam van de verzameling Azure Cosmos DB. |Yes |

#### <a name="example"></a>Voorbeeld

```json
{
    "name": "PersonCosmosDBTable",
    "properties": {
        "type": "DocumentDbCollection",
        "linkedServiceName": "CosmosDBLinkedService",
        "typeProperties": {
            "collectionName": "Person"
        },
        "external": true,
        "availability": {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```
Zie [Azure Cosmos DB connector](data-factory-azure-documentdb-connector.md#dataset-properties) -artikel voor meer informatie.

### <a name="azure-cosmos-db-collection-source-in-copy-activity"></a>Bron van de verzameling Azure Cosmos DB in de Kopieer activiteit
Als u gegevens uit een Azure Cosmos DB kopieert, stelt u het **bron type** van de Kopieer activiteit in op **DocumentDbCollectionSource** en geeft u de volgende eigenschappen op in de sectie **bron** :


| **Eigenschap** | **Beschrijving** | **Toegestane waarden** | **Vereist** |
| --- | --- | --- | --- |
| query |Geef de query op om gegevens te lezen. |De query teken reeks die wordt ondersteund door Azure Cosmos DB. <br/><br/>Voorbeeld: `SELECT c.BusinessEntityID, c.PersonType, c.NameStyle, c.Title, c.Name.First AS FirstName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |No <br/><br/>Indien niet opgegeven, de SQL-instructie die wordt uitgevoerd: `select <columns defined in structure> from mycollection` |
| nestingSeparator |Speciaal teken om aan te geven dat het document is genest |Wille keurig teken. <br/><br/>Azure Cosmos DB is een NoSQL-Archief voor JSON-documenten, waarbij geneste structuren zijn toegestaan. Azure Data Factory stelt de gebruiker in staat om de hiërarchie aan te duiden via nestingSeparator, dat wil zeggen. in de bovenstaande voor beelden. Met het scheidings teken genereert de Kopieer activiteit het object ' name ' met drie onderliggende elementen eerst, middelste en laatste, volgens ' naam. First ', ' naam. midden ' en ' naam. last ' in de tabel definitie. |No |

#### <a name="example"></a>Voorbeeld

```json
{
    "name": "DocDbToBlobPipeline",
    "properties": {
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "DocumentDbCollectionSource",
                    "query": "SELECT Person.Id, Person.Name.First AS FirstName, Person.Name.Middle as MiddleName, Person.Name.Last AS LastName FROM Person",
                    "nestingSeparator": "."
                },
                "sink": {
                    "type": "BlobSink",
                    "blobWriterAddHeader": true,
                    "writeBatchSize": 1000,
                    "writeBatchTimeout": "00:00:59"
                }
            },
            "inputs": [{
                "name": "PersonCosmosDBTable"
            }],
            "outputs": [{
                "name": "PersonBlobTableOut"
            }],
            "policy": {
                "concurrency": 1
            },
            "name": "CopyFromCosmosDbToBlob"
        }],
        "start": "2016-04-01T00:00:00",
        "end": "2016-04-02T00:00:00"
    }
}
```

### <a name="azure-cosmos-db-collection-sink-in-copy-activity"></a>Verzamelings Sink Azure Cosmos DB in Kopieer activiteit
Als u gegevens naar Azure Cosmos DB kopieert, stelt u het **sink-type** van de Kopieer activiteit in op **DocumentDbCollectionSink** en geeft u de volgende eigenschappen op in de sectie **sink** :

| **Eigenschap** | **Beschrijving** | **Toegestane waarden** | **Vereist** |
| --- | --- | --- | --- |
| nestingSeparator |Een speciaal teken in de naam van de bron kolom om aan te geven dat genest document nodig is. <br/><br/>Voor beeld: `Name.First` in de uitvoer tabel levert de volgende JSON-structuur in het Cosmos DB document:<br/><br/>"Naam": {<br/>    "Eerste": "John"<br/>}, |Teken dat wordt gebruikt voor het scheiden van geneste niveaus.<br/><br/>De standaard waarde is `.` (dot). |Teken dat wordt gebruikt voor het scheiden van geneste niveaus. <br/><br/>De standaard waarde is `.` (dot). |
| writeBatchSize |Aantal parallelle aanvragen voor het Azure Cosmos DB-service om documenten te maken.<br/><br/>U kunt de prestaties afstemmen bij het kopiëren van gegevens naar/van Azure Cosmos DB met behulp van deze eigenschap. U kunt een betere prestaties verwachten wanneer u writeBatchSize verhoogt, omdat er meer parallelle aanvragen naar Azure Cosmos DB worden verzonden. U moet er echter voor zorgen dat het fout bericht niet kan worden vertraagd: ' de aanvraag snelheid is groot '.<br/><br/>Beperking wordt bepaald door een aantal factoren, zoals de grootte van documenten, het aantal voor waarden in documenten, het indexerings beleid van de doel verzameling, enzovoort. Voor kopieer bewerkingen kunt u een betere verzameling (bijvoorbeeld S3) gebruiken om de meeste door Voer beschikbaar te hebben (2.500 aanvraag eenheden per seconde). |Geheel getal |Nee (standaard: 5) |
| writeBatchTimeout |Wacht tijd voordat de bewerking is voltooid voordat er een time-out optreedt. |tijdsbestek<br/><br/> Voor beeld: "00:30:00" (30 minuten). |No |

#### <a name="example"></a>Voorbeeld

```json
{
    "name": "BlobToDocDbPipeline",
    "properties": {
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "BlobSource"
                },
                "sink": {
                    "type": "DocumentDbCollectionSink",
                    "nestingSeparator": ".",
                    "writeBatchSize": 2,
                    "writeBatchTimeout": "00:00:00"
                },
                "translator": {
                    "type": "TabularTranslator",
                    "ColumnMappings": "FirstName: Name.First, MiddleName: Name.Middle, LastName: Name.Last, BusinessEntityID: BusinessEntityID, PersonType: PersonType, NameStyle: NameStyle, Title: Title, Suffix: Suffix"
                }
            },
            "inputs": [{
                "name": "PersonBlobTableIn"
            }],
            "outputs": [{
                "name": "PersonCosmosDbTableOut"
            }],
            "policy": {
                "concurrency": 1
            },
            "name": "CopyFromBlobToCosmosDb"
        }],
        "start": "2016-04-14T00:00:00",
        "end": "2016-04-15T00:00:00"
    }
}
```

Zie [Azure Cosmos DB connector](data-factory-azure-documentdb-connector.md#copy-activity-properties) -artikel voor meer informatie.

## <a name="azure-sql-database"></a>Azure SQL Database

### <a name="linked-service"></a>Gekoppelde service
Als u een Azure SQL Database gekoppelde service wilt definiëren, stelt u het **type** van de gekoppelde service in op **AzureSqlDatabase** en geeft u de volgende eigenschappen op in de sectie **typeProperties** :

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| connectionString |Geef de gegevens op die nodig zijn om verbinding te maken met het Azure SQL Database-exemplaar voor de Connections Tring-eigenschap. |Yes |

#### <a name="example"></a>Voorbeeld
```json
{
    "name": "AzureSqlLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        }
    }
}
```

Zie het artikel over [Azure SQL-connector](data-factory-azure-sql-connector.md#linked-service-properties) voor meer informatie.

### <a name="dataset"></a>Gegevensset
Als u een Azure SQL Database gegevensset wilt definiëren, stelt u het **type** van de gegevensset in op **AzureSqlTable** en geeft u de volgende eigenschappen op in de sectie **typeProperties** :

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| tableName |De naam van de tabel of weer gave in het Azure SQL Database exemplaar waarnaar de gekoppelde service verwijst. |Yes |

#### <a name="example"></a>Voorbeeld

```json
{
    "name": "AzureSqlInput",
    "properties": {
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```
Zie het artikel over [Azure SQL-connector](data-factory-azure-sql-connector.md#dataset-properties) voor meer informatie.

### <a name="sql-source-in-copy-activity"></a>SQL-bron in Kopieer activiteit
Als u gegevens uit een Azure SQL Database kopieert, stelt u het **bron type** van de Kopieer activiteit in op **SqlSource** en geeft u de volgende eigenschappen op in de sectie **bron** :


| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| sqlReaderQuery |Gebruik de aangepaste query om gegevens te lezen. |SQL-query teken reeks. Bijvoorbeeld: `select * from MyTable`. |No |
| sqlReaderStoredProcedureName |De naam van de opgeslagen procedure waarmee gegevens uit de bron tabel worden gelezen. |De naam van de opgeslagen procedure. |No |
| storedProcedureParameters |Para meters voor de opgeslagen procedure. |Naam/waarde-paren. Namen en hoofdletter gebruik van para meters moeten overeenkomen met de namen en de behuizing van de opgeslagen procedure parameters. |No |

#### <a name="example"></a>Voorbeeld

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "AzureSQLtoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureSQLInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "SqlSource",
                    "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```
Zie het artikel over [Azure SQL-connector](data-factory-azure-sql-connector.md#copy-activity-properties) voor meer informatie.

### <a name="sql-sink-in-copy-activity"></a>SQL-sink in Kopieer activiteit
Als u gegevens naar Azure SQL Database kopieert, stelt u het **sink-type** van de Kopieer activiteit in op **SqlSink** en geeft u de volgende eigenschappen op in de sectie **sink** :

| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| writeBatchTimeout |Wacht tijd voordat de batch INSERT-bewerking is voltooid voordat er een time-out optreedt. |tijdsbestek<br/><br/> Voor beeld: "00:30:00" (30 minuten). |No |
| writeBatchSize |Hiermee worden gegevens in de SQL-tabel ingevoegd wanneer de buffer grootte writeBatchSize bereikt. |Geheel getal (aantal rijen) |Nee (standaard: 10000) |
| sqlWriterCleanupScript |Geef een query op voor de Kopieer activiteit die moet worden uitgevoerd, zodat de gegevens van een specifiek segment worden opgeruimd. |Een query-instructie. |No |
| sliceIdentifierColumnName |Geef een kolom naam op voor de Kopieer activiteit die moet worden gevuld met een automatisch gegenereerde segment-id, die wordt gebruikt om gegevens van een specifiek segment op te schonen wanneer het opnieuw wordt uitgevoerd. |Kolom naam van een kolom met het gegevens type binary (32). |No |
| sqlWriterStoredProcedureName |De naam van de opgeslagen procedure waarmee gegevens in de doel tabel worden upsert (bijgewerkt/ingevoegd). |De naam van de opgeslagen procedure. |No |
| storedProcedureParameters |Para meters voor de opgeslagen procedure. |Naam/waarde-paren. Namen en hoofdletter gebruik van para meters moeten overeenkomen met de namen en de behuizing van de opgeslagen procedure parameters. |No |
| sqlWriterTableType |Geef een naam op voor het tabel type dat moet worden gebruikt in de opgeslagen procedure. Als u de Kopieer activiteit uitvoert, worden de gegevens die in een tijdelijke tabel worden verplaatst, met dit tabel type beschikbaar. De opgeslagen procedure code kan vervolgens de gegevens samen voegen die worden gekopieerd met bestaande gegevens. |De naam van een tabel type. |No |

#### <a name="example"></a>Voorbeeld

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoSQL",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": "AzureSqlOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "BlobSource",
                    "blobColumnSeparators": ","
                },
                "sink": {
                    "type": "SqlSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Zie het artikel over [Azure SQL-connector](data-factory-azure-sql-connector.md#copy-activity-properties) voor meer informatie.

## <a name="azure-synapse-analytics"></a>Azure Synapse Analytics

### <a name="linked-service"></a>Gekoppelde service
Als u een gekoppelde Azure Synapse Analytics-service wilt definiëren, stelt u het **type** van de gekoppelde service in op **AzureSqlDW** en geeft u de volgende eigenschappen op in de sectie **typeProperties** :

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| connectionString |Geef de gegevens op die nodig zijn om verbinding te maken met het Azure Synapse Analytics-exemplaar voor de Connections Tring-eigenschap. |Yes |



#### <a name="example"></a>Voorbeeld

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        }
    }
}
```

Zie het artikel over [Azure Synapse Analytics-connector](data-factory-azure-sql-data-warehouse-connector.md#linked-service-properties) voor meer informatie.

### <a name="dataset"></a>Gegevensset
Als u een Azure Synapse Analytics-gegevensset wilt definiëren, stelt u het **type** van de gegevensset in op **AzureSqlDWTable** en geeft u de volgende eigenschappen op in de sectie **typeProperties** :

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| tableName |De naam van de tabel of weer gave in de Azure Synapse Analytics-Data Base waarnaar de gekoppelde service verwijst. |Yes |

#### <a name="example"></a>Voorbeeld

```json
{
    "name": "AzureSqlDWInput",
    "properties": {
    "type": "AzureSqlDWTable",
        "linkedServiceName": "AzureSqlDWLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Zie het artikel over [Azure Synapse Analytics-connector](data-factory-azure-sql-data-warehouse-connector.md#dataset-properties) voor meer informatie.

### <a name="azure-synapse-analytics-source-in-copy-activity"></a>Azure Synapse Analytics-bron in Kopieer activiteit
Als u gegevens uit Azure Synapse Analytics kopieert, stelt u het **bron type** van de Kopieer activiteit in op **SqlDWSource** en geeft u de volgende eigenschappen op in de sectie **bron** :


| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| sqlReaderQuery |Gebruik de aangepaste query om gegevens te lezen. |SQL-query teken reeks. Bijvoorbeeld: `select * from MyTable`. |No |
| sqlReaderStoredProcedureName |De naam van de opgeslagen procedure waarmee gegevens uit de bron tabel worden gelezen. |De naam van de opgeslagen procedure. |No |
| storedProcedureParameters |Para meters voor de opgeslagen procedure. |Naam/waarde-paren. Namen en hoofdletter gebruik van para meters moeten overeenkomen met de namen en de behuizing van de opgeslagen procedure parameters. |No |

#### <a name="example"></a>Voorbeeld

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "AzureSQLDWtoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureSqlDWInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "SqlDWSource",
                    "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Zie het artikel over [Azure Synapse Analytics-connector](data-factory-azure-sql-data-warehouse-connector.md#copy-activity-properties) voor meer informatie.

### <a name="azure-synapse-analytics-sink-in-copy-activity"></a>Azure Synapse Analytics-sink in Kopieer activiteit
Als u gegevens naar Azure Synapse Analytics kopieert, stelt u het **sink-type** van de Kopieer activiteit in op **SqlDWSink** en geeft u de volgende eigenschappen op in de sectie **sink** :

| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| sqlWriterCleanupScript |Geef een query op voor de Kopieer activiteit die moet worden uitgevoerd, zodat de gegevens van een specifiek segment worden opgeruimd. |Een query-instructie. |No |
| allowPolyBase |Hiermee wordt aangegeven of poly base (indien van toepassing) moet worden gebruikt in plaats van BULKINSERT-mechanisme. <br/><br/> **Het gebruik van poly Base is de aanbevolen manier om gegevens in azure Synapse Analytics te laden.** |Waar <br/>False (standaard) |No |
| polyBaseSettings |Een groep eigenschappen die kan worden opgegeven wanneer de eigenschap **allowPolybase** is ingesteld op **True**. |&nbsp; |No |
| rejectValue |Hiermee geeft u het aantal of percentage rijen op dat kan worden afgewezen voordat de query mislukt. <br/><br/>Meer informatie over de opties voor het afwijzen van poly bases vindt u in de sectie **argumenten** van het onderwerp [externe tabel maken (Transact-SQL)](/sql/t-sql/statements/create-external-table-transact-sql) . |0 (standaard), 1, 2,... |No |
| rejectType |Hiermee wordt aangegeven of de optie rejectValue is opgegeven als een letterlijke waarde of een percentage. |Waarde (standaard), percentage |No |
| rejectSampleValue |Bepaalt het aantal rijen dat moet worden opgehaald voordat de poly base het percentage geweigerde rijen opnieuw berekent. |1, 2,... |Ja, als **rejectType** een **percentage** is |
| useTypeDefault |Hiermee geeft u op hoe ontbrekende waarden in tekst bestanden met scheidings tekens moeten worden verwerkt wanneer poly base gegevens ophaalt uit het tekst bestand.<br/><br/>Meer informatie over deze eigenschap vindt u in de sectie argumenten in [externe BESTANDS indeling maken (Transact-SQL)](/sql/t-sql/statements/create-external-file-format-transact-sql). |True, False (standaard) |No |
| writeBatchSize |Hiermee worden gegevens in de SQL-tabel ingevoegd wanneer de buffer grootte writeBatchSize bereikt |Geheel getal (aantal rijen) |Nee (standaard: 10000) |
| writeBatchTimeout |Wacht tijd voordat de batch INSERT-bewerking is voltooid voordat er een time-out optreedt. |tijdsbestek<br/><br/> Voor beeld: "00:30:00" (30 minuten). |No |

#### <a name="example"></a>Voorbeeld

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoSQLDW",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": "AzureSqlDWOutput"
            }],
            "typeProperties": {
                "source": {
                "type": "BlobSource",
                    "blobColumnSeparators": ","
                },
                "sink": {
                    "type": "SqlDWSink",
                    "allowPolyBase": true
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Zie het artikel over [Azure Synapse Analytics-connector](data-factory-azure-sql-data-warehouse-connector.md#copy-activity-properties) voor meer informatie.

## <a name="azure-cognitive-search"></a>Azure Cognitive Search

### <a name="linked-service"></a>Gekoppelde service
Als u een gekoppelde Azure Cognitive Search-service wilt definiëren, stelt u het **type** van de gekoppelde service in op **AzureSearch** en geeft u de volgende eigenschappen op in de sectie **typeProperties** :

| Eigenschap | Beschrijving | Vereist |
| -------- | ----------- | -------- |
| URL | URL voor de zoek service. | Yes |
| sleutel | De beheerders sleutel voor de zoek service. | Yes |

#### <a name="example"></a>Voorbeeld

```json
{
    "name": "AzureSearchLinkedService",
    "properties": {
        "type": "AzureSearch",
        "typeProperties": {
            "url": "https://<service>.search.windows.net",
            "key": "<AdminKey>"
        }
    }
}
```

Zie het artikel over [Azure Cognitive Search-connector](data-factory-azure-search-connector.md#linked-service-properties) voor meer informatie.

### <a name="dataset"></a>Gegevensset
Als u een Azure Cognitive Search-gegevensset wilt definiëren, stelt u het **type** van de gegevensset in op **AzureSearchIndex** en geeft u de volgende eigenschappen op in de sectie **typeProperties** :

| Eigenschap | Beschrijving | Vereist |
| -------- | ----------- | -------- |
| type | De eigenschap type moet worden ingesteld op **AzureSearchIndex**.| Yes |
| indexName | De naam van de zoek index. Data Factory maakt de index niet. De index moet bestaan in azure Cognitive Search. | Yes |

#### <a name="example"></a>Voorbeeld

```json
{
    "name": "AzureSearchIndexDataset",
    "properties": {
        "type": "AzureSearchIndex",
        "linkedServiceName": "AzureSearchLinkedService",
        "typeProperties": {
            "indexName": "products"
        },
        "availability": {
            "frequency": "Minute",
            "interval": 15
        }
    }
}
```

Zie het artikel over [Azure Cognitive Search-connector](data-factory-azure-search-connector.md#dataset-properties) voor meer informatie.

### <a name="azure-cognitive-search-index-sink-in-copy-activity"></a>Index Sink van Azure Cognitive Search in Kopieer activiteit
Als u gegevens naar een zoek index kopieert, stelt u het **sink-type** van de Kopieer activiteit in op **AzureSearchIndexSink** en geeft u de volgende eigenschappen op in de sectie **sink** :

| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| -------- | ----------- | -------------- | -------- |
| WriteBehavior | Hiermee geeft u op of u wilt samen voegen of vervangen wanneer een document al aanwezig is in de index. | Samen voegen (standaard)<br/>Uploaden| No |
| WriteBatchSize | Uploadt gegevens in de zoek index wanneer de buffer grootte writeBatchSize bereikt. | 1 tot en met 1.000. De standaard waarde is 1000. | No |

#### <a name="example"></a>Voorbeeld

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "SqlServertoAzureSearchIndex",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": " SqlServerInput"
            }],
            "outputs": [{
                "name": "AzureSearchIndexDataset"
            }],
            "typeProperties": {
                "source": {
                    "type": "SqlSource",
                    "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "AzureSearchIndexSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Zie het artikel over [Azure Cognitive Search-connector](data-factory-azure-search-connector.md#copy-activity-properties) voor meer informatie.

## <a name="azure-table-storage"></a>Azure-tabelopslag

### <a name="linked-service"></a>Gekoppelde service
Er zijn twee soorten gekoppelde services: Azure Storage gekoppelde service en Azure Storage SAS gekoppelde service.

#### <a name="azure-storage-linked-service"></a>Een gekoppelde Azure Storage-service
Als u uw Azure Storage-account wilt koppelen aan een data factory met behulp van de **account sleutel**, maakt u een Azure Storage gekoppelde service. Als u een Azure Storage gekoppelde service wilt definiëren, stelt u het **type** van de gekoppelde service in op **opslag**. Vervolgens kunt u de volgende eigenschappen opgeven in de sectie **typeProperties** :

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type |Het type eigenschap moet worden ingesteld op: **AzureStorage** |Ja |
| connectionString |Geef de gegevens op die nodig zijn om verbinding te maken met Azure Storage voor de connectionString-eigenschap. |Ja |

**Voorbeeld:**

```json
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
```

#### <a name="azure-storage-sas-linked-service"></a>Azure Storage SAS-gekoppelde service
Met de gekoppelde Azure Storage SAS-service kunt u een Azure Storage-account koppelen aan een Azure-data factory met behulp van een Shared Access Signature (SAS). Het biedt de data factory met beperkte/tijdgebonden toegang tot alle/specifieke resources (blob/container) in de opslag. Als u uw Azure Storage-account wilt koppelen aan een data factory met behulp van Shared Access Signature, maakt u een gekoppelde Azure Storage SAS-service. Als u een gekoppelde Azure Storage SAS-service wilt definiëren, stelt u het **type** van de gekoppelde service in op **azurestoragesas zijn**. Vervolgens kunt u de volgende eigenschappen opgeven in de sectie **typeProperties** :

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type |Het type eigenschap moet worden ingesteld op: **AzureStorageSas** |Ja |
| sasUri |Geef Shared Access Signature-URI op voor de Azure Storage-resources, zoals blob, container of tabel. |Ja |

**Voorbeeld:**

```json
{
    "name": "StorageSasLinkedService",
    "properties": {
        "type": "AzureStorageSas",
        "typeProperties": {
            "sasUri": "<storageUri>?<sasToken>"
        }
    }
}
```

Zie het artikel over [Azure Table Storage-connector](data-factory-azure-table-connector.md#linked-service-properties) voor meer informatie over deze gekoppelde services.

### <a name="dataset"></a>Gegevensset
Als u een gegevensset van een Azure-tabel wilt definiëren, stelt u het **type** van de gegevensset in op **AzureTable** en geeft u de volgende eigenschappen op in de sectie **typeProperties** :

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| tableName |De naam van de tabel in de Azure Table-Data Base-instantie waarnaar de gekoppelde service verwijst. |Ja. Wanneer een TableName zonder azureTableSourceQuery is opgegeven, worden alle records uit de tabel naar de bestemming gekopieerd. Als er ook een azureTableSourceQuery is opgegeven, worden records uit de tabel die aan de query voldoen, gekopieerd naar de bestemming. |

#### <a name="example"></a>Voorbeeld

```json
{
    "name": "AzureTableInput",
    "properties": {
        "type": "AzureTable",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Zie het artikel over [Azure Table Storage-connector](data-factory-azure-table-connector.md#dataset-properties) voor meer informatie over deze gekoppelde services.

### <a name="azure-table-source-in-copy-activity"></a>Azure-tabel bron in Kopieer activiteit
Als u gegevens uit Azure Table Storage kopieert, stelt u het **bron type** van de Kopieer activiteit in op **AzureTableSource** en geeft u de volgende eigenschappen op in de sectie **bron** :

| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| azureTableSourceQuery |Gebruik de aangepaste query om gegevens te lezen. |Query reeks voor Azure Table. Zie de voor beelden in de volgende sectie. |Nee. Wanneer een TableName zonder azureTableSourceQuery is opgegeven, worden alle records uit de tabel naar de bestemming gekopieerd. Als er ook een azureTableSourceQuery is opgegeven, worden records uit de tabel die aan de query voldoen, gekopieerd naar de bestemming. |
| azureTableSourceIgnoreTableNotFound |Geef aan of de uitzonde ring van de tabel niet bestaat. |TRUE<br/>FALSE |No |

#### <a name="example"></a>Voorbeeld

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "AzureTabletoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureTableInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "AzureTableSource",
                    "AzureTableSourceQuery": "PartitionKey eq 'DefaultPartitionKey'"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Zie het artikel over [Azure Table Storage-connector](data-factory-azure-table-connector.md#copy-activity-properties) voor meer informatie over deze gekoppelde services.

### <a name="azure-table-sink-in-copy-activity"></a>Azure Table sink in Kopieer activiteit
Als u gegevens naar Azure Table Storage kopieert, stelt u het **sink-type** van de Kopieer activiteit in op **AzureTableSink** en geeft u de volgende eigenschappen op in de sectie **sink** :

| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| azureTableDefaultPartitionKeyValue |Standaard waarde voor de partitie sleutel die door de Sink kan worden gebruikt. |Een teken reeks waarde. |No |
| azureTablePartitionKeyName |Geef de naam op van de kolom waarvan de waarden worden gebruikt als partitie sleutels. Als u niets opgeeft, wordt AzureTableDefaultPartitionKeyValue gebruikt als partitie sleutel. |Een kolom naam. |No |
| azureTableRowKeyName |Geef de naam van de kolom op waarvan de kolom waarden worden gebruikt als rijdefinitie. Als u deze niet opgeeft, gebruikt u een GUID voor elke rij. |Een kolom naam. |No |
| azureTableInsertType |De modus om gegevens in de Azure-tabel in te voegen.<br/><br/>Met deze eigenschap bepaalt u of bestaande rijen in de uitvoer tabel met overeenkomende partitie-en rijlabels hun waarden vervangen of samengevoegd hebben. <br/><br/>Zie entiteit [invoegen of samen voegen](/rest/api/storageservices/Insert-Or-Merge-Entity) en onderwerpen over [entiteiten invoegen of vervangen](/rest/api/storageservices/Insert-Or-Replace-Entity) voor meer informatie over hoe deze instellingen (samen voegen en vervangen) werken. <br/><br> Deze instelling is van toepassing op het niveau van de rij, niet op het tabel niveau, en geen van beide opties verwijdert rijen in de uitvoer tabel die niet voor komen in de invoer. |samen voegen (standaard)<br/>vervangen |No |
| writeBatchSize |Hiermee worden gegevens in de Azure-tabel ingevoegd wanneer de writeBatchSize of writeBatchTimeout wordt bereikt. |Geheel getal (aantal rijen) |Nee (standaard: 10000) |
| writeBatchTimeout |Hiermee worden gegevens in de Azure-tabel ingevoegd wanneer de writeBatchSize of writeBatchTimeout wordt bereikt |tijdsbestek<br/><br/>Voor beeld: "00:20:00" (20 minuten) |Nee (standaard waarde voor standaard time-outwaarde voor Storage-client 90 sec) |

#### <a name="example"></a>Voorbeeld

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoTable",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": "AzureTableOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "BlobSource"
                },
                "sink": {
                    "type": "AzureTableSink",
                    "writeBatchSize": 100,
                    "writeBatchTimeout": "01:00:00"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```
Zie het artikel over [Azure Table Storage-connector](data-factory-azure-table-connector.md#copy-activity-properties) voor meer informatie over deze gekoppelde services.

## <a name="amazon-redshift"></a>Amazon RedShift

### <a name="linked-service"></a>Gekoppelde service
Als u een gekoppelde Amazon Redshift-service wilt definiëren, stelt u het **type** van de gekoppelde service in op **AmazonRedshift** en geeft u de volgende eigenschappen op in de sectie **typeProperties** :

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| server |Het IP-adres of de hostnaam van de Amazon Redshift-server. |Yes |
| poort |Het nummer van de TCP-poort die de Amazon Redshift-server gebruikt om te Luis teren naar client verbindingen. |Nee, standaard waarde: 5439 |
| database |De naam van de Amazon Redshift-data base. |Yes |
| gebruikersnaam |De naam van de gebruiker die toegang heeft tot de data base. |Yes |
| wachtwoord |Het wacht woord voor het gebruikers account. |Yes |

#### <a name="example"></a>Voorbeeld

```json
{
    "name": "AmazonRedshiftLinkedService",
    "properties": {
        "type": "AmazonRedshift",
        "typeProperties": {
            "server": "<Amazon Redshift host name or IP address>",
            "port": 5439,
            "database": "<database name>",
            "username": "user",
            "password": "password"
        }
    }
}
```

Zie het artikel over de Amazon Redshift-connector voor meer informatie.

### <a name="dataset"></a>Gegevensset
Als u een Amazon Redshift-gegevensset wilt definiëren, stelt u het **type** van de gegevensset in op **RelationalTable** en geeft u de volgende eigenschappen op in de sectie **typeProperties** :

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| tableName |De naam van de tabel in de Amazon Redshift-data base waarnaar de gekoppelde service verwijst. |Nee (als de **query** van **RelationalSource** is opgegeven) |


#### <a name="example"></a>Voorbeeld

```json
{
    "name": "AmazonRedshiftInputDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "AmazonRedshiftLinkedService",
        "typeProperties": {
            "tableName": "<Table name>"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```
Zie het artikel over de Amazon Redshift-connector voor meer informatie.

### <a name="relational-source-in-copy-activity"></a>Relationele bron in Kopieer activiteit
Als u gegevens uit Amazon Redshift kopieert, stelt u het **bron type** van de Kopieer activiteit in op **RelationalSource** en geeft u de volgende eigenschappen op in de sectie **bron** :

| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| query |Gebruik de aangepaste query om gegevens te lezen. |SQL-query teken reeks. Bijvoorbeeld: `select * from MyTable`. |Nee (als **TableName** van **gegevensset** is opgegeven) |

#### <a name="example"></a>Voorbeeld

```json
{
    "name": "CopyAmazonRedshiftToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "AmazonRedshiftInputDataset"
            }],
            "outputs": [{
                "name": "AzureBlobOutputDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "AmazonRedshiftToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```
Zie het artikel over de Amazon Redshift-connector voor meer informatie.

## <a name="ibm-db2"></a>IBM DB2

### <a name="linked-service"></a>Gekoppelde service
Als u een gekoppelde IBM DB2-service wilt definiëren, stelt u het **type** van de gekoppelde service in op **OnPremisesDB2** en geeft u de volgende eigenschappen op in de sectie **typeProperties** :

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| server |Naam van de DB2-Server. |Yes |
| database |Naam van de DB2-Data Base. |Yes |
| schema |De naam van het schema in de data base. De schema naam is hoofdletter gevoelig. |No |
| authenticationType |Type verificatie dat wordt gebruikt om verbinding te maken met de DB2-Data Base. Mogelijke waarden zijn: anoniem, basis en Windows. |Yes |
| gebruikersnaam |Geef de gebruikers naam op als u basis-of Windows-verificatie gebruikt. |No |
| wachtwoord |Geef het wacht woord op voor het gebruikers account dat u hebt opgegeven voor de gebruikers naam. |No |
| gatewayName |De naam van de gateway die de Data Factory-service moet gebruiken om verbinding te maken met de on-premises DB2-Data Base. |Yes |

#### <a name="example"></a>Voorbeeld
```json
{
    "name": "OnPremDb2LinkedService",
    "properties": {
        "type": "OnPremisesDb2",
        "typeProperties": {
            "server": "<server>",
            "database": "<database>",
            "schema": "<schema>",
            "authenticationType": "<authentication type>",
            "username": "<username>",
            "password": "<password>",
            "gatewayName": "<gatewayName>"
        }
    }
}
```
Zie het artikel over IBM DB2-connector voor meer informatie.

### <a name="dataset"></a>Gegevensset
Als u een DB2-gegevensset wilt definiëren, stelt u het **type** van de gegevensset in op **RelationalTable** en geeft u de volgende eigenschappen op in de sectie **typeProperties** :

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| tableName |De naam van de tabel in de DB2-Data Base-instantie waarnaar de gekoppelde service verwijst. De tabel naam is hoofdletter gevoelig. |Nee (als de **query** van **RelationalSource** is opgegeven)

#### <a name="example"></a>Voorbeeld
```json
{
    "name": "Db2DataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremDb2LinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Zie het artikel over IBM DB2-connector voor meer informatie.

### <a name="relational-source-in-copy-activity"></a>Relationele bron in Kopieer activiteit
Als u gegevens uit IBM DB2 kopieert, stelt u het **bron type** van de Kopieer activiteit in op **RelationalSource** en geeft u de volgende eigenschappen op in de sectie **bron** :


| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| query |Gebruik de aangepaste query om gegevens te lezen. |SQL-query teken reeks. Bijvoorbeeld: `"query": "select * from "MySchema"."MyTable""`. |Nee (als **TableName** van **gegevensset** is opgegeven) |

#### <a name="example"></a>Voorbeeld
```json
{
    "name": "CopyDb2ToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "select * from \"Orders\""
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "inputs": [{
                "name": "Db2DataSet"
            }],
            "outputs": [{
                "name": "AzureBlobDb2DataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "Db2ToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```
Zie het artikel over IBM DB2-connector voor meer informatie.

## <a name="mysql"></a>MySQL

### <a name="linked-service"></a>Gekoppelde service
Als u een gekoppelde MySQL-service wilt definiëren, stelt u het **type** van de gekoppelde service in op **OnPremisesMySql** en geeft u de volgende eigenschappen op in de sectie **typeProperties** :

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| server |De naam van de MySQL-server. |Yes |
| database |De naam van de MySQL-data base. |Yes |
| schema |De naam van het schema in de data base. |No |
| authenticationType |Type verificatie dat wordt gebruikt om verbinding te maken met de MySQL-data base. Mogelijke waarden zijn: `Basic` . |Yes |
| userName |Geef de gebruikers naam op om verbinding te maken met de MySQL-data base. |Yes |
| wachtwoord |Geef het wacht woord op voor het gebruikers account dat u hebt opgegeven. |Yes |
| gatewayName |De naam van de gateway die de Data Factory-service moet gebruiken om verbinding te maken met de on-premises MySQL-data base. |Yes |

#### <a name="example"></a>Voorbeeld

```json
{
    "name": "OnPremMySqlLinkedService",
    "properties": {
        "type": "OnPremisesMySql",
        "typeProperties": {
            "server": "<server name>",
            "database": "<database name>",
            "schema": "<schema name>",
            "authenticationType": "<authentication type>",
            "userName": "<user name>",
            "password": "<password>",
            "gatewayName": "<gateway>"
        }
    }
}
```

Zie [mysql connector](data-factory-onprem-mysql-connector.md#linked-service-properties) article (Engelstalig) voor meer informatie.

### <a name="dataset"></a>Gegevensset
Als u een MySQL-gegevensset wilt definiëren, stelt u het **type** van de gegevensset in op **RelationalTable** en geeft u de volgende eigenschappen op in de sectie **typeProperties** :

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| tableName |De naam van de tabel in de MySQL-data base-instantie waarnaar de gekoppelde service verwijst. |Nee (als de **query** van **RelationalSource** is opgegeven) |

#### <a name="example"></a>Voorbeeld

```json
{
    "name": "MySqlDataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremMySqlLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```
Zie [mysql connector](data-factory-onprem-mysql-connector.md#dataset-properties) article (Engelstalig) voor meer informatie.

### <a name="relational-source-in-copy-activity"></a>Relationele bron in Kopieer activiteit
Als u gegevens uit een MySQL-data base kopieert, stelt u het **bron type** van de Kopieer activiteit in op **RelationalSource** en geeft u de volgende eigenschappen op in de sectie **bron** :


| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| query |Gebruik de aangepaste query om gegevens te lezen. |SQL-query teken reeks. Bijvoorbeeld: `select * from MyTable`. |Nee (als **TableName** van **gegevensset** is opgegeven) |


#### <a name="example"></a>Voorbeeld
```json
{
    "name": "CopyMySqlToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "MySqlDataSet"
            }],
            "outputs": [{
                "name": "AzureBlobMySqlDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "MySqlToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```

Zie [mysql connector](data-factory-onprem-mysql-connector.md#copy-activity-properties) article (Engelstalig) voor meer informatie.

## <a name="oracle"></a>Oracle

### <a name="linked-service"></a>Gekoppelde service
Als u een gekoppelde Oracle-service wilt definiëren, stelt u het **type** van de gekoppelde service in op **OnPremisesOracle** en geeft u de volgende eigenschappen op in de sectie **typeProperties** :

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| driverType | Opgeven welk stuur programma moet worden gebruikt voor het kopiëren van gegevens van/naar Oracle Database. Toegestane waarden zijn **micro soft** of **ODP** (standaard). Zie de sectie ondersteunde versie en installatie voor details van Stuur Programma's. | No |
| connectionString | Geef de gegevens op die nodig zijn om verbinding te maken met het Oracle Database-exemplaar voor de Connections Tring-eigenschap. | Yes |
| gatewayName | De naam van de gateway die wordt gebruikt om verbinding te maken met de on-premises Oracle-Server |Yes |

#### <a name="example"></a>Voorbeeld
```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "driverType": "Microsoft",
            "connectionString": "Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

Zie het artikel [Oracle Connector](data-factory-onprem-oracle-connector.md#linked-service-properties) (Engelstalig) voor meer informatie.

### <a name="dataset"></a>Gegevensset
Als u een Oracle-gegevensset wilt definiëren, stelt u het **type** van de gegevensset in op **OracleTable** en geeft u de volgende eigenschappen op in de sectie **typeProperties** :

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| tableName |De naam van de tabel in het Oracle Database waarnaar de gekoppelde service verwijst. |Nee (als **oracleReaderQuery** van **OracleSource** is opgegeven) |

#### <a name="example"></a>Voorbeeld

```json
{
    "name": "OracleInput",
    "properties": {
        "type": "OracleTable",
        "linkedServiceName": "OnPremisesOracleLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "external": true,
        "availability": {
            "offset": "01:00:00",
            "interval": "1",
            "anchorDateTime": "2016-02-27T12:00:00",
            "frequency": "Hour"
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```
Zie het artikel [Oracle Connector](data-factory-onprem-oracle-connector.md#dataset-properties) (Engelstalig) voor meer informatie.

### <a name="oracle-source-in-copy-activity"></a>Oracle-bron in Kopieer activiteit
Als u gegevens uit een Oracle-data base kopieert, stelt u het **bron type** van de Kopieer activiteit in op **OracleSource** en geeft u de volgende eigenschappen op in de sectie **bron** :

| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| oracleReaderQuery |Gebruik de aangepaste query om gegevens te lezen. |SQL-query teken reeks. Bijvoorbeeld: `select * from MyTable` <br/><br/>Indien niet opgegeven, de SQL-instructie die wordt uitgevoerd: `select * from MyTable` |Nee (als **TableName** van **gegevensset** is opgegeven) |

#### <a name="example"></a>Voorbeeld

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "OracletoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": " OracleInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "OracleSource",
                    "oracleReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
            "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Zie het artikel [Oracle Connector](data-factory-onprem-oracle-connector.md#copy-activity-properties) (Engelstalig) voor meer informatie.

### <a name="oracle-sink-in-copy-activity"></a>Oracle-sink in Kopieer activiteit
Als u gegevens kopieert naar een Data Base van het type Oracle, stelt u het **sink-soort** van de Kopieer activiteit in op **OracleSink** en geeft u de volgende eigenschappen op in de sectie **sink** :

| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| writeBatchTimeout |Wacht tijd voordat de batch INSERT-bewerking is voltooid voordat er een time-out optreedt. |tijdsbestek<br/><br/> Voor beeld: 00:30:00 (30 minuten). |No |
| writeBatchSize |Hiermee worden gegevens in de SQL-tabel ingevoegd wanneer de buffer grootte writeBatchSize bereikt. |Geheel getal (aantal rijen) |Nee (standaard: 100) |
| sqlWriterCleanupScript |Geef een query op voor de Kopieer activiteit die moet worden uitgevoerd, zodat de gegevens van een specifiek segment worden opgeruimd. |Een query-instructie. |No |
| sliceIdentifierColumnName |Geef de kolom naam voor de Kopieer activiteit op die moet worden gevuld met een automatisch gegenereerde segment-id, die wordt gebruikt om gegevens van een specifiek segment op te schonen wanneer het opnieuw wordt uitgevoerd. |Kolom naam van een kolom met het gegevens type binary (32). |No |

#### <a name="example"></a>Voorbeeld
```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-05T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoOracle",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": "OracleOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "BlobSource"
                },
                "sink": {
                    "type": "OracleSink"
                }
            },
            "scheduler": {
                "frequency": "Day",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```
Zie het artikel [Oracle Connector](data-factory-onprem-oracle-connector.md#copy-activity-properties) (Engelstalig) voor meer informatie.

## <a name="postgresql"></a>PostgreSQL

### <a name="linked-service"></a>Gekoppelde service
Als u een gekoppelde PostgreSQL-service wilt definiëren, stelt u het **type** van de gekoppelde service in op **OnPremisesPostgreSql** en geeft u de volgende eigenschappen op in de sectie **typeProperties** :

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| server |De naam van de PostgreSQL-server. |Yes |
| database |De naam van de PostgreSQL-data base. |Yes |
| schema |De naam van het schema in de data base. De schema naam is hoofdletter gevoelig. |No |
| authenticationType |Type verificatie dat wordt gebruikt om verbinding te maken met de PostgreSQL-data base. Mogelijke waarden zijn: anoniem, basis en Windows. |Yes |
| gebruikersnaam |Geef de gebruikers naam op als u basis-of Windows-verificatie gebruikt. |No |
| wachtwoord |Geef het wacht woord op voor het gebruikers account dat u hebt opgegeven voor de gebruikers naam. |No |
| gatewayName |De naam van de gateway die de Data Factory-service moet gebruiken om verbinding te maken met de on-premises PostgreSQL-data base. |Yes |

#### <a name="example"></a>Voorbeeld

```json
{
    "name": "OnPremPostgreSqlLinkedService",
    "properties": {
        "type": "OnPremisesPostgreSql",
        "typeProperties": {
            "server": "<server>",
            "database": "<database>",
            "schema": "<schema>",
            "authenticationType": "<authentication type>",
            "username": "<username>",
            "password": "<password>",
            "gatewayName": "<gatewayName>"
        }
    }
}
```
Zie [postgresql-connector](data-factory-onprem-postgresql-connector.md#linked-service-properties) artikel voor meer informatie.

### <a name="dataset"></a>Gegevensset
Als u een PostgreSQL-gegevensset wilt definiëren, stelt u het **type** van de gegevensset in op **RelationalTable** en geeft u de volgende eigenschappen op in de sectie **typeProperties** :

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| tableName |De naam van de tabel in de PostgreSQL-data base-instantie waarnaar de gekoppelde service verwijst. De tabel naam is hoofdletter gevoelig. |Nee (als de **query** van **RelationalSource** is opgegeven) |

#### <a name="example"></a>Voorbeeld
```json
{
    "name": "PostgreSqlDataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremPostgreSqlLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```
Zie [postgresql-connector](data-factory-onprem-postgresql-connector.md#dataset-properties) artikel voor meer informatie.

### <a name="relational-source-in-copy-activity"></a>Relationele bron in Kopieer activiteit
Als u gegevens uit een PostgreSQL-data base kopieert, stelt u het **bron type** van de Kopieer activiteit in op **RelationalSource** en geeft u de volgende eigenschappen op in de sectie **bron** :


| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| query |Gebruik de aangepaste query om gegevens te lezen. |SQL-query teken reeks. Bijvoorbeeld: "query": "Select * from \" MySchema \" . \" MyTable \" ". |Nee (als **TableName** van **gegevensset** is opgegeven) |

#### <a name="example"></a>Voorbeeld

```json
{
    "name": "CopyPostgreSqlToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "select * from \"public\".\"usstates\""
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "inputs": [{
                "name": "PostgreSqlDataSet"
            }],
            "outputs": [{
                "name": "AzureBlobPostgreSqlDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "PostgreSqlToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```

Zie [postgresql-connector](data-factory-onprem-postgresql-connector.md#copy-activity-properties) artikel voor meer informatie.

## <a name="sap-business-warehouse"></a>SAP Business Warehouse


### <a name="linked-service"></a>Gekoppelde service
Als u een gekoppelde service van SAP Business Warehouse (BW) wilt definiëren, stelt u het **type** van de gekoppelde service in op **SapBw** en geeft u de volgende eigenschappen op in de sectie **typeProperties** :

Eigenschap | Beschrijving | Toegestane waarden | Vereist
-------- | ----------- | -------------- | --------
server | De naam van de server waarop het SAP BW-exemplaar zich bevindt. | tekenreeks | Yes
systemNumber | Systeem nummer van het SAP BW systeem. | Decimaal getal van twee cijfers dat wordt weer gegeven als een teken reeks. | Yes
clientId | Client-ID van de client in het SAP W-systeem. | Decimaal getal met drie cijfers dat wordt weer gegeven als een teken reeks. | Yes
gebruikersnaam | Naam van de gebruiker die toegang heeft tot de SAP-server | tekenreeks | Yes
wachtwoord | Het wachtwoord voor de gebruiker. | tekenreeks | Yes
gatewayName | De naam van de gateway die de Data Factory-service moet gebruiken om verbinding te maken met het on-premises SAP BW exemplaar. | tekenreeks | Yes
encryptedCredential | De versleutelde referentie teken reeks. | tekenreeks | No

#### <a name="example"></a>Voorbeeld

```json
{
    "name": "SapBwLinkedService",
    "properties": {
        "type": "SapBw",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client id>",
            "username": "<SAP user>",
            "password": "<Password for SAP user>",
            "gatewayName": "<gateway name>"
        }
    }
}
```

Zie het artikel [SAP Business Warehouse connector](data-factory-sap-business-warehouse-connector.md#linked-service-properties) (Engelstalig) voor meer informatie.

### <a name="dataset"></a>Gegevensset
Als u een SAP BW gegevensset wilt definiëren, stelt u het **type** van de gegevensset in op **RelationalTable**. Er zijn geen typespecifieke eigenschappen die worden ondersteund voor de SAP BW-gegevensset van het type **RelationalTable**.

#### <a name="example"></a>Voorbeeld

```json
{
    "name": "SapBwDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "SapBwLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```
Zie het artikel [SAP Business Warehouse connector](data-factory-sap-business-warehouse-connector.md#dataset-properties) (Engelstalig) voor meer informatie.

### <a name="relational-source-in-copy-activity"></a>Relationele bron in Kopieer activiteit
Als u gegevens van SAP Business Warehouse kopieert, stelt u het **bron type** van de Kopieer activiteit in op **RelationalSource** en geeft u de volgende eigenschappen op in de sectie **bron** :


| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| query | Hiermee geeft u de MDX-query op die gegevens uit het SAP BW exemplaar moet lezen. | MDX-query. | Yes |

#### <a name="example"></a>Voorbeeld

```json
{
    "name": "CopySapBwToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "<MDX query for SAP BW>"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "SapBwDataset"
            }],
            "outputs": [{
                "name": "AzureBlobDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "SapBwToBlob"
        }],
        "start": "2017-03-01T18:00:00",
        "end": "2017-03-01T19:00:00"
    }
}
```

Zie het artikel [SAP Business Warehouse connector](data-factory-sap-business-warehouse-connector.md#copy-activity-properties) (Engelstalig) voor meer informatie.

## <a name="sap-hana"></a>SAP HANA

### <a name="linked-service"></a>Gekoppelde service
Als u een SAP HANA gekoppelde service wilt definiëren, stelt u het **type** van de gekoppelde service in op **SapHana** en geeft u de volgende eigenschappen op in de sectie **typeProperties** :

Eigenschap | Beschrijving | Toegestane waarden | Vereist
-------- | ----------- | -------------- | --------
server | De naam van de server waarop het SAP HANA-exemplaar zich bevindt. Als op uw server een aangepaste poort wordt gebruikt, geeft u op `server:port` . | tekenreeks | Yes
authenticationType | Type verificatie. | tekenreeksexpressie. "Basic" of "Windows" | Yes
gebruikersnaam | Naam van de gebruiker die toegang heeft tot de SAP-server | tekenreeks | Yes
wachtwoord | Het wachtwoord voor de gebruiker. | tekenreeks | Yes
gatewayName | De naam van de gateway die de Data Factory-service moet gebruiken om verbinding te maken met het on-premises SAP HANA exemplaar. | tekenreeks | Yes
encryptedCredential | De versleutelde referentie teken reeks. | tekenreeks | No

#### <a name="example"></a>Voorbeeld

```json
{
    "name": "SapHanaLinkedService",
    "properties": {
        "type": "SapHana",
        "typeProperties": {
            "server": "<server name>",
            "authenticationType": "<Basic, or Windows>",
            "username": "<SAP user>",
            "password": "<Password for SAP user>",
            "gatewayName": "<gateway name>"
        }
    }
}

```
Zie [SAP Hana connector](data-factory-sap-hana-connector.md#linked-service-properties) -artikel voor meer informatie.

### <a name="dataset"></a>Gegevensset
Als u een SAP HANA gegevensset wilt definiëren, stelt u het **type** van de gegevensset in op **RelationalTable**. Er zijn geen typespecifieke eigenschappen die worden ondersteund voor de SAP HANA-gegevensset van het type **RelationalTable**.

#### <a name="example"></a>Voorbeeld

```json
{
    "name": "SapHanaDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "SapHanaLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```
Zie [SAP Hana connector](data-factory-sap-hana-connector.md#dataset-properties) -artikel voor meer informatie.

### <a name="relational-source-in-copy-activity"></a>Relationele bron in Kopieer activiteit
Als u gegevens uit een SAP HANA gegevens archief kopieert, stelt u het **bron type** van de Kopieer activiteit in op **RelationalSource** en geeft u de volgende eigenschappen op in de sectie **bron** :

| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| query | Hiermee geeft u de SQL-query voor het lezen van gegevens uit het SAP HANA-exemplaar. | SQL-query. | Yes |


#### <a name="example"></a>Voorbeeld


```json
{
    "name": "CopySapHanaToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "<SQL Query for HANA>"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "SapHanaDataset"
            }],
            "outputs": [{
                "name": "AzureBlobDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "SapHanaToBlob"
        }],
        "start": "2017-03-01T18:00:00",
        "end": "2017-03-01T19:00:00"
    }
}
```

Zie [SAP Hana connector](data-factory-sap-hana-connector.md#copy-activity-properties) -artikel voor meer informatie.


## <a name="sql-server"></a>SQL Server

### <a name="linked-service"></a>Gekoppelde service
U maakt een gekoppelde service van het type **OnPremisesSqlServer** om een SQL Server-Data Base aan een Data Factory te koppelen. In de volgende tabel vindt u een beschrijving van de JSON-elementen die specifiek zijn voor SQL Server gekoppelde service.

In de volgende tabel vindt u een beschrijving van de JSON-elementen die specifiek zijn voor SQL Server gekoppelde service.

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| type |De eigenschap type moet worden ingesteld op: **OnPremisesSqlServer**. |Ja |
| connectionString |Geef Connections Tring-gegevens op die nodig zijn om verbinding te maken met de SQL Server-Data Base via SQL-verificatie of Windows-verificatie. |Yes |
| gatewayName |De naam van de gateway die de Data Factory-service moet gebruiken om verbinding te maken met de SQL Server-Data Base. |Yes |
| gebruikersnaam |Geef de gebruikers naam op als u Windows-verificatie gebruikt. Voor beeld: **domainname \\ gebruikers naam**. |No |
| wachtwoord |Geef het wacht woord op voor het gebruikers account dat u hebt opgegeven voor de gebruikers naam. |No |

U kunt referenties versleutelen met de cmdlet **New-AzDataFactoryEncryptValue** en deze gebruiken in de Connection String, zoals wordt weer gegeven in het volgende voor beeld (eigenschap **EncryptedCredential** ):

```json
"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",
```


#### <a name="example-json-for-using-sql-authentication"></a>Voor beeld: JSON voor het gebruik van SQL-verificatie

```json
{
    "name": "MyOnPremisesSQLDB",
    "properties": {
        "type": "OnPremisesSqlServer",
        "typeProperties": {
            "connectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=False;User ID=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```
#### <a name="example-json-for-using-windows-authentication"></a>Voor beeld: JSON voor het gebruik van Windows-verificatie

Als gebruikers naam en wacht woord zijn opgegeven, wordt deze door de gateway gebruikt om het opgegeven gebruikers account te imiteren om verbinding te maken met de SQL Server-Data Base. Anders maakt gateway verbinding met de SQL Server rechtstreeks met de beveiligings context van de gateway (het opstart account).

```json
{
    "Name": " MyOnPremisesSQLDB",
    "Properties": {
        "type": "OnPremisesSqlServer",
        "typeProperties": {
            "ConnectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=True;",
            "username": "<domain\\username>",
            "password": "<password>",
            "gatewayName": "<gateway name>"
        }
    }
}
```

Zie [SQL Server connector](data-factory-sqlserver-connector.md#linked-service-properties) -artikel voor meer informatie.

### <a name="dataset"></a>Gegevensset
Als u een SQL Server gegevensset wilt definiëren, stelt u het **type** van de gegevensset in op **SqlServerTable** en geeft u de volgende eigenschappen op in de sectie **typeProperties** :

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| tableName |De naam van de tabel of weer gave in de SQL Server Data Base-instantie waarnaar de gekoppelde service verwijst. |Yes |

#### <a name="example"></a>Voorbeeld
```json
{
    "name": "SqlServerInput",
    "properties": {
        "type": "SqlServerTable",
        "linkedServiceName": "SqlServerLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Zie [SQL Server connector](data-factory-sqlserver-connector.md#dataset-properties) -artikel voor meer informatie.

### <a name="sql-source-in-copy-activity"></a>SQL-bron in Kopieer activiteit
Als u gegevens uit een SQL Server-Data Base kopieert, stelt u het **bron type** van de Kopieer activiteit in op **SqlSource** en geeft u de volgende eigenschappen op in de sectie **bron** :


| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| sqlReaderQuery |Gebruik de aangepaste query om gegevens te lezen. |SQL-query teken reeks. Bijvoorbeeld: `select * from MyTable`. Kan verwijzen naar meerdere tabellen vanuit de data base waarnaar wordt verwezen door de invoer gegevensset. Indien niet opgegeven, de SQL-instructie die wordt uitgevoerd: Selecteer een van de MyTable. |No |
| sqlReaderStoredProcedureName |De naam van de opgeslagen procedure waarmee gegevens uit de bron tabel worden gelezen. |De naam van de opgeslagen procedure. |No |
| storedProcedureParameters |Para meters voor de opgeslagen procedure. |Naam/waarde-paren. Namen en hoofdletter gebruik van para meters moeten overeenkomen met de namen en de behuizing van de opgeslagen procedure parameters. |No |

Als de **sqlReaderQuery** is opgegeven voor de SqlSource, voert de Kopieer activiteit deze query uit op basis van de SQL Server database bron om de gegevens op te halen.

U kunt ook een opgeslagen procedure opgeven door de **sqlReaderStoredProcedureName** en **storedProcedureParameters** op te geven (als voor de opgeslagen procedure para meters worden gebruikt).

Als u sqlReaderQuery of sqlReaderStoredProcedureName niet opgeeft, worden de kolommen die in de sectie structuur zijn gedefinieerd, gebruikt om een SELECT-query te maken die wordt uitgevoerd op basis van de SQL Server Data Base. Als de definitie van de gegevensset niet de structuur heeft, worden alle kolommen geselecteerd in de tabel.

> [!NOTE]
> Wanneer u **sqlReaderStoredProcedureName** gebruikt, moet u nog steeds een waarde opgeven voor de eigenschap **TABLENAME** in de JSON van de gegevensset. Er zijn geen validaties die worden uitgevoerd voor deze tabel.


#### <a name="example"></a>Voorbeeld
```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "SqlServertoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": " SqlServerInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "SqlSource",
                    "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

In dit voor beeld is **sqlReaderQuery** opgegeven voor de SqlSource. De Kopieer activiteit voert deze query uit op basis van de SQL Server database bron om de gegevens op te halen. U kunt ook een opgeslagen procedure opgeven door de **sqlReaderStoredProcedureName** en **storedProcedureParameters** op te geven (als voor de opgeslagen procedure para meters worden gebruikt). De sqlReaderQuery kan verwijzen naar meerdere tabellen in de data base waarnaar wordt verwezen door de invoer gegevensset. Het is niet beperkt tot de tabel die is ingesteld als de tabel naam van de gegevensset typeProperty.

Als u sqlReaderQuery of sqlReaderStoredProcedureName niet opgeeft, worden de kolommen die in de sectie structuur zijn gedefinieerd, gebruikt om een SELECT-query te maken die wordt uitgevoerd op basis van de SQL Server Data Base. Als de definitie van de gegevensset niet de structuur heeft, worden alle kolommen geselecteerd in de tabel.

Zie [SQL Server connector](data-factory-sqlserver-connector.md#copy-activity-properties) -artikel voor meer informatie.

### <a name="sql-sink-in-copy-activity"></a>SQL-sink in Kopieer activiteit
Als u gegevens naar een SQL Server-Data Base kopieert, stelt u het **sink-type** van de Kopieer activiteit in op **SqlSink** en geeft u de volgende eigenschappen op in de sectie **sink** :

| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| writeBatchTimeout |Wacht tijd voordat de batch INSERT-bewerking is voltooid voordat er een time-out optreedt. |tijdsbestek<br/><br/> Voor beeld: "00:30:00" (30 minuten). |No |
| writeBatchSize |Hiermee worden gegevens in de SQL-tabel ingevoegd wanneer de buffer grootte writeBatchSize bereikt. |Geheel getal (aantal rijen) |Nee (standaard: 10000) |
| sqlWriterCleanupScript |Geef een query op om de Kopieer activiteit uit te voeren, zodat de gegevens van een specifiek segment worden opgeruimd. Zie de sectie REPEAT baarheid voor meer informatie. |Een query-instructie. |No |
| sliceIdentifierColumnName |Geef de kolom naam voor de Kopieer activiteit op die moet worden gevuld met een automatisch gegenereerde segment-id, die wordt gebruikt om gegevens van een specifiek segment op te schonen wanneer het opnieuw wordt uitgevoerd. Zie de sectie REPEAT baarheid voor meer informatie. |Kolom naam van een kolom met het gegevens type binary (32). |No |
| sqlWriterStoredProcedureName |De naam van de opgeslagen procedure waarmee gegevens in de doel tabel worden upsert (bijgewerkt/ingevoegd). |De naam van de opgeslagen procedure. |No |
| storedProcedureParameters |Para meters voor de opgeslagen procedure. |Naam/waarde-paren. Namen en hoofdletter gebruik van para meters moeten overeenkomen met de namen en de behuizing van de opgeslagen procedure parameters. |No |
| sqlWriterTableType |Geef de naam op van het tabel type dat moet worden gebruikt in de opgeslagen procedure. Als u de Kopieer activiteit uitvoert, worden de gegevens die in een tijdelijke tabel worden verplaatst, met dit tabel type beschikbaar. De opgeslagen procedure code kan vervolgens de gegevens samen voegen die worden gekopieerd met bestaande gegevens. |De naam van een tabel type. |No |

#### <a name="example"></a>Voorbeeld
De pijp lijn bevat een Kopieer activiteit die is geconfigureerd voor het gebruik van deze invoer-en uitvoer gegevens sets en die is gepland om elk uur te worden uitgevoerd. In de JSON-definitie van de pijp lijn is het **bron** type ingesteld op **BlobSource** en het **sink** -type is ingesteld op **SqlSink**.

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoSQL",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": " SqlServerOutput "
            }],
            "typeProperties": {
                "source": {
                    "type": "BlobSource",
                    "blobColumnSeparators": ","
                },
                "sink": {
                    "type": "SqlSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Zie [SQL Server connector](data-factory-sqlserver-connector.md#copy-activity-properties) -artikel voor meer informatie.

## <a name="sybase"></a>Sybase

### <a name="linked-service"></a>Gekoppelde service
Als u een gekoppelde Sybase-service wilt definiëren, stelt u het **type** van de gekoppelde service in op **OnPremisesSybase** en geeft u de volgende eigenschappen op in de sectie **typeProperties** :

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| server |De naam van de Sybase-server. |Yes |
| database |De naam van de Sybase-data base. |Yes |
| schema |De naam van het schema in de data base. |No |
| authenticationType |Type verificatie dat wordt gebruikt om verbinding te maken met de Sybase-data base. Mogelijke waarden zijn: anoniem, basis en Windows. |Yes |
| gebruikersnaam |Geef de gebruikers naam op als u basis-of Windows-verificatie gebruikt. |No |
| wachtwoord |Geef het wacht woord op voor het gebruikers account dat u hebt opgegeven voor de gebruikers naam. |No |
| gatewayName |De naam van de gateway die de Data Factory-service moet gebruiken om verbinding te maken met de on-premises Sybase-data base. |Yes |

#### <a name="example"></a>Voorbeeld
```json
{
    "name": "OnPremSybaseLinkedService",
    "properties": {
        "type": "OnPremisesSybase",
        "typeProperties": {
            "server": "<server>",
            "database": "<database>",
            "schema": "<schema>",
            "authenticationType": "<authentication type>",
            "username": "<username>",
            "password": "<password>",
            "gatewayName": "<gatewayName>"
        }
    }
}
```

Zie voor meer informatie het artikel [Sybase connector](data-factory-onprem-sybase-connector.md#linked-service-properties) .

### <a name="dataset"></a>Gegevensset
Als u een Sybase-gegevensset wilt definiëren, stelt u het **type** van de gegevensset in op **RelationalTable** en geeft u de volgende eigenschappen op in de sectie **typeProperties** :

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| tableName |De naam van de tabel in het data base-exemplaar van Sybase waarnaar de gekoppelde service verwijst. |Nee (als de **query** van **RelationalSource** is opgegeven) |

#### <a name="example"></a>Voorbeeld

```json
{
    "name": "SybaseDataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremSybaseLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Zie voor meer informatie het artikel [Sybase connector](data-factory-onprem-sybase-connector.md#dataset-properties) .

### <a name="relational-source-in-copy-activity"></a>Relationele bron in Kopieer activiteit
Als u gegevens uit een Sybase-data base kopieert, stelt u het **bron type** van de Kopieer activiteit in op **RelationalSource** en geeft u de volgende eigenschappen op in de sectie **bron** :


| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| query |Gebruik de aangepaste query om gegevens te lezen. |SQL-query teken reeks. Bijvoorbeeld: `select * from MyTable`. |Nee (als **TableName** van **gegevensset** is opgegeven) |

#### <a name="example"></a>Voorbeeld

```json
{
    "name": "CopySybaseToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "select * from DBA.Orders"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "inputs": [{
                "name": "SybaseDataSet"
            }],
            "outputs": [{
                "name": "AzureBlobSybaseDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "SybaseToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```

Zie voor meer informatie het artikel [Sybase connector](data-factory-onprem-sybase-connector.md#copy-activity-properties) .

## <a name="teradata"></a>Teradata

### <a name="linked-service"></a>Gekoppelde service
Als u een gekoppelde Teradata-service wilt definiëren, stelt u het **type** van de gekoppelde service in op **OnPremisesTeradata** en geeft u de volgende eigenschappen op in de sectie **typeProperties** :

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| server |Naam van de server van de Teradata. |Yes |
| authenticationType |Type verificatie dat wordt gebruikt om verbinding te maken met de Teradata-data base. Mogelijke waarden zijn: anoniem, basis en Windows. |Yes |
| gebruikersnaam |Geef de gebruikers naam op als u basis-of Windows-verificatie gebruikt. |No |
| wachtwoord |Geef het wacht woord op voor het gebruikers account dat u hebt opgegeven voor de gebruikers naam. |No |
| gatewayName |De naam van de gateway die de Data Factory-service moet gebruiken om verbinding te maken met de on-premises Teradata-data base. |Yes |

#### <a name="example"></a>Voorbeeld
```json
{
    "name": "OnPremTeradataLinkedService",
    "properties": {
        "type": "OnPremisesTeradata",
        "typeProperties": {
            "server": "<server>",
            "authenticationType": "<authentication type>",
            "username": "<username>",
            "password": "<password>",
            "gatewayName": "<gatewayName>"
        }
    }
}
```

Zie voor meer informatie het artikel [Teradata-connector](data-factory-onprem-teradata-connector.md#linked-service-properties) .

### <a name="dataset"></a>Gegevensset
Als u een Teradata-BLOB-gegevensset wilt definiëren, stelt u het **type** van de gegevensset in op **RelationalTable**. Er zijn momenteel geen type-eigenschappen die voor de Teradata-gegevensset worden ondersteund.

#### <a name="example"></a>Voorbeeld
```json
{
    "name": "TeradataDataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremTeradataLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Zie voor meer informatie het artikel [Teradata-connector](data-factory-onprem-teradata-connector.md#dataset-properties) .

### <a name="relational-source-in-copy-activity"></a>Relationele bron in Kopieer activiteit
Als u gegevens uit een Teradata-data base kopieert, stelt u het **bron type** van de Kopieer activiteit in op **RelationalSource** en geeft u de volgende eigenschappen op in de sectie **bron** :

| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| query |Gebruik de aangepaste query om gegevens te lezen. |SQL-query teken reeks. Bijvoorbeeld: `select * from MyTable`. |Yes |

#### <a name="example"></a>Voorbeeld

```json
{
    "name": "CopyTeradataToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', SliceStart, SliceEnd)"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "TeradataDataSet"
            }],
            "outputs": [{
                "name": "AzureBlobTeradataDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "TeradataToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "isPaused": false
    }
}
```

Zie voor meer informatie het artikel [Teradata-connector](data-factory-onprem-teradata-connector.md#copy-activity-properties) .

## <a name="cassandra"></a>Cassandra


### <a name="linked-service"></a>Gekoppelde service
Als u een gekoppelde Cassandra-service wilt definiëren, stelt u het **type** van de gekoppelde service in op **OnPremisesCassandra** en geeft u de volgende eigenschappen op in de sectie **typeProperties** :

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| host |Een of meer IP-adressen of hostnamen van Cassandra-servers.<br/><br/>Geef een door komma's gescheiden lijst met IP-adressen of hostnamen op om gelijktijdig verbinding te maken met alle servers. |Yes |
| poort |De TCP-poort die de Cassandra-server gebruikt om te Luis teren naar client verbindingen. |Nee, standaard waarde: 9042 |
| authenticationType |Basic of anoniem |Yes |
| gebruikersnaam |Geef de gebruikers naam voor het gebruikers account op. |Ja, als authenticationType is ingesteld op Basic. |
| wachtwoord |Geef het wacht woord voor het gebruikers account op. |Ja, als authenticationType is ingesteld op Basic. |
| gatewayName |De naam van de gateway die wordt gebruikt om verbinding te maken met de on-premises Cassandra-data base. |Yes |
| encryptedCredential |Referentie versleuteld door de gateway. |No |

#### <a name="example"></a>Voorbeeld

```json
{
    "name": "CassandraLinkedService",
    "properties": {
        "type": "OnPremisesCassandra",
        "typeProperties": {
            "authenticationType": "Basic",
            "host": "<cassandra server name or IP address>",
            "port": 9042,
            "username": "user",
            "password": "password",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

Zie [Cassandra-connector](data-factory-onprem-cassandra-connector.md#linked-service-properties) artikel voor meer informatie.

### <a name="dataset"></a>Gegevensset
Als u een Cassandra-gegevensset wilt definiëren, stelt u het **type** van de gegevensset in op **CassandraTable** en geeft u de volgende eigenschappen op in de sectie **typeProperties** :

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| Keys Pace |De naam van de spatie of het schema in de Cassandra-data base. |Ja (als de **query** voor **CassandraSource** niet is gedefinieerd). |
| tableName |De naam van de tabel in de Cassandra-data base. |Ja (als de **query** voor **CassandraSource** niet is gedefinieerd). |

#### <a name="example"></a>Voorbeeld

```json
{
    "name": "CassandraInput",
    "properties": {
        "linkedServiceName": "CassandraLinkedService",
        "type": "CassandraTable",
        "typeProperties": {
            "tableName": "mytable",
            "keySpace": "<key space>"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Zie [Cassandra-connector](data-factory-onprem-cassandra-connector.md#dataset-properties) artikel voor meer informatie.

### <a name="cassandra-source-in-copy-activity"></a>Cassandra bron in Kopieer activiteit
Als u gegevens uit Cassandra kopieert, stelt u het **bron type** van de Kopieer activiteit in op **CassandraSource** en geeft u de volgende eigenschappen op in de sectie **bron** :

| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| query |Gebruik de aangepaste query om gegevens te lezen. |SQL-92-query of CQL-query. Zie [CQL Reference](https://docs.datastax.com/en/cql/3.1/cql/cql_reference/cqlReferenceTOC.html)(Engelstalig). <br/><br/>Wanneer u SQL query gebruikt, geeft u de naam van de **spatie op. tabel naam** voor de tabel die u wilt doorzoeken. |Nee (als TableName en Keys op gegevensset zijn gedefinieerd). |
| consistencyLevel |Het consistentie niveau geeft aan hoeveel replica's moeten reageren op een lees aanvraag voordat gegevens worden geretourneerd naar de client toepassing. Cassandra controleert het opgegeven aantal replica's voor gegevens om te voldoen aan de Lees aanvraag. |EEN, TWEE, DRIE, QUORUM, ALLE, LOCAL_QUORUM, EACH_QUORUM LOCAL_ONE. Zie [gegevens consistentie configureren](https://docs.datastax.com/en/cassandra/2.1/cassandra/dml/dml_config_consistency_c.html) voor meer informatie. |Nee. De standaard waarde is één. |

#### <a name="example"></a>Voorbeeld

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "CassandraToAzureBlob",
            "description": "Copy from Cassandra to an Azure blob",
            "type": "Copy",
            "inputs": [{
                "name": "CassandraInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "CassandraSource",
                    "query": "select id, firstname, lastname from mykeyspace.mytable"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Zie [Cassandra-connector](data-factory-onprem-cassandra-connector.md#copy-activity-properties) artikel voor meer informatie.

## <a name="mongodb"></a>MongoDB

### <a name="linked-service"></a>Gekoppelde service
Als u een gekoppelde MongoDB-service wilt definiëren, stelt u het **type** van de gekoppelde service in op **OnPremisesMongoDB** en geeft u de volgende eigenschappen op in de sectie **typeProperties** :

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| server |Het IP-adres of de hostnaam van de MongoDB-server. |Yes |
| poort |TCP-poort die de MongoDB-server gebruikt om te Luis teren naar client verbindingen. |Optioneel, standaard waarde: 27017 |
| authenticationType |Basic of anoniem. |Yes |
| gebruikersnaam |Gebruikers account voor toegang tot MongoDB. |Ja (als basis verificatie wordt gebruikt). |
| wachtwoord |Het wachtwoord voor de gebruiker. |Ja (als basis verificatie wordt gebruikt). |
| authSource |De naam van de MongoDB-data base die u wilt gebruiken om uw referenties voor verificatie te controleren. |Optioneel (als basis verificatie wordt gebruikt). standaard: gebruikt het beheerders account en de data base die is opgegeven met de eigenschap databasename. |
| databaseName |De naam van de MongoDB-data base waartoe u toegang wilt krijgen. |Yes |
| gatewayName |De naam van de gateway die toegang heeft tot het gegevens archief. |Yes |
| encryptedCredential |Referentie versleuteld door gateway. |Optioneel |

#### <a name="example"></a>Voorbeeld

```json
{
    "name": "OnPremisesMongoDbLinkedService",
    "properties": {
        "type": "OnPremisesMongoDb",
        "typeProperties": {
            "authenticationType": "<Basic or Anonymous>",
            "server": "< The IP address or host name of the MongoDB server >",
            "port": "<The number of the TCP port that the MongoDB server uses to listen for client connections.>",
            "username": "<username>",
            "password": "<password>",
            "authSource": "< The database that you want to use to check your credentials for authentication. >",
            "databaseName": "<database name>",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

Zie [MongoDb connector-artikel](data-factory-on-premises-mongodb-connector.md#linked-service-properties) voor meer informatie.

### <a name="dataset"></a>Gegevensset
Als u een MongoDB-gegevensset wilt definiëren, stelt u het **type** van de gegevensset in op **MongoDbCollection** en geeft u de volgende eigenschappen op in de sectie **typeProperties** :

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| collectionName |De naam van de verzameling in de MongoDB-data base. |Yes |

#### <a name="example"></a>Voorbeeld

```json
{
    "name": "MongoDbInputDataset",
    "properties": {
        "type": "MongoDbCollection",
        "linkedServiceName": "OnPremisesMongoDbLinkedService",
        "typeProperties": {
            "collectionName": "<Collection name>"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```

Zie [MongoDb connector-artikel](data-factory-on-premises-mongodb-connector.md#dataset-properties) voor meer informatie.

#### <a name="mongodb-source-in-copy-activity"></a>MongoDB bron in Kopieer activiteit
Als u gegevens uit MongoDB kopieert, stelt u het **bron type** van de Kopieer activiteit in op **MongoDbSource** en geeft u de volgende eigenschappen op in de sectie **bron** :

| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| query |Gebruik de aangepaste query om gegevens te lezen. |SQL-92-query teken reeks. Bijvoorbeeld: `select * from MyTable`. |Nee (als de **verzamelings** - **DataSet** is opgegeven) |

#### <a name="example"></a>Voorbeeld

```json
{
    "name": "CopyMongoDBToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "MongoDbSource",
                    "query": "select * from MyTable"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "MongoDbInputDataset"
            }],
            "outputs": [{
                "name": "AzureBlobOutputDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "MongoDBToAzureBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```

Zie [MongoDb connector-artikel](data-factory-on-premises-mongodb-connector.md#copy-activity-properties) voor meer informatie.

## <a name="amazon-s3"></a>Amazon S3


### <a name="linked-service"></a>Gekoppelde service
Als u een gekoppelde Amazon S3-service wilt definiëren, stelt u het **type** van de gekoppelde service in op **awsaccesskey worden** en geeft u de volgende eigenschappen op in de sectie **typeProperties** :

| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| accessKeyID |ID van de geheime toegangs sleutel. |tekenreeks |Yes |
| secretAccessKey |De geheime toegangs sleutel zelf. |Versleutelde geheime teken reeks |Yes |

#### <a name="example"></a>Voorbeeld
```json
{
    "name": "AmazonS3LinkedService",
    "properties": {
        "type": "AwsAccessKey",
        "typeProperties": {
            "accessKeyId": "<access key id>",
            "secretAccessKey": "<secret access key>"
        }
    }
}
```

Zie het artikel over de [Amazon S3-connector](data-factory-amazon-simple-storage-service-connector.md#linked-service-properties)voor meer informatie.

### <a name="dataset"></a>Gegevensset
Als u een Amazon S3-gegevensset wilt definiëren, stelt u het **type** van de gegevensset in op **AmazonS3** en geeft u de volgende eigenschappen op in de sectie **typeProperties** :

| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| Bucket |De naam van de S3-Bucket. |Tekenreeks |Ja |
| sleutel |De object sleutel S3. |Tekenreeks |No |
| beleids |Voor voegsel voor de object sleutel S3. Objecten waarvan de sleutels beginnen met dit voor voegsel worden geselecteerd. Is alleen van toepassing als de sleutel leeg is. |Tekenreeks |No |
| versie |De versie van het S3-object als S3-versie beheer is ingeschakeld. |Tekenreeks |No |
| indeling | De volgende indelings typen worden ondersteund: **TextFormat**, **JsonFormat**, **Avro Format**, **OrcFormat**, **ParquetFormat**. Stel de eigenschap **type** onder indeling in op een van deze waarden. Zie voor meer informatie secties [tekst indeling](data-factory-supported-file-and-compression-formats.md#text-format), [JSON-indeling](data-factory-supported-file-and-compression-formats.md#json-format), [Avro](data-factory-supported-file-and-compression-formats.md#avro-format)-indeling, [Orc-indeling](data-factory-supported-file-and-compression-formats.md#orc-format)en Parquet- [indeling](data-factory-supported-file-and-compression-formats.md#parquet-format) . <br><br> Als u bestanden wilt **kopiëren als-zich bevindt** tussen archieven op basis van bestanden (binaire kopie), slaat u de sectie indeling in de gegevensset voor invoer en uitvoer over. |No | |
| compressie | Geef het type en compressie niveau voor de gegevens op. Ondersteunde typen zijn: **gzip**, **Deflate**, **bzip2** en **ZipDeflate**. De ondersteunde niveaus zijn: **optimaal** en **snelst**. Zie [Bestands-en compressie-indelingen in azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support)voor meer informatie. |No | |


> [!NOTE]
> Bucket + sleutel Hiermee geeft u de locatie van het S3-object op waarbij Bucket de hoofd container voor S3-objecten is en de sleutel het volledige pad naar het S3-object.

#### <a name="example-sample-dataset-with-prefix"></a>Voor beeld: gegevensset met voor voegsel

```json
{
    "name": "dataset-s3",
    "properties": {
        "type": "AmazonS3",
        "linkedServiceName": "link- testS3",
        "typeProperties": {
            "prefix": "testFolder/test",
            "bucketName": "<S3 bucket name>",
            "format": {
                "type": "OrcFormat"
            }
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```
#### <a name="example-sample-data-set-with-version"></a>Voor beeld: gegevensset (met versie)

```json
{
    "name": "dataset-s3",
    "properties": {
        "type": "AmazonS3",
        "linkedServiceName": "link- testS3",
        "typeProperties": {
            "key": "testFolder/test.orc",
            "bucketName": "<S3 bucket name>",
            "version": "XXXXXXXXXczm0CJajYkHf0_k6LhBmkcL",
            "format": {
                "type": "OrcFormat"
            }
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```

#### <a name="example-dynamic-paths-for-s3"></a>Voor beeld: dynamische paden voor S3
In het voor beeld gebruiken we vaste waarden voor de eigenschappen Key en Bucket in de Amazon S3-gegevensset.

```json
"key": "testFolder/test.orc",
"bucketName": "<S3 bucket name>",
```

U kunt Data Factory de sleutel en Bucket dynamisch te berekenen tijdens runtime met behulp van systeem variabelen zoals slice start.

```json
"key": "$$Text.Format('{0:MM}/{0:dd}/test.orc', SliceStart)"
"bucketName": "$$Text.Format('{0:yyyy}', SliceStart)"
```

U kunt hetzelfde doen voor de eigenschap prefix van een Amazon S3-gegevensset. Zie [Data Factory-functies en systeem variabelen](data-factory-functions-variables.md) voor een lijst met ondersteunde functies en variabelen.

Zie het artikel over de [Amazon S3-connector](data-factory-amazon-simple-storage-service-connector.md#dataset-properties)voor meer informatie.

### <a name="file-system-source-in-copy-activity"></a>Bestandssysteem bron in Kopieer activiteit
Als u gegevens uit Amazon S3 kopieert, stelt u het **bron type** van de Kopieer activiteit in op **FileSystemSource** en geeft u de volgende eigenschappen op in de sectie **bron** :


| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| recursieve |Hiermee geeft u op of S3-objecten in de directory recursief moeten worden weer geven. |waar/onwaar |No |


#### <a name="example"></a>Voorbeeld


```json
{
    "name": "CopyAmazonS3ToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "FileSystemSource",
                    "recursive": true
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "AmazonS3InputDataset"
            }],
            "outputs": [{
                "name": "AzureBlobOutputDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "AmazonS3ToBlob"
        }],
        "start": "2016-08-08T18:00:00",
        "end": "2016-08-08T19:00:00"
    }
}
```

Zie het artikel over de [Amazon S3-connector](data-factory-amazon-simple-storage-service-connector.md#copy-activity-properties)voor meer informatie.

## <a name="file-system"></a>Bestandssysteem


### <a name="linked-service"></a>Gekoppelde service
U kunt een on-premises bestands systeem koppelen aan een Azure-data factory met de gekoppelde **on-premises Bestands server** . De volgende tabel bevat beschrijvingen van de JSON-elementen die specifiek zijn voor de on-premises gekoppelde service van de bestands server.

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| type |Zorg ervoor dat de eigenschap type is ingesteld op **OnPremisesFileServer**. |Yes |
| host |Hiermee geeft u het pad naar de hoofdmap van de map die u wilt kopiëren. Gebruik het escape teken ' \ ' voor speciale tekens in de teken reeks. Zie voor beelden van gekoppelde service en gegevensset-definities voor voor beeld. |Yes |
| userid |Geef de ID op van de gebruiker die toegang heeft tot de server. |Nee (als u kiest voor encryptedCredential) |
| wachtwoord |Geef het wacht woord op voor de gebruiker (gebruikers-id). |Nee (als u encryptedCredential kiest |
| encryptedCredential |Geef de versleutelde referenties op die u kunt verkrijgen door de cmdlet New-AzDataFactoryEncryptValue uit te voeren. |Nee (als u gebruikers naam en wacht woord als tekst zonder opmaak wilt opgeven) |
| gatewayName |Hiermee geeft u de naam op van de gateway die Data Factory moet gebruiken om verbinding te maken met de on-premises Bestands server. |Yes |

#### <a name="sample-folder-path-definitions"></a>Definities van pad naar voor beeld van map

| Scenario | Host in de definitie van de gekoppelde service | folderPath in de definitie van de gegevensset |
| --- | --- | --- |
| Lokale map op Data Management Gateway computer: <br/><br/>Voor beelden: D: \\ \* of D:\folder\subfolder\\* |D: \\ \\ (voor Data Management Gateway 2,0 en latere versies) <br/><br/> localhost (voor eerdere versies dan Data Management Gateway 2,0) |.\\\\ of submap \\ \\ (voor Data Management Gateway 2,0 en latere versies) <br/><br/>D: \\ \\ of D: \\ \\ submap \\ \\ (voor de gateway versie onder 2,0) |
| Externe gedeelde map: <br/><br/>Voor beelden: \\ \\ mijn \\ map-share \\ \* of \\ \\ mijn server- \\ \\ submap share folder \\\\* |\\\\\\\\share mijn server \\ \\ |.\\\\ of submap \\ \\ |


#### <a name="example-using-username-and-password-in-plain-text"></a>Voor beeld: gebruikers naam en wacht woord gebruiken als tekst zonder opmaak

```json
{
    "Name": "OnPremisesFileServerLinkedService",
    "properties": {
        "type": "OnPremisesFileServer",
        "typeProperties": {
            "host": "\\\\Contosogame-Asia",
            "userid": "Admin",
            "password": "123456",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="example-using-encryptedcredential"></a>Voor beeld: encryptedcredential gebruiken

```json
{
    "Name": " OnPremisesFileServerLinkedService ",
    "properties": {
        "type": "OnPremisesFileServer",
        "typeProperties": {
            "host": "D:\\",
            "encryptedCredential": "WFuIGlzIGRpc3Rpbmd1aXNoZWQsIG5vdCBvbmx5IGJ5xxxxxxxxxxxxxxxxx",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

Zie het [artikel over bestandssysteem connector](data-factory-onprem-file-system-connector.md#linked-service-properties)voor meer informatie.

### <a name="dataset"></a>Gegevensset
Als u een bestands systeem-gegevensset wilt definiëren, stelt u het **type** van de gegevensset in op **share file** en geeft u de volgende eigenschappen op in de sectie **typeProperties** :

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| folderPath |Hiermee geeft u het pad naar de map. Gebruik het escape teken ' \ ' voor speciale tekens in de teken reeks. Zie voor beelden van gekoppelde service en gegevensset-definities voor voor beeld.<br/><br/>U kunt deze eigenschap combi neren met **partitionBy** om mappaden te laten baseren op de begin-en eind datum van het segment. |Yes |
| fileName |Geef de naam van het bestand op in de **FolderPath** als u wilt dat de tabel verwijst naar een specifiek bestand in de map. Als u geen waarde voor deze eigenschap opgeeft, wijst de tabel naar alle bestanden in de map.<br/><br/>Als er geen bestands naam is opgegeven voor een uitvoer gegevensset, de naam van het gegenereerde bestand heeft de volgende indeling: <br/><br/>`Data.<Guid>.txt` (Voor beeld: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt) |No |
| File filter |Geef een filter op dat moet worden gebruikt om een subset van bestanden in de folderPath in plaats van alle bestanden te selecteren. <br/><br/>Toegestane waarden zijn: `*` (meerdere tekens) en `?` (één teken).<br/><br/>Voor beeld 1: "file filter": "*. log"<br/>Voor beeld 2: "file filter": 2016-1-?. txt<br/><br/>File filter is van toepassing op een invoer-file share-gegevensset. |No |
| partitionedBy |U kunt partitionedBy gebruiken om een dynamische folderPath/fileName op te geven voor gegevens van een tijd reeks. Een voor beeld is folderPath para meters voor elk uur aan gegevens. |No |
| indeling | De volgende indelings typen worden ondersteund: **TextFormat**, **JsonFormat**, **Avro Format**, **OrcFormat**, **ParquetFormat**. Stel de eigenschap **type** onder indeling in op een van deze waarden. Zie voor meer informatie secties [tekst indeling](data-factory-supported-file-and-compression-formats.md#text-format), [JSON-indeling](data-factory-supported-file-and-compression-formats.md#json-format), [Avro](data-factory-supported-file-and-compression-formats.md#avro-format)-indeling, [Orc-indeling](data-factory-supported-file-and-compression-formats.md#orc-format)en Parquet- [indeling](data-factory-supported-file-and-compression-formats.md#parquet-format) . <br><br> Als u bestanden wilt **kopiëren als-zich bevindt** tussen archieven op basis van bestanden (binaire kopie), slaat u de sectie indeling in de gegevensset voor invoer en uitvoer over. |No |
| compressie | Geef het type en compressie niveau voor de gegevens op. Ondersteunde typen zijn: **gzip**, **Deflate**, **bzip2** en **ZipDeflate**; en ondersteunde niveaus zijn: **optimaal** en **snelst**. Zie [Bestands-en compressie-indelingen in azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |No |

> [!NOTE]
> U kunt fileName en file filter niet tegelijkertijd gebruiken.

#### <a name="example"></a>Voorbeeld

```json
{
    "name": "OnpremisesFileSystemInput",
    "properties": {
        "type": " FileShare",
        "linkedServiceName": " OnPremisesFileServerLinkedService ",
        "typeProperties": {
            "folderPath": "mysharedfolder/yearno={Year}/monthno={Month}/dayno={Day}",
            "fileName": "{Hour}.csv",
            "partitionedBy": [{
                "name": "Year",
                "value": {
                    "type": "DateTime",
                    "date": "SliceStart",
                        "format": "yyyy"
                }
            }, {
                "name": "Month",
                "value": {
                    "type": "DateTime",
                    "date": "SliceStart",
                    "format": "MM"
                }
            }, {
                "name": "Day",
                "value": {
                    "type": "DateTime",
                    "date": "SliceStart",
                    "format": "dd"
                }
            }, {
                "name": "Hour",
                "value": {
                    "type": "DateTime",
                    "date": "SliceStart",
                    "format": "HH"
                }
            }]
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Zie het [artikel over bestandssysteem connector](data-factory-onprem-file-system-connector.md#dataset-properties)voor meer informatie.

### <a name="file-system-source-in-copy-activity"></a>Bestandssysteem bron in Kopieer activiteit
Als u gegevens uit bestands systeem kopieert, stelt u het **bron type** van de Kopieer activiteit in op **FileSystemSource** en geeft u de volgende eigenschappen op in de sectie **bron** :

| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| recursieve |Geeft aan of de gegevens recursief worden gelezen uit de submappen of alleen vanuit de opgegeven map. |True, False (standaard) |No |

#### <a name="example"></a>Voorbeeld

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2015-06-01T18:00:00",
        "end": "2015-06-01T19:00:00",
        "description": "Pipeline for copy activity",
        "activities": [{
            "name": "OnpremisesFileSystemtoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "OnpremisesFileSystemInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "FileSystemSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
            "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```
Zie het [artikel over bestandssysteem connector](data-factory-onprem-file-system-connector.md#copy-activity-properties)voor meer informatie.

### <a name="file-system-sink-in-copy-activity"></a>Sink van het bestands systeem in de Kopieer activiteit
Als u gegevens naar het bestands systeem kopieert, stelt u het **sink-type** van de Kopieer activiteit in op **FileSystemSink** en geeft u de volgende eigenschappen op in de sectie **sink** :

| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| copyBehavior |Hiermee wordt het Kopieer gedrag gedefinieerd wanneer de bron BlobSource of File System is. |**PreserveHierarchy:** Hiermee behoudt u de bestands hiërarchie in de doelmap. Dat wil zeggen, het relatieve pad van het bron bestand naar de bronmap is hetzelfde als het relatieve pad van het doel bestand naar de doelmap.<br/><br/>**FlattenHierarchy:** Alle bestanden in de bronmap worden gemaakt in het eerste niveau van de doelmap. De doel bestanden worden gemaakt met een automatisch gegenereerde naam.<br/><br/>**MergeFiles:** Alle bestanden van de bronmap worden samengevoegd met één bestand. Als de bestands naam/BLOB-naam is opgegeven, is de naam van het samengevoegde bestand de opgegeven naam. Anders is het een automatisch gegenereerde bestands naam. |No |

balans

#### <a name="example"></a>Voorbeeld

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2015-06-01T18:00:00",
        "end": "2015-06-01T20:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "AzureSQLtoOnPremisesFile",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureSQLInput"
            }],
            "outputs": [{
                "name": "OnpremisesFileSystemOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "SqlSource",
                    "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "FileSystemSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 3,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Zie het [artikel over bestandssysteem connector](data-factory-onprem-file-system-connector.md#copy-activity-properties)voor meer informatie.

## <a name="ftp"></a>FTP

### <a name="linked-service"></a>Gekoppelde service
Als u een gekoppelde FTP-service wilt definiëren, stelt u het **type** van de gekoppelde service in op **FtpServer** en geeft u de volgende eigenschappen op in de sectie **typeProperties** :

| Eigenschap | Beschrijving | Vereist | Standaard |
| --- | --- | --- | --- |
| host |Naam of IP-adres van de FTP-server |Yes |&nbsp; |
| authenticationType |Verificatietype opgeven |Yes |Basic, anoniem |
| gebruikersnaam |Gebruiker die toegang heeft tot de FTP-server |No |&nbsp; |
| wachtwoord |Wacht woord voor de gebruiker (gebruikers naam) |No |&nbsp; |
| encryptedCredential |Versleutelde referentie voor toegang tot de FTP-server |No |&nbsp; |
| gatewayName |De naam van de Data Management Gateway om verbinding te maken met een on-premises FTP-server |No |&nbsp; |
| poort |Poort waarop de FTP-server luistert |No |21 |
| enableSsl |Opgeven of FTP via SSL/TLS-kanaal moet worden gebruikt |No |true |
| enableServerCertificateValidation |Opgeven of de TLS/SSL-certificaat validatie van de server moet worden ingeschakeld wanneer u FTP via SSL/TLS-kanaal gebruikt |No |true |

#### <a name="example-using-anonymous-authentication"></a>Voor beeld: anonieme verificatie gebruiken

```json
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
            "typeProperties": {
            "authenticationType": "Anonymous",
            "host": "myftpserver.com"
        }
    }
}
```

#### <a name="example-using-username-and-password-in-plain-text-for-basic-authentication"></a>Voor beeld: gebruikers naam en wacht woord gebruiken in tekst zonder opmaak voor basis verificatie

```json
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "username": "Admin",
            "password": "123456"
        }
    }
}
```

#### <a name="example-using-port-enablessl-enableservercertificatevalidation"></a>Voor beeld: Using Port, enableSsl, enableServerCertificateValidation

```json
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "username": "Admin",
            "password": "123456",
            "port": "21",
            "enableSsl": true,
            "enableServerCertificateValidation": true
        }
    }
}
```

#### <a name="example-using-encryptedcredential-for-authentication-and-gateway"></a>Voor beeld: encryptedCredential gebruiken voor verificatie en gateway

```json
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "encryptedCredential": "xxxxxxxxxxxxxxxxx",
            "gatewayName": "<onpremgateway>"
        }
      }
}
```

Zie het artikel over de [FTP-connector](data-factory-ftp-connector.md#linked-service-properties) voor meer informatie.

### <a name="dataset"></a>Gegevensset
Als u een FTP-gegevensset wilt definiëren, stelt u het **type** gegevensset in op bestands **share** en geeft u de volgende eigenschappen op in de sectie **typeProperties** :

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| folderPath |Subpad van de map. Escape teken ' \ ' gebruiken voor speciale tekens in de teken reeks. Zie voor beelden van gekoppelde service en gegevensset-definities voor voor beeld.<br/><br/>U kunt deze eigenschap combi neren met **partitionBy** om mappaden te laten baseren op de begin-en eind datum van het segment. |Yes
| fileName |Geef de naam van het bestand op in de **FolderPath** als u wilt dat de tabel verwijst naar een specifiek bestand in de map. Als u geen waarde voor deze eigenschap opgeeft, wijst de tabel naar alle bestanden in de map.<br/><br/>Als er geen bestands naam is opgegeven voor een uitvoer gegevensset, zou de naam van het gegenereerde bestand de volgende indeling hebben: <br/><br/>`Data.<Guid>.txt` (Voor beeld: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt) |No |
| File filter |Geef een filter op dat moet worden gebruikt om een subset van bestanden in de folderPath in plaats van alle bestanden te selecteren.<br/><br/>Toegestane waarden zijn: `*` (meerdere tekens) en `?` (één teken).<br/><br/>Voor beelden 1: `"fileFilter": "*.log"`<br/>Voor beeld 2: `"fileFilter": 2016-1-?.txt"`<br/><br/> File filter is van toepassing op een invoer-file share-gegevensset. Deze eigenschap wordt niet ondersteund met HDFS. |No |
| partitionedBy |partitionedBy kan worden gebruikt om een dynamische folderPath op te geven, filename voor time series-gegevens. Bijvoorbeeld folderPath para meters voor elk uur aan gegevens. |No |
| indeling | De volgende indelings typen worden ondersteund: **TextFormat**, **JsonFormat**, **Avro Format**, **OrcFormat**, **ParquetFormat**. Stel de eigenschap **type** onder indeling in op een van deze waarden. Zie voor meer informatie secties [tekst indeling](data-factory-supported-file-and-compression-formats.md#text-format), [JSON-indeling](data-factory-supported-file-and-compression-formats.md#json-format), [Avro](data-factory-supported-file-and-compression-formats.md#avro-format)-indeling, [Orc-indeling](data-factory-supported-file-and-compression-formats.md#orc-format)en Parquet- [indeling](data-factory-supported-file-and-compression-formats.md#parquet-format) . <br><br> Als u bestanden wilt **kopiëren als-zich bevindt** tussen archieven op basis van bestanden (binaire kopie), slaat u de sectie indeling in de gegevensset voor invoer en uitvoer over. |No |
| compressie | Geef het type en compressie niveau voor de gegevens op. Ondersteunde typen zijn: **gzip**, **Deflate**, **bzip2** en **ZipDeflate**; en ondersteunde niveaus zijn: **optimaal** en **snelst**. Zie [Bestands-en compressie-indelingen in azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support)voor meer informatie. |No |
| useBinaryTransfer |Opgeven of binaire overdrachts modus moet worden gebruikt. True voor binaire modus en ONWAAR ASCII. Standaard waarde: True. Deze eigenschap kan alleen worden gebruikt wanneer het gekoppelde type gekoppelde service van het type: FtpServer. |No |

> [!NOTE]
> bestands naam en file filter kunnen niet tegelijkertijd worden gebruikt.

#### <a name="example"></a>Voorbeeld

```json
{
    "name": "FTPFileInput",
    "properties": {
        "type": "FileShare",
        "linkedServiceName": "FTPLinkedService",
        "typeProperties": {
            "folderPath": "<path to shared folder>",
            "fileName": "test.csv",
            "useBinaryTransfer": true
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

Zie het artikel over de [FTP-connector](data-factory-ftp-connector.md#dataset-properties) voor meer informatie.

### <a name="file-system-source-in-copy-activity"></a>Bestandssysteem bron in Kopieer activiteit
Als u gegevens van een FTP-server kopieert, stelt u het **bron type** van de Kopieer activiteit in op **FileSystemSource** en geeft u de volgende eigenschappen op in de sectie **bron** :

| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| recursieve |Geeft aan of de gegevens recursief worden gelezen uit de submappen of alleen vanuit de opgegeven map. |True, False (standaard) |No |

#### <a name="example"></a>Voorbeeld

```json
{
    "name": "pipeline",
    "properties": {
        "activities": [{
            "name": "FTPToBlobCopy",
            "inputs": [{
                "name": "FtpFileInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "FileSystemSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "NewestFirst",
                "retry": 1,
                "timeout": "00:05:00"
            }
        }],
        "start": "2016-08-24T18:00:00",
        "end": "2016-08-24T19:00:00"
    }
}
```

Zie het artikel over de [FTP-connector](data-factory-ftp-connector.md#copy-activity-properties) voor meer informatie.


## <a name="hdfs"></a>HDFS

### <a name="linked-service"></a>Gekoppelde service
Als u een gekoppelde koppelings service van HDFS wilt definiëren, stelt u het **type** van de gekoppelde service in op **HDFS** en geeft u de volgende eigenschappen op in de sectie **typeProperties** :

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| type |De eigenschap type moet worden ingesteld op: **Hdfs** |Yes |
| URL |URL naar HDFS |Yes |
| authenticationType |Anoniem of Windows. <br><br> Als u **Kerberos-verificatie** wilt gebruiken voor HDFS-connector, raadpleegt u deze sectie om uw on-premises omgeving dienovereenkomstig in te stellen. |Yes |
| userName |Gebruikers naam voor Windows-verificatie. |Ja (voor Windows-verificatie) |
| wachtwoord |Wacht woord voor Windows-verificatie. |Ja (voor Windows-verificatie) |
| gatewayName |De naam van de gateway die de Data Factory-service moet gebruiken om verbinding te maken met de HDFS. |Yes |
| encryptedCredential |[New-AzDataFactoryEncryptValue-](/powershell/module/az.datafactory/new-azdatafactoryencryptvalue) uitvoer van de toegangs referentie. |No |

#### <a name="example-using-anonymous-authentication"></a>Voor beeld: anonieme verificatie gebruiken

```json
{
    "name": "HDFSLinkedService",
    "properties": {
        "type": "Hdfs",
        "typeProperties": {
            "authenticationType": "Anonymous",
            "userName": "hadoop",
            "url": "http://<machine>:50070/webhdfs/v1/",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="example-using-windows-authentication"></a>Voor beeld: Windows-verificatie gebruiken

```json
{
    "name": "HDFSLinkedService",
    "properties": {
        "type": "Hdfs",
        "typeProperties": {
            "authenticationType": "Windows",
            "userName": "Administrator",
            "password": "password",
            "url": "http://<machine>:50070/webhdfs/v1/",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

Zie voor meer informatie het artikel HDFS connector.

### <a name="dataset"></a>Gegevensset
Als u een HDFS-gegevensset wilt definiëren, stelt u het **type** van de gegevensset in op bestands **share** en geeft u de volgende eigenschappen op in de sectie **typeProperties** :

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| folderPath |Pad naar de map. Voorbeeld: `myfolder`<br/><br/>Escape teken ' \ ' gebruiken voor speciale tekens in de teken reeks. Bijvoorbeeld: voor folder\subfolder geeft u een submap op \\ \\ en voor d:\samplefolder, geeft u d: \\ \\ SampleFolder op.<br/><br/>U kunt deze eigenschap combi neren met **partitionBy** om mappaden te laten baseren op de begin-en eind datum van het segment. |Yes |
| fileName |Geef de naam van het bestand op in de **FolderPath** als u wilt dat de tabel verwijst naar een specifiek bestand in de map. Als u geen waarde voor deze eigenschap opgeeft, wijst de tabel naar alle bestanden in de map.<br/><br/>Als er geen bestands naam is opgegeven voor een uitvoer gegevensset, zou de naam van het gegenereerde bestand de volgende indeling hebben: <br/><br/>`Data.<Guid>.txt` (bijvoorbeeld:: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |No |
| partitionedBy |partitionedBy kan worden gebruikt om een dynamische folderPath op te geven, filename voor time series-gegevens. Voor beeld: folderPath para meters voor elk uur aan gegevens. |No |
| indeling | De volgende indelings typen worden ondersteund: **TextFormat**, **JsonFormat**, **Avro Format**, **OrcFormat**, **ParquetFormat**. Stel de eigenschap **type** onder indeling in op een van deze waarden. Zie voor meer informatie secties [tekst indeling](data-factory-supported-file-and-compression-formats.md#text-format), [JSON-indeling](data-factory-supported-file-and-compression-formats.md#json-format), [Avro](data-factory-supported-file-and-compression-formats.md#avro-format)-indeling, [Orc-indeling](data-factory-supported-file-and-compression-formats.md#orc-format)en Parquet- [indeling](data-factory-supported-file-and-compression-formats.md#parquet-format) . <br><br> Als u bestanden wilt **kopiëren als-zich bevindt** tussen archieven op basis van bestanden (binaire kopie), slaat u de sectie indeling in de gegevensset voor invoer en uitvoer over. |No |
| compressie | Geef het type en compressie niveau voor de gegevens op. Ondersteunde typen zijn: **gzip**, **Deflate**, **bzip2** en **ZipDeflate**. Ondersteunde niveaus zijn: **optimaal** en **snelst**. Zie [Bestands-en compressie-indelingen in azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support)voor meer informatie. |No |

> [!NOTE]
> bestands naam en file filter kunnen niet tegelijkertijd worden gebruikt.

#### <a name="example"></a>Voorbeeld

```json
{
    "name": "InputDataset",
    "properties": {
        "type": "FileShare",
        "linkedServiceName": "HDFSLinkedService",
        "typeProperties": {
            "folderPath": "DataTransfer/UnitTest/"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

Zie voor meer informatie het artikel HDFS connector.

### <a name="file-system-source-in-copy-activity"></a>Bestandssysteem bron in Kopieer activiteit
Als u gegevens kopieert van HDFS, stelt u het **bron type** van de Kopieer activiteit in op **FileSystemSource** en geeft u de volgende eigenschappen op in de sectie **bron** :

**FileSystemSource** ondersteunt de volgende eigenschappen:

| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| recursieve |Geeft aan of de gegevens recursief worden gelezen uit de submappen of alleen vanuit de opgegeven map. |True, False (standaard) |No |

#### <a name="example"></a>Voorbeeld

```json
{
    "name": "pipeline",
    "properties": {
        "activities": [{
            "name": "HdfsToBlobCopy",
            "inputs": [{
                "name": "InputDataset"
            }],
            "outputs": [{
                "name": "OutputDataset"
            }],
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "FileSystemSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "NewestFirst",
                "retry": 1,
                "timeout": "00:05:00"
            }
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```

Zie voor meer informatie het artikel HDFS connector.

## <a name="sftp"></a>SFTP


### <a name="linked-service"></a>Gekoppelde service
Als u een gekoppelde SFTP-service wilt definiëren, stelt u het **type** van de gekoppelde service in op **SFTP** en geeft u de volgende eigenschappen op in de sectie **typeProperties** :

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| host | Naam of IP-adres van de SFTP-server. |Yes |
| poort |Poort waarop de SFTP-server luistert. De standaard waarde is: 21 |No |
| authenticationType |Geef het verificatie type op. Toegestane waarden: **Basic**, **SshPublicKey**. <br><br> Raadpleeg de sectie basis verificatie en [verificatie van open bare SSH-sleutel](#using-ssh-public-key-authentication) voor meer informatie over de eigenschappen en JSON-voor beelden. |Yes |
| skipHostKeyValidation | Geef op of de validatie van de host-sleutel moet worden overgeslagen. | Nee. De standaard waarde: False |
| hostKeyFingerprint | Geef de vinger afdruk van de host-sleutel op. | Ja als de `skipHostKeyValidation` is ingesteld op false.  |
| gatewayName |De naam van de Data Management Gateway om verbinding te maken met een on-premises SFTP-server. | Ja bij het kopiëren van gegevens van een on-premises SFTP-server. |
| encryptedCredential | Versleutelde referentie voor toegang tot de SFTP-server. Automatisch gegenereerd wanneer u basis verificatie (gebruikers naam en wacht woord) of SshPublicKey-verificatie (gebruikers naam + persoonlijke sleutel of inhoud) in de wizard kopiëren of het pop-updialoogvenster van de ClickOnce opgeeft. | Nee. Alleen Toep assen bij het kopiëren van gegevens van een on-premises SFTP-server. |

#### <a name="example-using-basic-authentication"></a>Voor beeld: basis verificatie gebruiken

Als u basis verificatie wilt gebruiken, stelt u `authenticationType` in op `Basic` en geeft u de volgende eigenschappen op, naast de SFTP-connector generics die zijn geïntroduceerd in de laatste sectie:

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| gebruikersnaam | Gebruiker die toegang heeft tot de SFTP-server. |Yes |
| wachtwoord | Wacht woord voor de gebruiker (gebruikers naam). | Yes |

```json
{
    "name": "SftpLinkedService",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<SFTP server name or IP address>",
            "port": 22,
            "authenticationType": "Basic",
            "username": "xxx",
            "password": "xxx",
            "skipHostKeyValidation": false,
            "hostKeyFingerPrint": "ssh-rsa 2048 xx:00:00:00:xx:00:x0:0x:0x:0x:0x:00:00:x0:x0:00",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="example-basic-authentication-with-encrypted-credential"></a>Voor beeld: **basis verificatie met versleutelde referentie**

```json
{
    "name": "SftpLinkedService",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<FTP server name or IP address>",
            "port": 22,
            "authenticationType": "Basic",
            "username": "xxx",
            "encryptedCredential": "xxxxxxxxxxxxxxxxx",
            "skipHostKeyValidation": false,
            "hostKeyFingerPrint": "ssh-rsa 2048 xx:00:00:00:xx:00:x0:0x:0x:0x:0x:00:00:x0:x0:00",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="using-ssh-public-key-authentication"></a>**Verificatie met open bare SSH-sleutel gebruiken:**

Als u basis verificatie wilt gebruiken, stelt u `authenticationType` in op `SshPublicKey` en geeft u de volgende eigenschappen op, naast de SFTP-connector generics die zijn geïntroduceerd in de laatste sectie:

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| gebruikersnaam |Gebruiker die toegang heeft tot de SFTP-server |Yes |
| privateKeyPath | Geef een absoluut pad naar het bestand met de persoonlijke sleutel op waartoe de gateway toegang kan hebben. | Geef de `privateKeyPath` or op `privateKeyContent` . <br><br> Alleen Toep assen bij het kopiëren van gegevens van een on-premises SFTP-server. |
| privateKeyContent | Een geserialiseerde teken reeks met de inhoud van de persoonlijke sleutel. De wizard kopiëren kan het persoonlijke sleutel bestand lezen en de inhoud van de persoonlijke sleutel automatisch extra heren. Als u een andere tool/SDK gebruikt, gebruikt u in plaats daarvan de eigenschap privateKeyPath. | Geef de `privateKeyPath` or op `privateKeyContent` . |
| Wachtzin | Geef de wachtwoordzin/het wacht woord op voor het ontsleutelen van de persoonlijke sleutel als het sleutel bestand wordt beveiligd door een wachtwoordzin. | Ja als het persoonlijke-sleutel bestand is beveiligd door een wachtwoordzin. |

```json
{
    "name": "SftpLinkedServiceWithPrivateKeyPath",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<FTP server name or IP address>",
            "port": 22,
            "authenticationType": "SshPublicKey",
            "username": "xxx",
            "privateKeyPath": "D:\\privatekey_openssh",
            "passPhrase": "xxx",
            "skipHostKeyValidation": true,
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="example-sshpublickey-authentication-using-private-key-content"></a>Voor beeld: **SshPublicKey-verificatie met de inhoud van een persoonlijke sleutel**

```json
{
    "name": "SftpLinkedServiceWithPrivateKeyContent",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "mysftpserver.westus.cloudapp.azure.com",
            "port": 22,
            "authenticationType": "SshPublicKey",
            "username": "xxx",
            "privateKeyContent": "<base64 string of the private key content>",
            "passPhrase": "xxx",
            "skipHostKeyValidation": true
        }
    }
}
```

Zie voor meer informatie het artikel [SFTP connector](data-factory-sftp-connector.md#linked-service-properties) .

### <a name="dataset"></a>Gegevensset
Als u een SFTP-gegevensset wilt definiëren, stelt u het **type** van de gegevensset in op bestands **share** en geeft u de volgende eigenschappen op in de sectie **typeProperties** :

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| folderPath |Subpad van de map. Escape teken ' \ ' gebruiken voor speciale tekens in de teken reeks. Zie voor beelden van gekoppelde service en gegevensset-definities voor voor beeld.<br/><br/>U kunt deze eigenschap combi neren met **partitionBy** om mappaden te laten baseren op de begin-en eind datum van het segment. |Yes |
| fileName |Geef de naam van het bestand op in de **FolderPath** als u wilt dat de tabel verwijst naar een specifiek bestand in de map. Als u geen waarde voor deze eigenschap opgeeft, wijst de tabel naar alle bestanden in de map.<br/><br/>Als er geen bestands naam is opgegeven voor een uitvoer gegevensset, zou de naam van het gegenereerde bestand de volgende indeling hebben: <br/><br/>`Data.<Guid>.txt` (Voor beeld: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt) |No |
| File filter |Geef een filter op dat moet worden gebruikt om een subset van bestanden in de folderPath in plaats van alle bestanden te selecteren.<br/><br/>Toegestane waarden zijn: `*` (meerdere tekens) en `?` (één teken).<br/><br/>Voor beelden 1: `"fileFilter": "*.log"`<br/>Voor beeld 2: `"fileFilter": 2016-1-?.txt"`<br/><br/> File filter is van toepassing op een invoer-file share-gegevensset. Deze eigenschap wordt niet ondersteund met HDFS. |No |
| partitionedBy |partitionedBy kan worden gebruikt om een dynamische folderPath op te geven, filename voor time series-gegevens. Bijvoorbeeld folderPath para meters voor elk uur aan gegevens. |No |
| indeling | De volgende indelings typen worden ondersteund: **TextFormat**, **JsonFormat**, **Avro Format**, **OrcFormat**, **ParquetFormat**. Stel de eigenschap **type** onder indeling in op een van deze waarden. Zie voor meer informatie secties [tekst indeling](data-factory-supported-file-and-compression-formats.md#text-format), [JSON-indeling](data-factory-supported-file-and-compression-formats.md#json-format), [Avro](data-factory-supported-file-and-compression-formats.md#avro-format)-indeling, [Orc-indeling](data-factory-supported-file-and-compression-formats.md#orc-format)en Parquet- [indeling](data-factory-supported-file-and-compression-formats.md#parquet-format) . <br><br> Als u bestanden wilt **kopiëren als-zich bevindt** tussen archieven op basis van bestanden (binaire kopie), slaat u de sectie indeling in de gegevensset voor invoer en uitvoer over. |No |
| compressie | Geef het type en compressie niveau voor de gegevens op. Ondersteunde typen zijn: **gzip**, **Deflate**, **bzip2** en **ZipDeflate**. Ondersteunde niveaus zijn: **optimaal** en **snelst**. Zie [Bestands-en compressie-indelingen in azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support)voor meer informatie. |No |
| useBinaryTransfer |Opgeven of binaire overdrachts modus moet worden gebruikt. True voor binaire modus en ONWAAR ASCII. Standaard waarde: True. Deze eigenschap kan alleen worden gebruikt wanneer het gekoppelde type gekoppelde service van het type: FtpServer. |No |

> [!NOTE]
> bestands naam en file filter kunnen niet tegelijkertijd worden gebruikt.

#### <a name="example"></a>Voorbeeld

```json
{
    "name": "SFTPFileInput",
    "properties": {
        "type": "FileShare",
        "linkedServiceName": "SftpLinkedService",
        "typeProperties": {
            "folderPath": "<path to shared folder>",
            "fileName": "test.csv"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

Zie voor meer informatie het artikel [SFTP connector](data-factory-sftp-connector.md#dataset-properties) .

### <a name="file-system-source-in-copy-activity"></a>Bestandssysteem bron in Kopieer activiteit
Als u gegevens van een SFTP-bron kopieert, stelt u het **bron type** van de Kopieer activiteit in op **FileSystemSource** en geeft u de volgende eigenschappen op in de sectie **bron** :

| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| recursieve |Geeft aan of de gegevens recursief worden gelezen uit de submappen of alleen vanuit de opgegeven map. |True, False (standaard) |No |



#### <a name="example"></a>Voorbeeld

```json
{
    "name": "pipeline",
    "properties": {
        "activities": [{
            "name": "SFTPToBlobCopy",
            "inputs": [{
                "name": "SFTPFileInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "FileSystemSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "NewestFirst",
                "retry": 1,
                "timeout": "00:05:00"
            }
        }],
        "start": "2017-02-20T18:00:00",
        "end": "2017-02-20T19:00:00"
    }
}
```

Zie voor meer informatie het artikel [SFTP connector](data-factory-sftp-connector.md#copy-activity-properties) .


## <a name="http"></a>HTTP

### <a name="linked-service"></a>Gekoppelde service
Als u een gekoppelde HTTP-service wilt definiëren, stelt u het **type** van de gekoppelde service in op **http** en geeft u de volgende eigenschappen op in de sectie **typeProperties** :

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| URL | Basis-URL naar de webserver | Yes |
| authenticationType | Hiermee geeft u het verificatie type op. Toegestane waarden zijn: **Anonymous**, **Basic**, **Digest**, **Windows**, **ClientCertificate**. <br><br> Raadpleeg de secties onder deze tabel voor meer eigenschappen en JSON-voor beelden voor deze verificatie typen. | Yes |
| enableServerCertificateValidation | Opgeven of de TLS/SSL-certificaat validatie van de server moet worden ingeschakeld als de bron HTTPS-webserver is | Nee, standaard waarde is waar |
| gatewayName | De naam van de Data Management Gateway om verbinding te maken met een on-premises HTTP-bron. | Ja bij het kopiëren van gegevens uit een on-premises HTTP-bron. |
| encryptedCredential | Versleutelde referentie voor toegang tot het HTTP-eind punt. Automatisch gegenereerd wanneer u de verificatie gegevens configureert in de wizard kopiëren of in het pop-updialoogvenster van de ClickOnce. | Nee. Alleen Toep assen bij het kopiëren van gegevens van een on-premises HTTP-server. |

#### <a name="example-using-basic-digest-or-windows-authentication"></a>Voor beeld: Basic, Digest of Windows-verificatie gebruiken
Stel `authenticationType` in als `Basic` , `Digest` , of `Windows` , en geef de volgende eigenschappen op naast de http-connector generics die hierboven zijn geïntroduceerd:

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| gebruikersnaam | Gebruikers naam voor toegang tot het HTTP-eind punt. | Yes |
| wachtwoord | Wacht woord voor de gebruiker (gebruikers naam). | Yes |

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "Http",
        "typeProperties": {
            "authenticationType": "basic",
            "url": "https://en.wikipedia.org/wiki/",
            "userName": "user name",
            "password": "password"
        }
    }
}
```

#### <a name="example-using-clientcertificate-authentication"></a>Voor beeld: met behulp van ClientCertificate-verificatie

Als u basis verificatie wilt gebruiken, stelt u `authenticationType` in op `ClientCertificate` en geeft u de volgende eigenschappen op, naast de http-connector generieke items die hierboven zijn geïntroduceerd:

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| embeddedCertData | De met base64 gecodeerde inhoud van binaire gegevens van het PFX-bestand (Personal Information Exchange). | Geef de `embeddedCertData` or op `certThumbprint` . |
| certThumbprint | De vinger afdruk van het certificaat dat is geïnstalleerd op het certificaat archief van uw gateway computer. Alleen Toep assen bij het kopiëren van gegevens uit een on-premises HTTP-bron. | Geef de `embeddedCertData` or op `certThumbprint` . |
| wachtwoord | Het wacht woord dat is gekoppeld aan het certificaat. | No |

Als u `certThumbprint` voor verificatie gebruikt en het certificaat is geïnstalleerd in het persoonlijke archief van de lokale computer, moet u de machtiging lezen verlenen aan de Gateway Service:

1. Start micro soft Management Console (MMC). Voeg de module **certificaten** toe die gericht is op de **lokale computer**.
2. Vouw **certificaten** uit, **persoonlijk** en klik op **certificaten**.
3. Klik met de rechter muisknop op het certificaat in het persoonlijke archief en selecteer **alle taken** -> **persoonlijke sleutels beheren...**
3. Voeg op het tabblad **beveiliging** het gebruikers account toe waaronder Data Management gateway host-service wordt uitgevoerd met de lees toegang tot het certificaat.

**Voor beeld: client certificaat gebruiken:** Deze gekoppelde service koppelt uw data factory aan een on-premises HTTP-webserver. Er wordt een client certificaat gebruikt dat is geïnstalleerd op de computer waarop Data Management Gateway is geïnstalleerd.

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "Http",
        "typeProperties": {
            "authenticationType": "ClientCertificate",
            "url": "https://en.wikipedia.org/wiki/",
            "certThumbprint": "thumbprint of certificate",
            "gatewayName": "gateway name"
        }
    }
}
```

#### <a name="example-using-client-certificate-in-a-file"></a>Voor beeld: client certificaat gebruiken in een bestand
Deze gekoppelde service koppelt uw data factory aan een on-premises HTTP-webserver. Er wordt een client certificaat bestand gebruikt op de computer waarop Data Management Gateway is geïnstalleerd.

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "Http",
        "typeProperties": {
            "authenticationType": "ClientCertificate",
            "url": "https://en.wikipedia.org/wiki/",
            "embeddedCertData": "base64 encoded cert data",
            "password": "password of cert"
        }
    }
}
```

Zie het artikel [http-connector](data-factory-http-connector.md#linked-service-properties) voor meer informatie.

### <a name="dataset"></a>Gegevensset
Als u een HTTP-gegevensset wilt definiëren, stelt u het **type** van de gegevensset in op **http** en geeft u de volgende eigenschappen op in de sectie **typeProperties** :

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| relativeUrl | Een relatieve URL naar de resource die de gegevens bevat. Wanneer pad niet is opgegeven, wordt alleen de URL gebruikt die is opgegeven in de definitie van de gekoppelde service. <br><br> Als u een dynamische URL wilt maken, kunt u [Data Factory functies en systeem variabelen](data-factory-functions-variables.md)gebruiken, bijvoorbeeld: `"relativeUrl": "$$Text.Format('/my/report?month={0:yyyy}-{0:MM}&fmt=csv', SliceStart)"` . | No |
| requestMethod | Http-methode. Toegestane waarden zijn **Get** of **post**. | Nee. De standaardinstelling is `GET`. |
| additionalHeaders | Aanvullende HTTP-aanvraag headers. | No |
| requestBody | Hoofd tekst voor HTTP-aanvraag. | No |
| indeling | Als u **de gegevens van het HTTP-eind punt net als-** als wilt ophalen zonder deze te parseren, slaat u deze indelings instellingen over. <br><br> Als u de inhoud van het HTTP-antwoord tijdens het kopiëren wilt parseren, worden de volgende indelings typen ondersteund: **TextFormat**, **JsonFormat**, **Avro Format**, **OrcFormat**, **ParquetFormat**. Zie voor meer informatie secties [tekst indeling](data-factory-supported-file-and-compression-formats.md#text-format), [JSON-indeling](data-factory-supported-file-and-compression-formats.md#json-format), [Avro](data-factory-supported-file-and-compression-formats.md#avro-format)-indeling, [Orc-indeling](data-factory-supported-file-and-compression-formats.md#orc-format)en Parquet- [indeling](data-factory-supported-file-and-compression-formats.md#parquet-format) . |No |
| compressie | Geef het type en compressie niveau voor de gegevens op. Ondersteunde typen zijn: **gzip**, **Deflate**, **bzip2** en **ZipDeflate**. Ondersteunde niveaus zijn: **optimaal** en **snelst**. Zie [Bestands-en compressie-indelingen in azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support)voor meer informatie. |No |

#### <a name="example-using-the-get-default-method"></a>Voor beeld: de methode GET (standaard) gebruiken

```json
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "Http",
        "linkedServiceName": "HttpLinkedService",
        "typeProperties": {
            "relativeUrl": "XXX/test.xml",
            "additionalHeaders": "Connection: keep-alive\nUser-Agent: Mozilla/5.0\n"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

#### <a name="example-using-the-post-method"></a>Voor beeld: de methode POST gebruiken

```json
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "Http",
        "linkedServiceName": "HttpLinkedService",
        "typeProperties": {
            "relativeUrl": "/XXX/test.xml",
            "requestMethod": "Post",
            "requestBody": "body for POST HTTP request"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```
Zie het artikel [http-connector](data-factory-http-connector.md#dataset-properties) voor meer informatie.

### <a name="http-source-in-copy-activity"></a>HTTP-bron in Kopieer activiteit
Als u gegevens uit een HTTP-bron kopieert, stelt u het **bron type** van de Kopieer activiteit in op **http** en geeft u de volgende eigenschappen op in de sectie **bron** :

| Eigenschap | Beschrijving | Vereist |
| -------- | ----------- | -------- |
| httpRequestTimeout | De time-out (time span) voor de HTTP-aanvraag om een antwoord te krijgen. Het is de time-out om een antwoord te krijgen, niet de time-out voor het lezen van antwoord gegevens. | Nee. Standaard waarde: 00:01:40 |


#### <a name="example"></a>Voorbeeld

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "HttpSourceToAzureBlob",
            "description": "Copy from an HTTP source to an Azure blob",
            "type": "Copy",
            "inputs": [{
                "name": "HttpSourceDataInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "HttpSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Zie het artikel [http-connector](data-factory-http-connector.md#copy-activity-properties) voor meer informatie.

## <a name="odata"></a>OData

### <a name="linked-service"></a>Gekoppelde service
Als u een gekoppelde OData-service wilt definiëren, stelt u het **type** van de gekoppelde service in op **odata** en geeft u de volgende eigenschappen op in de sectie **typeProperties** :

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| URL |De URL van de OData-service. |Yes |
| authenticationType |Type verificatie dat wordt gebruikt om verbinding te maken met de OData-bron. <br/><br/> Voor Cloud OData zijn mogelijke waarden anoniem, basis en OAuth (Opmerking Azure Data Factory momenteel alleen op Azure Active Directory gebaseerde OAuth). <br/><br/> Voor on-premises OData zijn mogelijke waarden anoniem, basis en Windows. |Yes |
| gebruikersnaam |Geef de gebruikers naam op als u basis verificatie gebruikt. |Ja (alleen als u basis verificatie gebruikt) |
| wachtwoord |Geef het wacht woord op voor het gebruikers account dat u hebt opgegeven voor de gebruikers naam. |Ja (alleen als u basis verificatie gebruikt) |
| authorizedCredential |Als u OAuth gebruikt, klikt u op de knop **machtigen** in de wizard Data Factory kopiëren of de editor en voert u uw referenties in. vervolgens wordt de waarde van deze eigenschap automatisch gegenereerd. |Ja (alleen als u OAuth-verificatie gebruikt) |
| gatewayName |De naam van de gateway die de Data Factory-service moet gebruiken om verbinding te maken met de on-premises OData-service. Geef alleen op als u gegevens kopieert uit on-premises OData-bron. |No |

#### <a name="example---using-basic-authentication"></a>Voor beeld-basis verificatie gebruiken
```json
{
    "name": "inputLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "https://services.odata.org/OData/OData.svc",
            "authenticationType": "Basic",
            "username": "username",
            "password": "password"
        }
    }
}
```

#### <a name="example---using-anonymous-authentication"></a>Voor beeld-anonieme verificatie gebruiken

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "https://services.odata.org/OData/OData.svc",
            "authenticationType": "Anonymous"
        }
    }
}
```

#### <a name="example---using-windows-authentication-accessing-on-premises-odata-source"></a>Voor beeld: Windows-verificatie gebruiken om de on-premises OData-bron te openen

```json
{
    "name": "inputLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "<endpoint of on-premises OData source, for example, Dynamics CRM>",
            "authenticationType": "Windows",
            "username": "domain\\user",
            "password": "password",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="example---using-oauth-authentication-accessing-cloud-odata-source"></a>Voor beeld-OAuth-authenticatie gebruiken toegang tot de Cloud-OData-bron
```json
{
    "name": "inputLinkedService",
    "properties":
    {
        "type": "OData",
            "typeProperties":
        {
            "url": "<endpoint of cloud OData source, for example, https://<tenant>.crm.dynamics.com/XRMServices/2011/OrganizationData.svc>",
            "authenticationType": "OAuth",
            "authorizedCredential": "<auto generated by clicking the Authorize button on UI>"
        }
    }
}
```

Zie [OData-connector](data-factory-odata-connector.md#linked-service-properties) artikel voor meer informatie.

### <a name="dataset"></a>Gegevensset
Als u een OData-gegevensset wilt definiëren, stelt u het **type** van de gegevensset in op **ODataResource** en geeft u de volgende eigenschappen op in de sectie **typeProperties** :

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| leertraject |Pad naar de OData-resource |No |

#### <a name="example"></a>Voorbeeld

```json
{
    "name": "ODataDataset",
    "properties": {
        "type": "ODataResource",
        "typeProperties": {
            "path": "Products"
        },
        "linkedServiceName": "ODataLinkedService",
        "structure": [],
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "retryInterval": "00:01:00",
            "retryTimeout": "00:10:00",
            "maximumRetry": 3
        }
    }
}
```

Zie [OData-connector](data-factory-odata-connector.md#dataset-properties) artikel voor meer informatie.

### <a name="relational-source-in-copy-activity"></a>Relationele bron in Kopieer activiteit
Als u gegevens uit een OData-bron kopieert, stelt u het **bron type** van de Kopieer activiteit in op **RelationalSource** en geeft u de volgende eigenschappen op in de sectie **bron** :

| Eigenschap | Beschrijving | Voorbeeld | Vereist |
| --- | --- | --- | --- |
| query |Gebruik de aangepaste query om gegevens te lezen. |"? $select = naam, beschrijving&$top = 5" |No |

#### <a name="example"></a>Voorbeeld

```json
{
    "name": "CopyODataToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "?$select=Name, Description&$top=5"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "ODataDataSet"
            }],
            "outputs": [{
                "name": "AzureBlobODataDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "ODataToBlob"
        }],
        "start": "2017-02-01T18:00:00",
        "end": "2017-02-03T19:00:00"
    }
}
```

Zie [OData-connector](data-factory-odata-connector.md#copy-activity-properties) artikel voor meer informatie.


## <a name="odbc"></a>ODBC


### <a name="linked-service"></a>Gekoppelde service
Als u een gekoppelde ODBC-service wilt definiëren, stelt u het **type** van de gekoppelde service in op **OnPremisesOdbc** en geeft u de volgende eigenschappen op in de sectie **typeProperties** :

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| connectionString |Het referentie deel voor niet-toegang van de connection string en een optionele versleutelde referentie. Zie de voor beelden in de volgende secties. |Yes |
| referenties |Het deel van de toegangs referentie van de connection string dat is opgegeven in de eigenschaps waarde-indeling van het stuur programma. Voorbeeld: `“Uid=<user ID>;Pwd=<password>;RefreshToken=<secret refresh token>;”.` |No |
| authenticationType |Type verificatie dat wordt gebruikt om verbinding te maken met het ODBC-gegevens archief. Mogelijke waarden zijn: anoniem en basis. |Yes |
| gebruikersnaam |Geef de gebruikers naam op als u basis verificatie gebruikt. |No |
| wachtwoord |Geef het wacht woord op voor het gebruikers account dat u hebt opgegeven voor de gebruikers naam. |No |
| gatewayName |De naam van de gateway die de Data Factory-service moet gebruiken om verbinding te maken met het ODBC-gegevens archief. |Yes |

#### <a name="example---using-basic-authentication"></a>Voor beeld-basis verificatie gebruiken

```json
{
    "name": "ODBCLinkedService",
    "properties": {
        "type": "OnPremisesOdbc",
        "typeProperties": {
            "authenticationType": "Basic",
            "connectionString": "Driver={SQL Server};Server=Server.database.windows.net; Database=TestDatabase;",
            "userName": "username",
            "password": "password",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```
#### <a name="example---using-basic-authentication-with-encrypted-credentials"></a>Voor beeld: basis verificatie gebruiken met versleutelde referenties
U kunt de referenties versleutelen met de cmdlet [New-AzDataFactoryEncryptValue](/powershell/module/az.datafactory/new-azdatafactoryencryptvalue) .

```json
{
    "name": "ODBCLinkedService",
    "properties": {
        "type": "OnPremisesOdbc",
        "typeProperties": {
            "authenticationType": "Basic",
            "connectionString": "Driver={SQL Server};Server=myserver.database.windows.net; Database=TestDatabase;;EncryptedCredential=eyJDb25uZWN0...........................",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="example-using-anonymous-authentication"></a>Voor beeld: anonieme verificatie gebruiken

```json
{
    "name": "ODBCLinkedService",
    "properties": {
        "type": "OnPremisesOdbc",
        "typeProperties": {
            "authenticationType": "Anonymous",
            "connectionString": "Driver={SQL Server};Server={servername}.database.windows.net; Database=TestDatabase;",
            "credential": "UID={uid};PWD={pwd}",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

Zie voor meer informatie het artikel over [ODBC-connectors](data-factory-odbc-connector.md#linked-service-properties) .

### <a name="dataset"></a>Gegevensset
Als u een ODBC-gegevensset wilt definiëren, stelt u het **type** van de gegevensset in op **RelationalTable** en geeft u de volgende eigenschappen op in de sectie **typeProperties** :

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| tableName |De naam van de tabel in het ODBC-gegevens archief. |Yes |


#### <a name="example"></a>Voorbeeld

```json
{
    "name": "ODBCDataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "ODBCLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Zie voor meer informatie het artikel over [ODBC-connectors](data-factory-odbc-connector.md#dataset-properties) .

### <a name="relational-source-in-copy-activity"></a>Relationele bron in Kopieer activiteit
Als u gegevens uit een ODBC-gegevens archief kopieert, stelt u het **bron type** van de Kopieer activiteit in op **RelationalSource** en geeft u de volgende eigenschappen op in de sectie **bron** :

| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| query |Gebruik de aangepaste query om gegevens te lezen. |SQL-query teken reeks. Bijvoorbeeld: `select * from MyTable`. |Yes |

#### <a name="example"></a>Voorbeeld

```json
{
    "name": "CopyODBCToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "OdbcDataSet"
            }],
            "outputs": [{
                "name": "AzureBlobOdbcDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "OdbcToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```

Zie voor meer informatie het artikel over [ODBC-connectors](data-factory-odbc-connector.md#copy-activity-properties) .

## <a name="salesforce"></a>SalesForce


### <a name="linked-service"></a>Gekoppelde service
Als u een gekoppelde service voor Sales Force wilt definiëren, stelt u het **type** van de gekoppelde service in op **Sales Force** en geeft u de volgende eigenschappen op in de sectie **typeProperties** :

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| environmentUrl | Geef de URL van het Sales Force-exemplaar op. <br><br> -Standaard is ' https://login.salesforce.com '. <br> -Geef "" op om gegevens uit de sandbox te kopiëren https://test.salesforce.com . <br> -Als u gegevens wilt kopiëren uit een aangepast domein, geeft u bijvoorbeeld ' https://[domain]. mijn. Sales Force. com ' op. |No |
| gebruikersnaam |Geef een gebruikers naam op voor het gebruikers account. |Yes |
| wachtwoord |Geef een wacht woord op voor het gebruikers account. |Yes |
| Security |Geef een beveiligings token op voor het gebruikers account. Zie [beveiligings Token ophalen](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm) voor instructies over het opnieuw instellen/ophalen van een beveiligings token. Zie [beveiliging en de API](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_concepts_security.htm)voor meer informatie over beveiligings tokens in het algemeen. |Yes |

#### <a name="example"></a>Voorbeeld

```json
{
    "name": "SalesforceLinkedService",
    "properties": {
        "type": "Salesforce",
        "typeProperties": {
            "username": "<user name>",
            "password": "<password>",
            "securityToken": "<security token>"
        }
    }
}
```

Zie het artikel [Sales Force-connector](data-factory-salesforce-connector.md#linked-service-properties) voor meer informatie.

### <a name="dataset"></a>Gegevensset
Als u een Sales Force-gegevensset wilt definiëren, stelt u het **type** van de gegevensset in op **RelationalTable** en geeft u de volgende eigenschappen op in de sectie **typeProperties** :

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| tableName |De naam van de tabel in Sales Force. |Nee (als er een **query** van **RelationalSource** is opgegeven) |

#### <a name="example"></a>Voorbeeld

```json
{
    "name": "SalesforceInput",
    "properties": {
        "linkedServiceName": "SalesforceLinkedService",
        "type": "RelationalTable",
        "typeProperties": {
            "tableName": "AllDataType__c"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Zie het artikel [Sales Force-connector](data-factory-salesforce-connector.md#dataset-properties) voor meer informatie.

### <a name="relational-source-in-copy-activity"></a>Relationele bron in Kopieer activiteit
Als u gegevens uit Sales Force kopieert, stelt u het **bron type** van de Kopieer activiteit in op **RelationalSource** en geeft u de volgende eigenschappen op in de sectie **bron** :

| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| query |Gebruik de aangepaste query om gegevens te lezen. |Een SQL-92 query of [Sales Force object query language-query (SOQL)](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm) . Bijvoorbeeld `select * from MyTable__c`. |Nee (als de **tabel naam** van de **gegevensset** is opgegeven) |

#### <a name="example"></a>Voorbeeld



```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "SalesforceToAzureBlob",
            "description": "Copy from Salesforce to an Azure blob",
            "type": "Copy",
            "inputs": [{
                "name": "SalesforceInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "SELECT Id, Col_AutoNumber__c, Col_Checkbox__c, Col_Currency__c, Col_Date__c, Col_DateTime__c, Col_Email__c, Col_Number__c, Col_Percent__c, Col_Phone__c, Col_Picklist__c, Col_Picklist_MultiSelect__c, Col_Text__c, Col_Text_Area__c, Col_Text_AreaLong__c, Col_Text_AreaRich__c, Col_URL__c, Col_Text_Encrypt__c, Col_Lookup__c FROM AllDataType__c"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

> [!IMPORTANT]
> Het onderdeel __c van de API-naam is vereist voor een aangepast object.

Zie het artikel [Sales Force-connector](data-factory-salesforce-connector.md#copy-activity-properties) voor meer informatie.

## <a name="web-data"></a>Webgegevens

### <a name="linked-service"></a>Gekoppelde service
Als u een gekoppelde web-service wilt definiëren, stelt u het **type** van de gekoppelde service in op **Web** en geeft u de volgende eigenschappen op in de sectie **typeProperties** :

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| URL |URL naar de webbron |Yes |
| authenticationType |Toegang. |Yes |


#### <a name="example"></a>Voorbeeld


```json
{
    "name": "web",
    "properties": {
        "type": "Web",
        "typeProperties": {
            "authenticationType": "Anonymous",
            "url": "https://en.wikipedia.org/wiki/"
        }
    }
}
```

Zie het artikel [Web Table connector](data-factory-web-table-connector.md#linked-service-properties) (Engelstalig) voor meer informatie.

### <a name="dataset"></a>Gegevensset
Als u een web-gegevensset wilt definiëren, stelt u het **type** gegevensset in op **Webtabel** en geeft u de volgende eigenschappen op in de sectie **typeProperties** :

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type |het type van de gegevensset. moet worden ingesteld op **Webtable** |Yes |
| leertraject |Een relatieve URL naar de resource die de tabel bevat. |Nee. Wanneer pad niet is opgegeven, wordt alleen de URL gebruikt die is opgegeven in de definitie van de gekoppelde service. |
| index |De index van de tabel in de resource. Zie index ophalen van een tabel in een sectie met een HTML-pagina voor de stappen voor het ophalen van index van een tabel in een HTML-pagina. |Yes |

#### <a name="example"></a>Voorbeeld

```json
{
    "name": "WebTableInput",
    "properties": {
        "type": "WebTable",
        "linkedServiceName": "WebLinkedService",
        "typeProperties": {
            "index": 1,
            "path": "AFI's_100_Years...100_Movies"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

Zie het artikel [Web Table connector](data-factory-web-table-connector.md#dataset-properties) (Engelstalig) voor meer informatie.

### <a name="web-source-in-copy-activity"></a>Webbron in Kopieer activiteit
Als u gegevens uit een webtabel kopieert, stelt u het **bron type** van de Kopieer activiteit in op **webbron**. Wanneer de bron in de Kopieer activiteit van het type **websource** is, worden er op dit moment geen aanvullende eigenschappen ondersteund.

#### <a name="example"></a>Voorbeeld

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "WebTableToAzureBlob",
            "description": "Copy from a Web table to an Azure blob",
            "type": "Copy",
            "inputs": [{
                "name": "WebTableInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "WebSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Zie het artikel [Web Table connector](data-factory-web-table-connector.md#copy-activity-properties) (Engelstalig) voor meer informatie.

## <a name="compute-environments"></a>REKEN OMGEVINGEN
De volgende tabel geeft een lijst van de reken omgevingen die worden ondersteund door Data Factory en de trans formatie activiteiten die hierop kunnen worden uitgevoerd. Klik op de koppeling voor de compute waarin u bent geïnteresseerd om de JSON-schema's voor de gekoppelde service te zien om deze te koppelen aan een data factory.

| Compute-omgeving | Activiteiten |
| --- | --- |
| [Hdinsight-cluster op aanvraag](#on-demand-azure-hdinsight-cluster) of [uw eigen hdinsight-cluster](#existing-azure-hdinsight-cluster) |[Aangepaste .net-activiteit](#net-custom-activity), [Hive](#hdinsight-hive-activity)-activiteit, [Pig-activiteit](#hdinsight-pig-activity), [MapReduce activiteit](#hdinsight-mapreduce-activity), Hadoop streaming-activiteit, [Spark-activiteit](#hdinsight-spark-activity) |
| [Azure Batch](#azure-batch) |[Aangepaste .NET-activiteit](#net-custom-activity) |
| [Azure Machine Learning Studio (klassiek)](#azure-machine-learning-studio-classic) | [Azure machine learning Studio (klassiek) batch uitvoerings activiteit](#azure-machine-learning-studio-classic-batch-execution-activity), [Azure machine learning Studio (klassiek) resource activiteit bijwerken](#azure-machine-learning-studio-classic-update-resource-activity) |
| [Azure Data Lake Analytics](#azure-data-lake-analytics) |[Data Lake Analytics U-SQL](#data-lake-analytics-u-sql-activity) |
| [Azure SQL database](#azure-sql-database), [Azure Synapse Analytics](#azure-synapse-analytics), [SQL Server](#sql-server-stored-procedure) |[Opgeslagen procedure](#stored-procedure-activity) |

## <a name="on-demand-azure-hdinsight-cluster"></a>Azure HDInsight-cluster op aanvraag
De Azure Data Factory-service kan automatisch een HDInsight-cluster op basis van Windows/Linux maken voor het verwerken van gegevens. Het cluster wordt gemaakt in dezelfde regio als het opslag account (linkedServiceName-eigenschap in de JSON) die aan het cluster is gekoppeld. U kunt de volgende transformatie activiteiten uitvoeren op deze gekoppelde service: [aangepaste .net-activiteit](#net-custom-activity), [Hive](#hdinsight-hive-activity)-activiteit, [Pig-activiteit](#hdinsight-pig-activity), [MapReduce activiteit](#hdinsight-mapreduce-activity), Hadoop streaming-activiteit, [Spark-activiteit](#hdinsight-spark-activity).

### <a name="linked-service"></a>Gekoppelde service
De volgende tabel bevat beschrijvingen voor de eigenschappen die worden gebruikt in de Azure JSON-definitie van een gekoppelde HDInsight-service op aanvraag.

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| type |De eigenschap type moet worden ingesteld op **HDInsightOnDemand**. |Yes |
| clusterSize |Aantal werk-en gegevens knooppunten in het cluster. Het HDInsight-cluster wordt gemaakt met 2 hoofd knooppunten, samen met het aantal worker-knoop punten dat u voor deze eigenschap opgeeft. De knoop punten zijn van grootte Standard_D3 met 4 kern geheugens. een cluster van 4 worker-knoop punten neemt dus 24 kernen (4 \* 4 = 16 kernen voor werk knooppunten, plus 2 \* 4 = 8 kernen voor hoofd knooppunten). Zie op [Linux gebaseerde Hadoop-clusters maken in HDInsight](../../hdinsight/hdinsight-hadoop-provision-linux-clusters.md) voor meer informatie over de Standard_D3-laag. |Yes |
| timetolive |De toegestane tijd niet-actief voor het HDInsight-cluster op aanvraag. Hiermee geeft u op hoe lang het HDInsight-cluster op aanvraag actief blijft na voltooiing van een uitvoering van een activiteit als er geen andere actieve taken in het cluster zijn.<br/><br/>Als bijvoorbeeld het uitvoeren van een activiteit zes minuten duurt en timetolive is ingesteld op 5 minuten, blijft het cluster 5 minuten na de 6 minuten van verwerking van de uitvoering van de activiteit actief. Als er een andere uitvoering van de activiteit wordt uitgevoerd met het venster van 6 minuten, wordt deze verwerkt door hetzelfde cluster.<br/><br/>Het maken van een HDInsight-cluster op aanvraag is een dure bewerking (kan enige tijd duren). Gebruik deze instelling daarom zo nodig om de prestaties van een data factory te verbeteren door opnieuw gebruik te maken van een on-demand HDInsight-cluster.<br/><br/>Als u timetolive waarde instelt op 0, wordt het cluster verwijderd zodra de activiteit wordt uitgevoerd. Als u daarentegen een hoge waarde instelt, kan het cluster onnodig onbeschikbaar blijven als gevolg van hoge kosten. Daarom is het belang rijk dat u de juiste waarde instelt op basis van uw behoeften.<br/><br/>Meerdere pijp lijnen kunnen hetzelfde exemplaar van het HDInsight-cluster op aanvraag delen als de waarde van de eigenschap timetolive op de juiste wijze is ingesteld |Yes |
| versie |De versie van het HDInsight-cluster. Zie [ondersteunde HDInsight-versies in azure Data Factory](data-factory-compute-linked-services.md#supported-hdinsight-versions-in-azure-data-factory)voor meer informatie. |No |
| linkedServiceName |Azure Storage gekoppelde service die moet worden gebruikt door het cluster op aanvraag om gegevens op te slaan en te verwerken. <p>Op dit moment kunt u geen HDInsight-cluster op aanvraag maken dat gebruikmaakt van een Azure Data Lake Store als opslag. Als u de resultaat gegevens van HDInsight-verwerking in een Azure Data Lake Store wilt opslaan, gebruikt u een Kopieer activiteit om de gegevens van de Azure-Blob Storage naar de Azure Data Lake Store te kopiëren.</p>  | Yes |
| additionalLinkedServiceNames |Hiermee worden extra opslag accounts voor de gekoppelde HDInsight-service opgegeven, zodat de Data Factory-service namens u kan worden geregistreerd. |No |
| osType |Type besturings systeem. Toegestane waarden zijn: Windows (standaard) en Linux |No |
| hcatalogLinkedServiceName |De naam van de gekoppelde Azure SQL-service die verwijst naar de HCatalog-data base. Het HDInsight-cluster op aanvraag wordt gemaakt door Azure SQL Database als de meta Store te gebruiken. |No |

### <a name="json-example"></a>JSON-voor beeld
De volgende JSON definieert een gekoppelde HDInsight-service op aanvraag van Linux. De Data Factory-service maakt automatisch een HDInsight **-cluster op basis van Linux** tijdens het verwerken van een gegevens segment.

```json
{
    "name": "HDInsightOnDemandLinkedService",
    "properties": {
        "type": "HDInsightOnDemand",
        "typeProperties": {
            "version": "3.5",
            "clusterSize": 1,
            "timeToLive": "00:05:00",
            "osType": "Linux",
            "linkedServiceName": "StorageLinkedService"
        }
    }
}
```

Zie het artikel [Compute linked Services](data-factory-compute-linked-services.md) (Engelstalig) voor meer informatie.

## <a name="existing-azure-hdinsight-cluster"></a>Bestaand Azure HDInsight-cluster
U kunt een gekoppelde Azure HDInsight-service maken om uw eigen HDInsight-cluster te registreren bij Data Factory. U kunt de volgende gegevens transformatie activiteiten uitvoeren op deze gekoppelde service: [.net aangepaste activiteit](#net-custom-activity), [Hive](#hdinsight-hive-activity)-activiteit, [Pig-activiteit](#hdinsight-pig-activity), [MapReduce activiteit](#hdinsight-mapreduce-activity), Hadoop streaming-activiteit, [Spark-activiteit](#hdinsight-spark-activity).

### <a name="linked-service"></a>Gekoppelde service
De volgende tabel bevat beschrijvingen voor de eigenschappen die worden gebruikt in de Azure JSON-definitie van een gekoppelde Azure HDInsight-service.

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| type |De eigenschap type moet worden ingesteld op **HDInsight**. |Yes |
| clusterUri |De URI van het HDInsight-cluster. |Yes |
| gebruikersnaam |Geef de naam op van de gebruiker die moet worden gebruikt om verbinding te maken met een bestaand HDInsight-cluster. |Yes |
| wachtwoord |Geef het wacht woord voor het gebruikers account op. |Yes |
| linkedServiceName | De naam van de gekoppelde Azure Storage-service die verwijst naar de Azure Blob-opslag die wordt gebruikt door het HDInsight-cluster. <p>Op dit moment kunt u geen Azure Data Lake Store gekoppelde service opgeven voor deze eigenschap. Als het HDInsight-cluster toegang heeft tot de Data Lake Store, kunt u toegang krijgen tot gegevens in de Azure Data Lake Store. </p>  |Yes |

Zie [ondersteunde hdinsight-versies](data-factory-compute-linked-services.md#supported-hdinsight-versions-in-azure-data-factory)voor versies van HDInsight-clusters die worden ondersteund.

#### <a name="json-example"></a>JSON-voor beeld

```json
{
    "name": "HDInsightLinkedService",
    "properties": {
        "type": "HDInsight",
        "typeProperties": {
            "clusterUri": " https://<hdinsightclustername>.azurehdinsight.net/",
            "userName": "admin",
            "password": "<password>",
            "linkedServiceName": "MyHDInsightStoragelinkedService"
        }
    }
}
```

## <a name="azure-batch"></a>Azure Batch
U kunt een Azure Batch gekoppelde service maken om een batch-pool van virtuele machines (Vm's) te registreren met een data factory. U kunt aangepaste .NET-activiteiten uitvoeren met behulp van Azure Batch of Azure HDInsight. U kunt een [aangepaste .net-activiteit](#net-custom-activity) uitvoeren op deze gekoppelde service.

### <a name="linked-service"></a>Gekoppelde service
De volgende tabel bevat beschrijvingen voor de eigenschappen die worden gebruikt in de Azure JSON-definitie van een Azure Batch gekoppelde service.

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| type |De eigenschap type moet worden ingesteld op **AzureBatch**. |Yes |
| accountName |De naam van het Azure Batch-account. |Yes |
| accessKey |De toegangs sleutel voor het Azure Batch-account. |Yes |
| poolName |De naam van de pool van virtuele machines. |Yes |
| linkedServiceName |De naam van de Azure Storage gekoppelde service die is gekoppeld aan deze Azure Batch gekoppelde service. Deze gekoppelde service wordt gebruikt voor tijdelijke bestanden die vereist zijn om de activiteit uit te voeren en de logboeken voor het uitvoeren van activiteiten op te slaan. |Yes |


#### <a name="json-example"></a>JSON-voor beeld

```json
{
    "name": "AzureBatchLinkedService",
    "properties": {
        "type": "AzureBatch",
        "typeProperties": {
            "accountName": "<Azure Batch account name>",
            "accessKey": "<Azure Batch account key>",
            "poolName": "<Azure Batch pool name>",
            "linkedServiceName": "<Specify associated storage linked service reference here>"
        }
    }
}
```

## <a name="azure-machine-learning-studio-classic"></a>Azure Machine Learning Studio (klassiek)
U maakt een gekoppelde Azure Machine Learning Studio-Service (klassiek) om een studio (klassiek) batch Score-eind punt te registreren met een data factory. Twee activiteiten voor gegevens transformatie die op deze gekoppelde service kunnen worden uitgevoerd: [Azure machine learning Studio (klassieke) activiteiten voor batch uitvoering](#azure-machine-learning-studio-classic-batch-execution-activity), [Azure machine learning Studio (klassiek) resource activiteit bijwerken](#azure-machine-learning-studio-classic-update-resource-activity).

### <a name="linked-service"></a>Gekoppelde service
De volgende tabel bevat beschrijvingen voor de eigenschappen die in de Azure JSON-definitie van een gekoppelde Studio-Service (klassiek) worden gebruikt.

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| Type |De eigenschap type moet worden ingesteld op: **AzureML**. |Yes |
| mlEndpoint |De batch Score-URL. |Yes |
| apiKey |De API van het gepubliceerde werkruimte model. |Yes |

#### <a name="json-example"></a>JSON-voor beeld

```json
{
    "name": "AzureMLLinkedService",
    "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "https://[batch scoring endpoint]/jobs",
            "apiKey": "<apikey>"
        }
    }
}
```

## <a name="azure-data-lake-analytics"></a>Azure Data Lake Analytics
U maakt een **Azure data Lake Analytics** gekoppelde service om een Azure data Lake Analytics compute-service te koppelen aan een Azure-Data Factory voordat u de [Data Lake Analytics U-SQL-activiteit](data-factory-usql-activity.md) in een pijp lijn gebruikt.

### <a name="linked-service"></a>Gekoppelde service

De volgende tabel bevat beschrijvingen voor de eigenschappen die worden gebruikt in de JSON-definitie van een Azure Data Lake Analytics gekoppelde service.

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| Type |De eigenschap type moet worden ingesteld op: **AzureDataLakeAnalytics**. |Yes |
| accountName |Azure Data Lake Analytics account naam. |Yes |
| dataLakeAnalyticsUri |Azure Data Lake Analytics-URI. |No |
| autorisatie |De autorisatie code wordt automatisch opgehaald nadat u op de knop **machtigen** in de Data Factory editor hebt geklikt en de OAuth-aanmelding hebt voltooid. |Yes |
| subscriptionId |Azure-abonnements-ID |Nee (als dit niet wordt opgegeven, wordt het abonnement van de data factory gebruikt). |
| resourceGroupName |Naam van Azure-resourcegroep |Nee (als dit niet is opgegeven, wordt de resource groep van de data factory gebruikt). |
| sessionId |sessie-ID van de OAuth-autorisatie sessie. Elke sessie-ID is uniek en kan slechts één keer worden gebruikt. Wanneer u de Data Factory editor gebruikt, wordt deze ID automatisch gegenereerd. |Yes |


#### <a name="json-example"></a>JSON-voor beeld
In het volgende voor beeld wordt een JSON-definitie geboden voor een Azure Data Lake Analytics gekoppelde service.

```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "<account name>",
            "dataLakeAnalyticsUri": "datalakeanalyticscompute.net",
            "authorization": "<authcode>",
            "sessionId": "<session ID>",
            "subscriptionId": "<subscription id>",
            "resourceGroupName": "<resource group name>"
        }
    }
}
```

## <a name="sql-server-stored-procedure"></a>Opgeslagen procedure SQL Server

U maakt een SQL Server gekoppelde service en gebruikt deze met de [opgeslagen procedure activiteit](data-factory-stored-proc-activity.md) om een opgeslagen procedure vanuit een Data Factory pijp lijn aan te roepen.

### <a name="linked-service"></a>Gekoppelde service
U maakt een gekoppelde service van het type **OnPremisesSqlServer** om een SQL Server-Data Base aan een Data Factory te koppelen. In de volgende tabel vindt u een beschrijving van de JSON-elementen die specifiek zijn voor SQL Server gekoppelde service.

In de volgende tabel vindt u een beschrijving van de JSON-elementen die specifiek zijn voor SQL Server gekoppelde service.

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| type |De eigenschap type moet worden ingesteld op: **OnPremisesSqlServer**. |Ja |
| connectionString |Geef Connections Tring-gegevens op die nodig zijn om verbinding te maken met de SQL Server-Data Base via SQL-verificatie of Windows-verificatie. |Yes |
| gatewayName |De naam van de gateway die de Data Factory-service moet gebruiken om verbinding te maken met de SQL Server-Data Base. |Yes |
| gebruikersnaam |Geef de gebruikers naam op als u Windows-verificatie gebruikt. Voor beeld: **domainname \\ gebruikers naam**. |No |
| wachtwoord |Geef het wacht woord op voor het gebruikers account dat u hebt opgegeven voor de gebruikers naam. |No |

U kunt referenties versleutelen met de cmdlet **New-AzDataFactoryEncryptValue** en deze gebruiken in de Connection String, zoals wordt weer gegeven in het volgende voor beeld (eigenschap **EncryptedCredential** ):

```JSON
"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",
```


#### <a name="example-json-for-using-sql-authentication"></a>Voor beeld: JSON voor het gebruik van SQL-verificatie

```json
{
    "name": "MyOnPremisesSQLDB",
    "properties": {
        "type": "OnPremisesSqlServer",
        "typeProperties": {
            "connectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=False;User ID=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```
#### <a name="example-json-for-using-windows-authentication"></a>Voor beeld: JSON voor het gebruik van Windows-verificatie

Als gebruikers naam en wacht woord zijn opgegeven, wordt deze door de gateway gebruikt om het opgegeven gebruikers account te imiteren om verbinding te maken met de SQL Server-Data Base. Anders maakt gateway verbinding met de SQL Server rechtstreeks met de beveiligings context van de gateway (het opstart account).

```json
{
    "Name": " MyOnPremisesSQLDB",
    "Properties": {
        "type": "OnPremisesSqlServer",
        "typeProperties": {
            "ConnectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=True;",
            "username": "<domain\\username>",
            "password": "<password>",
            "gatewayName": "<gateway name>"
        }
    }
}
```

Zie [SQL Server connector](data-factory-sqlserver-connector.md#linked-service-properties) -artikel voor meer informatie.

## <a name="data-transformation-activities"></a>ACTIVITEITEN VOOR GEGEVENS TRANSFORMATIE

Activiteit | Beschrijving
-------- | -----------
[HDInsight Hive-activiteit](#hdinsight-hive-activity) | De HDInsight Hive-activiteit in een Data Factory pijp lijn voert Hive-query's uit op uw eigen of op aanvraag gebaseerd HDInsight-cluster op basis van Windows/Linux.
[HDInsight Pig-activiteit](#hdinsight-pig-activity) | Met de HDInsight Pig-activiteit in een Data Factory pijp lijn worden Pig-query's uitgevoerd op uw eigen of op aanvraag gebaseerd HDInsight-cluster op basis van Windows/Linux.
[HDInsight MapReduce-activiteit](#hdinsight-mapreduce-activity) | Met de HDInsight MapReduce-activiteit in een Data Factory pijp lijn worden MapReduce-Program ma's uitgevoerd op uw eigen of op aanvraag gebaseerd HDInsight-cluster op basis van Windows/Linux.
[HDInsight-streamingactiviteit](#hdinsight-streaming-activity) | Met de activiteit HDInsight streaming in een Data Factory pijp lijn worden Hadoop-streaming-Program ma's uitgevoerd op uw eigen of op aanvraag gebaseerd HDInsight-cluster op basis van Windows/Linux.
[HDInsight Spark-activiteit](#hdinsight-spark-activity) | Met de HDInsight Spark-activiteit in een Data Factory pijp lijn worden Spark-Program ma's uitgevoerd op uw eigen HDInsight-cluster.
[Azure Machine Learning Studio-batchuitvoeringsactiviteit (klassiek)](#azure-machine-learning-studio-classic-batch-execution-activity) | Met Azure Data Factory kunt u eenvoudig pijp lijnen maken die gebruikmaken van een gepubliceerde Studio-webservice (klassiek) voor predictive analytics. Met de activiteit voor batch uitvoering in een Azure Data Factory-pijp lijn kunt u een studio-webservice aanroepen om voor spellingen te maken voor de gegevens in batch.
[Resourceactiviteit voor Azure Machine Learning Studio (klassiek)](#azure-machine-learning-studio-classic-update-resource-activity) | Na verloop van tijd moeten de voorspellende modellen in de Azure Machine Learning Studio (klassieke) Score experimenten opnieuw worden getraind met nieuwe invoer gegevens sets. Wanneer u klaar bent met opnieuw trainen, wilt u de Score-webservice bijwerken met het opnieuw getrainde machine learning model. U kunt de activiteit resource bijwerken gebruiken om de webservice bij te werken met het nieuwe getrainde model.
[Opgeslagen procedureactiviteit](#stored-procedure-activity) | U kunt de opgeslagen procedure-activiteit in een Data Factory pijp lijn gebruiken voor het aanroepen van een opgeslagen procedure in een van de volgende gegevens archieven: Azure SQL Database, Azure Synapse Analytics, SQL Server data base in uw onderneming of een Azure-VM.
[Data Lake Analytics U-SQL-activiteit](#data-lake-analytics-u-sql-activity) | Met Data Lake Analytics U-SQL-activiteit wordt een U-SQL-script op een Azure Data Lake Analytics cluster uitgevoerd.
[Aangepaste .NET-activiteit](#net-custom-activity) | Als u gegevens moet transformeren op een manier die niet wordt ondersteund door Data Factory, kunt u een aangepaste activiteit maken met uw eigen gegevens verwerkings logica en de activiteit in de pijp lijn gebruiken. U kunt de aangepaste .NET-activiteit zodanig configureren dat deze wordt uitgevoerd met behulp van een Azure Batch-service of een Azure HDInsight-cluster.


## <a name="hdinsight-hive-activity"></a>HDInsight Hive-activiteit
U kunt de volgende eigenschappen opgeven in een JSON-definitie van Hive-activiteit. De eigenschap type voor de activiteit moet: **HDInsightHive**. U moet eerst een gekoppelde HDInsight-service maken en de naam ervan opgeven als een waarde voor de eigenschap **linkedServiceName** . De volgende eigenschappen worden ondersteund in de **typeProperties** -sectie wanneer u het type activiteit instelt op HDInsightHive:

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| script |Het Hive-script inline opgeven |No |
| scriptpad |Sla het Hive-script op in een Azure Blob-opslag en geef het pad naar het bestand op. Gebruik de eigenschap script of scriptPath. Beide kunnen niet tegelijk worden gebruikt. De bestands naam is hoofdletter gevoelig. |No |
| compliant |Geef para meters op als sleutel/waarde-paren voor het verwijzen in het Hive-script met behulp van ' hiveconf ' |No |

Deze type-eigenschappen zijn specifiek voor de Hive-activiteit. Andere eigenschappen (buiten de sectie typeProperties) worden ondersteund voor alle activiteiten.

### <a name="json-example"></a>JSON-voor beeld
De volgende JSON definieert een HDInsight Hive-activiteit in een pijp lijn.

```json
{
    "name": "Hive Activity",
    "description": "description",
    "type": "HDInsightHive",
    "inputs": [
      {
        "name": "input tables"
      }
    ],
    "outputs": [
      {
        "name": "output tables"
      }
    ],
    "linkedServiceName": "MyHDInsightLinkedService",
    "typeProperties": {
      "script": "Hive script",
      "scriptPath": "<pathtotheHivescriptfileinAzureblobstorage>",
      "defines": {
        "param1": "param1Value"
      }
    },
   "scheduler": {
      "frequency": "Day",
      "interval": 1
    }
}
```

Zie het artikel Hive- [activiteit](data-factory-hive-activity.md) voor meer informatie.

## <a name="hdinsight-pig-activity"></a>HDInsight Pig-activiteit
U kunt de volgende eigenschappen opgeven in de JSON-definitie van een Pig-activiteit. De eigenschap type voor de activiteit moet: **HDInsightPig**. U moet eerst een gekoppelde HDInsight-service maken en de naam ervan opgeven als een waarde voor de eigenschap **linkedServiceName** . De volgende eigenschappen worden ondersteund in de **typeProperties** -sectie wanneer u het type activiteit instelt op HDInsightPig:

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| script |Inline van het Pig-script opgeven |No |
| scriptpad |Sla het Pig-script op in een Azure Blob-opslag en geef het pad naar het bestand op. Gebruik de eigenschap script of scriptPath. Beide kunnen niet tegelijk worden gebruikt. De bestands naam is hoofdletter gevoelig. |No |
| compliant |Para meters opgeven als sleutel/waarde-paren voor het verwijzen binnen het Pig-script |No |

Deze type-eigenschappen zijn specifiek voor de Pig-activiteit. Andere eigenschappen (buiten de sectie typeProperties) worden ondersteund voor alle activiteiten.

### <a name="json-example"></a>JSON-voor beeld

```json
{
    "name": "HiveActivitySamplePipeline",
      "properties": {
    "activities": [
        {
            "name": "Pig Activity",
            "description": "description",
            "type": "HDInsightPig",
            "inputs": [
                  {
                    "name": "input tables"
                  }
            ],
            "outputs": [
                  {
                    "name": "output tables"
                  }
            ],
            "linkedServiceName": "MyHDInsightLinkedService",
            "typeProperties": {
                  "script": "Pig script",
                  "scriptPath": "<pathtothePigscriptfileinAzureblobstorage>",
                  "defines": {
                    "param1": "param1Value"
                  }
            },
               "scheduler": {
                  "frequency": "Day",
                  "interval": 1
            }
          }
    ]
  }
}
```

Zie het artikel Pig-activiteit voor meer informatie.

## <a name="hdinsight-mapreduce-activity"></a>HDInsight MapReduce-activiteit
U kunt de volgende eigenschappen opgeven in een JSON-definitie van de MapReduce-activiteit. De eigenschap type voor de activiteit moet: **HDInsightMapReduce**. U moet eerst een gekoppelde HDInsight-service maken en de naam ervan opgeven als een waarde voor de eigenschap **linkedServiceName** . De volgende eigenschappen worden ondersteund in de **typeProperties** -sectie wanneer u het type activiteit instelt op HDInsightMapReduce:

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| jarLinkedService | De naam van de gekoppelde service voor de Azure Storage die het JAR-bestand bevat. | Yes |
| jarFilePath | Pad naar het JAR-bestand in de Azure Storage. | Yes |
| className | De naam van de hoofd klasse in het JAR-bestand. | Yes |
| opmerkingen | Een lijst met door komma's gescheiden argumenten voor het MapReduce-programma. Tijdens runtime ziet u enkele extra argumenten (bijvoorbeeld: MapReduce. job. Tags) van het MapReduce-Framework. Als u de argumenten wilt onderscheiden met de MapReduce argumenten, kunt u de optie en waarde als argumenten gebruiken, zoals wordt weer gegeven in het volgende voor beeld (-s,--input,--output etc., zijn opties direct gevolgd door hun waarden) | No |

### <a name="json-example"></a>JSON-voor beeld

```json
{
    "name": "MahoutMapReduceSamplePipeline",
    "properties": {
        "description": "Sample Pipeline to Run a Mahout Custom Map Reduce Jar. This job calculates an Item Similarity Matrix to determine the similarity between two items",
        "activities": [
            {
                "type": "HDInsightMapReduce",
                "typeProperties": {
                    "className": "org.apache.mahout.cf.taste.hadoop.similarity.item.ItemSimilarityJob",
                    "jarFilePath": "adfsamples/Mahout/jars/mahout-examples-0.9.0.2.2.7.1-34.jar",
                    "jarLinkedService": "StorageLinkedService",
                    "arguments": ["-s", "SIMILARITY_LOGLIKELIHOOD", "--input", "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/input", "--output", "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/output/", "--maxSimilaritiesPerItem", "500", "--tempDir", "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/temp/mahout"]
                },
                "inputs": [
                    {
                        "name": "MahoutInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "MahoutOutput"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "retry": 3
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "MahoutActivity",
                "description": "Custom Map Reduce to generate Mahout result",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2017-01-03T00:00:00",
        "end": "2017-01-04T00:00:00"
    }
}
```

Zie [MapReduce activity](data-factory-map-reduce.md) article (Engelstalig) voor meer informatie.

## <a name="hdinsight-streaming-activity"></a>HDInsight-streamingactiviteit
U kunt de volgende eigenschappen opgeven in een Hadoop streaming-activiteit JSON-definitie. De eigenschap type voor de activiteit moet: **HDInsightStreaming**. U moet eerst een gekoppelde HDInsight-service maken en de naam ervan opgeven als een waarde voor de eigenschap **linkedServiceName** . De volgende eigenschappen worden ondersteund in de **typeProperties** -sectie wanneer u het type activiteit instelt op HDInsightStreaming:

| Eigenschap | Beschrijving |
| --- | --- |
| toewijzing | De naam van het uitvoer bare toewijzings programma. In het voor beeld is cat.exe het uitvoer bare toewijzings programma.|
| reductier | De naam van het uitvoer bare bestand voor de verkleinings groep. In het voor beeld is wc.exe het uitvoerbaar bestand voor de reducer. |
| input | Invoer bestand (inclusief locatie) voor de toewijzing. In het voor beeld: `"wasb://adfsample@<account name>.blob.core.windows.net/example/data/gutenberg/davinci.txt"` : adfsample is de BLOB-container, voor beeld/data/Gutenberg is de map en davinci.txt is de blob. |
| output | Uitvoer bestand (inclusief locatie) voor de verkorter. De uitvoer van de Hadoop streaming-taak wordt geschreven naar de locatie die is opgegeven voor deze eigenschap. |
| filePaths | Paden voor de uitvoer bare bestanden Mapper en reducer. In het voor beeld: ' adfsample/example/apps/wc.exe ', adfsample is de BLOB-container, voor beeld/apps is de map en wc.exe het uitvoer bare bestand is. |
| fileLinkedService | Azure Storage gekoppelde service die de Azure-opslag vertegenwoordigt die de bestanden bevat die zijn opgegeven in de sectie filePath. |
| opmerkingen | Een lijst met door komma's gescheiden argumenten voor het MapReduce-programma. Tijdens runtime ziet u enkele extra argumenten (bijvoorbeeld: MapReduce. job. Tags) van het MapReduce-Framework. Als u de argumenten wilt onderscheiden met de MapReduce argumenten, kunt u de optie en waarde als argumenten gebruiken, zoals wordt weer gegeven in het volgende voor beeld (-s,--input,--output etc., zijn opties direct gevolgd door hun waarden) |
| getDebugInfo | Een optioneel element. Als deze is ingesteld op fout, worden de Logboeken alleen gedownload bij fout. Als deze is ingesteld op alle, worden logboeken altijd gedownload, ongeacht de uitvoerings status. |

> [!NOTE]
> U moet een uitvoer gegevensset opgeven voor de activiteit Hadoop streaming voor de eigenschap **outputs** . Deze gegevensset kan alleen een dummy-gegevensset zijn die is vereist om de pijplijn planning (elk uur, dagelijks, enzovoort) te kunnen bezorgen. Als de activiteit geen invoer heeft, kunt u het opgeven van een invoer gegevensset voor de activiteit voor de eigenschap **inputs** overs Laan.

## <a name="json-example"></a>JSON-voor beeld

```json
{
    "name": "HadoopStreamingPipeline",
    "properties": {
        "description": "Hadoop Streaming Demo",
        "activities": [
            {
                "type": "HDInsightStreaming",
                "typeProperties": {
                    "mapper": "cat.exe",
                    "reducer": "wc.exe",
                    "input": "wasb://<nameofthecluster>@spestore.blob.core.windows.net/example/data/gutenberg/davinci.txt",
                    "output": "wasb://<nameofthecluster>@spestore.blob.core.windows.net/example/data/StreamingOutput/wc.txt",
                    "filePaths": ["<nameofthecluster>/example/apps/wc.exe","<nameofthecluster>/example/apps/cat.exe"],
                    "fileLinkedService": "StorageLinkedService",
                    "getDebugInfo": "Failure"
                },
                "outputs": [
                    {
                        "name": "StreamingOutputDataset"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "RunHadoopStreamingJob",
                "description": "Run a Hadoop streaming job",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2014-01-04T00:00:00",
        "end": "2014-01-05T00:00:00"
    }
}
```

Zie het artikel [Hadoop streaming-activiteit](data-factory-hadoop-streaming-activity.md) voor meer informatie.

## <a name="hdinsight-spark-activity"></a>HDInsight Spark-activiteit
U kunt de volgende eigenschappen opgeven in een JSON-definitie van een Spark-activiteit. De eigenschap type voor de activiteit moet: **HDInsightSpark**. U moet eerst een gekoppelde HDInsight-service maken en de naam ervan opgeven als een waarde voor de eigenschap **linkedServiceName** . De volgende eigenschappen worden ondersteund in de **typeProperties** -sectie wanneer u het type activiteit instelt op HDInsightSpark:

| Eigenschap | Beschrijving | Vereist |
| -------- | ----------- | -------- |
| rootPath | De Azure Blob-container en de map waarin het Spark-bestand zich bevindt. De bestands naam is hoofdletter gevoelig. | Yes |
| entryFilePath | Relatief pad naar de hoofdmap van de Spark-code/-pakket. | Yes |
| className | Hoofd klasse java/Spark van de toepassing | No |
| opmerkingen | Een lijst met opdracht regel argumenten voor het Spark-programma. | No |
| proxyUser | Het gebruikers account dat moet worden geïmiteerd voor het uitvoeren van het Spark-programma | No |
| sparkConfig | Eigenschappen van Spark-configuratie. | No |
| getDebugInfo | Hiermee geeft u op wanneer de Spark-logboek bestanden worden gekopieerd naar de Azure-opslag die wordt gebruikt door het HDInsight-cluster (of) dat is opgegeven door sparkJobLinkedService. Toegestane waarden: geen, altijd of mislukt. Standaard waarde: geen. | No |
| sparkJobLinkedService | De Azure Storage gekoppelde service die het Spark-taak bestand, de afhankelijkheden en de logboeken bevat.  Als u geen waarde voor deze eigenschap opgeeft, wordt de opslag gebruikt die aan het HDInsight-cluster is gekoppeld. | No |

### <a name="json-example"></a>JSON-voor beeld

```json
{
    "name": "SparkPipeline",
    "properties": {
        "activities": [
            {
                "type": "HDInsightSpark",
                "typeProperties": {
                    "rootPath": "adfspark\\pyFiles",
                    "entryFilePath": "test.py",
                    "getDebugInfo": "Always"
                },
                "outputs": [
                    {
                        "name": "OutputDataset"
                    }
                ],
                "name": "MySparkActivity",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2017-02-05T00:00:00",
        "end": "2017-02-06T00:00:00"
    }
}
```
Houd rekening met de volgende punten:

- De eigenschap **type** wordt ingesteld op **HDInsightSpark**.
- De **rootPath** is ingesteld op **adfspark \\ pyFiles** , waarbij adfspark de Azure Blob-container is en pyFiles een fijnere map in die container. In dit voor beeld is de Azure-Blob Storage de versie die is gekoppeld aan het Spark-cluster. U kunt het bestand uploaden naar een andere Azure Storage. Als u dit doet, maakt u een Azure Storage gekoppelde service om het opslag account te koppelen aan de data factory. Geef vervolgens de naam van de gekoppelde service op als waarde voor de eigenschap **sparkJobLinkedService** . Zie Eigenschappen van Spark-activiteit voor meer informatie over deze eigenschap en andere eigenschappen die door de Spark-activiteit worden ondersteund.
- De **entryFilePath** is ingesteld op de **test.py**. Dit is het python-bestand.
- De eigenschap **getDebugInfo** is ingesteld op **Always**, wat betekent dat de logboek bestanden altijd worden gegenereerd (geslaagd of mislukt).

    > [!IMPORTANT]
    > U wordt aangeraden deze eigenschap niet in te stellen op altijd in een productie omgeving, tenzij u een probleem oplost.
- De sectie **outputs** heeft één uitvoer gegevensset. U moet een uitvoer gegevensset opgeven, zelfs als het Spark-programma geen uitvoer produceert. De uitvoer gegevensset verstuurt het schema voor de pijp lijn (elk uur, dagelijks, enzovoort).

Zie artikel [Spark-activiteit](data-factory-spark.md) voor meer informatie over de activiteit.

## <a name="azure-machine-learning-studio-classic-batch-execution-activity"></a>Azure Machine Learning Studio-batchuitvoeringsactiviteit (klassiek)
U kunt de volgende eigenschappen opgeven in een JSON-definitie voor een Azure Machine Learning Studio (klassieke) batch uitvoering. De eigenschap type voor de activiteit moet: **AzureMLBatchExecution**. U moet eerst een gekoppelde Studio-Service (klassiek) maken en de naam ervan opgeven als een waarde voor de eigenschap **linkedServiceName** . De volgende eigenschappen worden ondersteund in de **typeProperties** -sectie wanneer u het type activiteit instelt op AzureMLBatchExecution:

Eigenschap | Beschrijving | Vereist
-------- | ----------- | --------
Webserviceinputactivity | De gegevensset die moet worden door gegeven als invoer voor de web-service Studio (klassiek). Deze gegevensset moet ook worden opgenomen in de invoer voor de activiteit. |Gebruik ofwel Webserviceinputactivity of webServiceInputs. |
webServiceInputs | Geef gegevens sets op die moeten worden door gegeven als invoer voor de web-service Studio (klassiek). Als de webservice meerdere invoer heeft, gebruikt u de eigenschap webServiceInputs in plaats van de eigenschap Webserviceinputactivity te gebruiken. Gegevens sets waarnaar wordt verwezen door de **webServiceInputs** moeten ook worden opgenomen in de activiteiten **invoer**. | Gebruik ofwel Webserviceinputactivity of webServiceInputs. |
webServiceOutputs | De gegevens sets die zijn toegewezen als uitvoer voor de web-service Studio (klassiek). De webservice retourneert uitvoer gegevens in deze gegevensset. | Yes |
globalParameters | Geef waarden op voor de web service-para meters in deze sectie. | No |

### <a name="json-example"></a>JSON-voor beeld
In dit voor beeld heeft de activiteit de gegevensset **MLSqlInput** als invoer en **MLSqlOutput** als uitvoer. De **MLSqlInput** wordt door gegeven als invoer voor de webservice met behulp van de JSON-eigenschap **webserviceinputactivity** . De **MLSqlOutput** wordt door gegeven als een uitvoer naar de webservice met behulp van de JSON-eigenschap **webServiceOutputs** .

```json
{
   "name": "MLWithSqlReaderSqlWriter",
   "properties": {
      "description": "Azure ML model with sql azure reader/writer",
      "activities": [{
         "name": "MLSqlReaderSqlWriterActivity",
         "type": "AzureMLBatchExecution",
         "description": "test",
         "inputs": [ { "name": "MLSqlInput" }],
         "outputs": [ { "name": "MLSqlOutput" } ],
         "linkedServiceName": "MLSqlReaderSqlWriterDecisionTreeModel",
         "typeProperties":
         {
            "webServiceInput": "MLSqlInput",
            "webServiceOutputs": {
               "output1": "MLSqlOutput"
            },
            "globalParameters": {
               "Database server name": "<myserver>.database.windows.net",
               "Database name": "<database>",
               "Server user account name": "<user name>",
               "Server user account password": "<password>"
            }
         },
         "policy": {
            "concurrency": 1,
            "executionPriorityOrder": "NewestFirst",
            "retry": 1,
            "timeout": "02:00:00"
         }
      }],
      "start": "2016-02-13T00:00:00",
       "end": "2016-02-14T00:00:00"
   }
}
```

In het JSON-voor beeld gebruikt de geimplementeerde Studio-webservice (Classic) een lezer en een schrijver-module voor het lezen/schrijven van gegevens van/naar een Azure SQL Database. Deze webservice bevat de volgende vier para meters: database server naam, database naam, Server gebruikers accountnaam en wacht woord voor Server gebruikers account.

> [!NOTE]
> Alleen invoer en uitvoer van de AzureMLBatchExecution-activiteit kunnen worden door gegeven als para meters voor de webservice. In het bovenstaande JSON-fragment is MLSqlInput bijvoorbeeld een invoer van de AzureMLBatchExecution-activiteit, die wordt door gegeven als invoer voor de webservice via de Webserviceinputactivity-para meter.

## <a name="azure-machine-learning-studio-classic-update-resource-activity"></a>Resourceactiviteit voor Azure Machine Learning Studio (klassiek)
U kunt de volgende eigenschappen opgeven in een Azure Machine Learning Studio (klassieke) de JSON-definitie van de resource activiteit bijwerken. De eigenschap type voor de activiteit moet: **AzureMLUpdateResource**. U moet eerst een gekoppelde Studio-Service (klassiek) maken en de naam ervan opgeven als een waarde voor de eigenschap **linkedServiceName** . De volgende eigenschappen worden ondersteund in de **typeProperties** -sectie wanneer u het type activiteit instelt op AzureMLUpdateResource:

Eigenschap | Beschrijving | Vereist
-------- | ----------- | --------
trainedModelName | De naam van het opnieuw getrainde model. | Yes |
trainedModelDatasetName | DataSet die verwijst naar het iLearner-bestand dat wordt geretourneerd door de bewerking voor opnieuw trainen. | Yes |

### <a name="json-example"></a>JSON-voor beeld
De pijp lijn heeft twee activiteiten: **AzureMLBatchExecution** en **AzureMLUpdateResource**. In de activiteit Studio (klassiek) batch execution worden de trainings gegevens als invoer gebruikt en wordt een iLearner-bestand als uitvoer gegenereerd. De activiteit roept de training-webservice (trainings experiment beschikbaar als een webservice) aan met de gegevens voor het invoeren van de training en ontvangt het ilearner-bestand van de webservice. De placeholderBlob is slechts een dummy-uitvoer gegevensset die wordt vereist door de Azure Data Factory-service om de pijp lijn uit te voeren.


```json
{
    "name": "pipeline",
    "properties": {
        "activities": [
            {
                "name": "retraining",
                "type": "AzureMLBatchExecution",
                "inputs": [
                    {
                        "name": "trainingData"
                    }
                ],
                "outputs": [
                    {
                        "name": "trainedModelBlob"
                    }
                ],
                "typeProperties": {
                    "webServiceInput": "trainingData",
                    "webServiceOutputs": {
                        "output1": "trainedModelBlob"
                    }
                 },
                "linkedServiceName": "trainingEndpoint",
                "policy": {
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1,
                    "timeout": "02:00:00"
                }
            },
            {
                "type": "AzureMLUpdateResource",
                "typeProperties": {
                    "trainedModelName": "trained model",
                    "trainedModelDatasetName" :  "trainedModelBlob"
                },
                "inputs": [{ "name": "trainedModelBlob" }],
                "outputs": [{ "name": "placeholderBlob" }],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "retry": 3
                },
                "name": "AzureML Update Resource",
                "linkedServiceName": "updatableScoringEndpoint2"
            }
        ],
        "start": "2016-02-13T00:00:00",
        "end": "2016-02-14T00:00:00"
    }
}
```

## <a name="data-lake-analytics-u-sql-activity"></a>Data Lake Analytics U-SQL-activiteit
U kunt de volgende eigenschappen opgeven in de JSON-definitie van een U-SQL-activiteit. De eigenschap type voor de activiteit moet: **DataLakeAnalyticsU-SQL** zijn. U moet een Azure Data Lake Analytics gekoppelde service maken en de naam ervan opgeven als een waarde voor de eigenschap **linkedServiceName** . De volgende eigenschappen worden ondersteund in de **typeProperties** -sectie wanneer u het type activiteit instelt op DATALAKEANALYTICSU-SQL:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| scriptPath |Pad naar de map die het U-SQL-script bevat. De naam van het bestand is hoofdletter gevoelig. |Nee (als u script gebruikt) |
| scriptLinkedService |Gekoppelde service die de opslag met het script koppelt aan de data factory |Nee (als u script gebruikt) |
| script |Geef inline-script op in plaats van scriptPath en scriptLinkedService op te geven. Bijvoorbeeld: ' script ': ' DATABASE test maken '. |Nee (als u scriptPath en scriptLinkedService gebruikt) |
| degreeOfParallelism |Het maximum aantal knoop punten dat tegelijkertijd wordt gebruikt om de taak uit te voeren. |No |
| priority |Hiermee wordt bepaald welke taken uit de wachtrij moeten worden geselecteerd om eerst te worden uitgevoerd. Hoe lager het getal, des te hoger de prioriteit. |No |
| parameters |Para meters voor het U-SQL-script |No |

### <a name="json-example"></a>JSON-voor beeld

```json
{
    "name": "ComputeEventsByRegionPipeline",
    "properties": {
        "description": "This pipeline computes events for en-gb locale and date less than Feb 19, 2012.",
        "activities":
        [
            {
                "type": "DataLakeAnalyticsU-SQL",
                "typeProperties": {
                    "scriptPath": "scripts\\kona\\SearchLogProcessing.txt",
                    "scriptLinkedService": "StorageLinkedService",
                    "degreeOfParallelism": 3,
                    "priority": 100,
                    "parameters": {
                        "in": "/datalake/input/SearchLog.tsv",
                        "out": "/datalake/output/Result.tsv"
                    }
                },
                "inputs": [
                    {
                        "name": "DataLakeTable"
                    }
                ],
                "outputs":
                [
                    {
                        "name": "EventsByRegionTable"
                    }
                ],
                "policy": {
                    "timeout": "06:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "EventsByRegion",
                "linkedServiceName": "AzureDataLakeAnalyticsLinkedService"
            }
        ],
        "start": "2015-08-08T00:00:00",
        "end": "2015-08-08T01:00:00",
        "isPaused": false
    }
}
```

Zie [Data Lake Analytics U-SQL-activiteit](data-factory-usql-activity.md)voor meer informatie.

## <a name="stored-procedure-activity"></a>Opgeslagen procedureactiviteit
U kunt de volgende eigenschappen opgeven in de JSON-definitie van een opgeslagen procedure activiteit. De eigenschap type voor de activiteit moet: **SqlServerStoredProcedure**. U moet een van de volgende gekoppelde services maken en de naam van de gekoppelde service opgeven als waarde voor de eigenschap **linkedServiceName** :

- SQL Server
- Azure SQL Database
- Azure Synapse Analytics

De volgende eigenschappen worden ondersteund in de **typeProperties** -sectie wanneer u het type activiteit instelt op SqlServerStoredProcedure:

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| storedProcedureName |Geef de naam op van de opgeslagen procedure in Azure SQL Database of Azure Synapse Analytics die wordt vertegenwoordigd door de gekoppelde service die door de uitvoer tabel wordt gebruikt. |Yes |
| storedProcedureParameters |Geef waarden op voor opgeslagen procedure parameters. Als u null moet door geven voor een para meter, gebruikt u de syntaxis: "param1": Null (alle kleine letters). Raadpleeg het volgende voor beeld voor meer informatie over het gebruik van deze eigenschap. |No |

Als u een invoer-gegevensset opgeeft, moet deze beschikbaar zijn (in de status gereed) voor het uitvoeren van de opgeslagen procedure activiteit. De invoer gegevensset kan niet worden gebruikt in de opgeslagen procedure als een para meter. Het wordt alleen gebruikt voor het controleren van de afhankelijkheid voordat de opgeslagen procedure activiteit wordt gestart. U moet een uitvoer gegevensset opgeven voor een opgeslagen procedure-activiteit.

Uitvoer gegevensset Hiermee geeft u de **planning** op voor de opgeslagen procedure activiteit (elk uur, wekelijks, maandelijks, enzovoort). De uitvoer gegevensset moet een **gekoppelde service** gebruiken die verwijst naar een Azure SQL database of een Azure Synapse Analytics of een SQL Server-Data Base waarin u de opgeslagen procedure wilt uitvoeren. De uitvoer gegevensset kan worden gebruikt als een manier om het resultaat van de opgeslagen procedure voor verdere verwerking door andere activiteiten (koppelings[activiteiten](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline)) in de pijp lijn door te geven. Data Factory schrijft de uitvoer van een opgeslagen procedure echter niet automatisch naar deze gegevensset. Het is de opgeslagen procedure die naar een SQL-tabel schrijft waarnaar de uitvoer-gegevensset verwijst. In sommige gevallen kan de uitvoer gegevensset een dummy- **gegevensset** zijn, die alleen wordt gebruikt om de planning op te geven voor het uitvoeren van de opgeslagen procedure activiteit.

### <a name="json-example"></a>JSON-voor beeld

```json
{
    "name": "SprocActivitySamplePipeline",
    "properties": {
        "activities": [
            {
                "type": "SqlServerStoredProcedure",
                "typeProperties": {
                    "storedProcedureName": "usp_sample",
                    "storedProcedureParameters": {
                        "DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)"
                    }
                },
                "outputs": [{ "name": "sprocsampleout" }],
                "name": "SprocActivitySample"
            }
        ],
         "start": "2016-08-02T00:00:00",
         "end": "2016-08-02T05:00:00",
        "isPaused": false
    }
}
```

Zie het artikel [opgeslagen procedure-activiteit](data-factory-stored-proc-activity.md) voor meer informatie.

## <a name="net-custom-activity"></a>Aangepaste .NET-activiteit
U kunt de volgende eigenschappen opgeven in een JSON-definitie van een aangepaste .NET-activiteit. De eigenschap type voor de activiteit moet: **DotNetActivity**. U moet een gekoppelde Azure HDInsight-service of een Azure Batch gekoppelde service maken en de naam van de gekoppelde service opgeven als waarde voor de eigenschap **linkedServiceName** . De volgende eigenschappen worden ondersteund in de **typeProperties** -sectie wanneer u het type activiteit instelt op DotNetActivity:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| AssemblyName | De naam van de assembly. In het voor beeld is dit: **MyDotnetActivity.dll**. | Yes |
| EntryPoint |De naam van de klasse die de IDotNetActivity-interface implementeert. In het voor beeld is dit: **MyDotNetActivityNS. MyDotNetActivity** waarbij MyDotNetActivityNS de naam ruimte is en MyDotNetActivity de klasse is.  | Yes |
| PackageLinkedService | De naam van de gekoppelde Azure Storage-service die verwijst naar de Blob-opslag met het zip-bestand van de aangepaste activiteit. In het voor beeld is dit: **AzureStorageLinkedService**.| Yes |
| PackageFile | De naam van het zip-bestand. In het voor beeld is dit: **customactivitycontainer/MyDotNetActivity.zip**. | Yes |
| extendedProperties | Uitgebreide eigenschappen die u kunt definiëren en door geven aan de .NET-code. In dit voor beeld wordt de variabele **slice start** ingesteld op een waarde op basis van de systeem variabele slice start. | No |

### <a name="json-example"></a>JSON-voor beeld

```json
{
  "name": "ADFTutorialPipelineCustom",
  "properties": {
    "description": "Use custom activity",
    "activities": [
      {
        "Name": "MyDotNetActivity",
        "Type": "DotNetActivity",
        "Inputs": [
          {
            "Name": "InputDataset"
          }
        ],
        "Outputs": [
          {
            "Name": "OutputDataset"
          }
        ],
        "LinkedServiceName": "AzureBatchLinkedService",
        "typeProperties": {
          "AssemblyName": "MyDotNetActivity.dll",
          "EntryPoint": "MyDotNetActivityNS.MyDotNetActivity",
          "PackageLinkedService": "AzureStorageLinkedService",
          "PackageFile": "customactivitycontainer/MyDotNetActivity.zip",
          "extendedProperties": {
            "SliceStart": "$$Text.Format('{0:yyyyMMddHH-mm}', Time.AddMinutes(SliceStart, 0))"
          }
        },
        "Policy": {
          "Concurrency": 2,
          "ExecutionPriorityOrder": "OldestFirst",
          "Retry": 3,
          "Timeout": "00:30:00",
          "Delay": "00:00:00"
        }
      }
    ],
    "start": "2016-11-16T00:00:00",
    "end": "2016-11-16T05:00:00",
    "isPaused": false
  }
}
```

Zie [aangepaste activiteiten gebruiken in Data Factory](data-factory-use-custom-activities.md) artikel voor meer informatie.

## <a name="next-steps"></a>Volgende stappen
Zie de volgende zelfstudies:

- [Zelf studie: een pijp lijn maken met een Kopieer activiteit](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
- [Zelf studie: een pijp lijn maken met een Hive-activiteit](data-factory-build-your-first-pipeline.md)