---
title: Gegevens sets maken in Azure Data Factory
description: Meer informatie over het maken van gegevens sets in Azure Data Factory, met voor beelden die gebruikmaken van eigenschappen zoals offset en anchorDateTime.
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 4b222b387dad1c078cfe2a063ed310ef463b192e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100376833"
---
# <a name="datasets-in-azure-data-factory-version-1"></a>Gegevens sets in Azure Data Factory (versie 1)
> [!div class="op_single_selector" title1="Selecteer de versie van de Data Factory-service die u gebruikt:"]
> * [Versie 1:](data-factory-create-datasets.md)
> * [Versie 2 (huidige versie)](../concepts-datasets-linked-services.md)

> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. Als u de huidige versie van de Data Factory-service gebruikt, raadpleegt u [gegevens sets in v2](../concepts-datasets-linked-services.md).

In dit artikel wordt beschreven welke gegevens sets zijn, hoe ze worden gedefinieerd in JSON-indeling en hoe ze worden gebruikt in Azure Data Factory pijp lijnen. Het bevat details over elke sectie (bijvoorbeeld structuur, Beschik baarheid en beleid) in de JSON-definitie van de gegevensset. Het artikel bevat ook voor beelden voor het gebruik van de eigenschappen **Offset**, **anchorDateTime** en **Style** in een JSON-definitie van een gegevensset.

> [!NOTE]
> Als u geen ervaring hebt met Data Factory, raadpleegt u [Introduction to Azure Data Factory](data-factory-introduction.md) voor een overzicht. Als u geen praktijk ervaring hebt met het maken van gegevens fabrieken, kunt u een beter inzicht krijgen door de [zelf studie voor gegevens transformatie](data-factory-build-your-first-pipeline.md) en de [zelf studie](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)voor het verplaatsen van gegevens te lezen.

## <a name="overview"></a>Overzicht
Een gegevensfactory kan één of meer pijplijnen hebben. Een **pijp lijn** is een logische groep **activiteiten** die samen een taak uitvoeren. De activiteiten in een pijplijn bepalen acties die moeten worden uitgevoerd op uw gegevens. U kunt bijvoorbeeld een Kopieer activiteit gebruiken om gegevens van een SQL Server Data Base naar Azure Blob Storage te kopiëren. Vervolgens kunt u een Hive-activiteit gebruiken waarmee een Hive-script op een Azure HDInsight-cluster wordt uitgevoerd om gegevens uit de Blob-opslag te verwerken om uitvoer gegevens te produceren. Ten slotte kunt u een tweede Kopieer activiteit gebruiken om de uitvoer gegevens te kopiëren naar Azure Synapse Analytics, op het punt waar business intelligence (BI) Reporting-oplossingen zijn gebouwd. Voor meer informatie over pijp lijnen en activiteiten raadpleegt u [pijp lijnen en activiteiten in azure Data Factory](data-factory-create-pipelines.md).

Een activiteit kan nul of meer invoer **gegevens sets** hebben en een of meer uitvoer gegevens sets produceren. Een invoer-gegevensset vertegenwoordigt de invoer voor een activiteit in de pijp lijn en een uitvoer gegevensset vertegenwoordigt de uitvoer voor de activiteit. Met gegevenssets worden gegevens binnen andere gegevensarchieven geïdentificeerd, waaronder tabellen, bestanden, mappen en documenten. Een Azure Blob-gegevensset geeft bijvoorbeeld de BLOB-container en de map in de Blob-opslag van waaruit de pijp lijn de gegevens moet lezen.

Voordat u een gegevensset maakt, maakt u een **gekoppelde service** om uw gegevens archief te koppelen aan de Data Factory. Gekoppelde services zijn te vergelijken met verbindingsreeksen, die de verbindingsinformatie bevatten die Data Factory nodig heeft om verbinding te maken met externe bronnen. Met gegevens sets wordt informatie in de gekoppelde gegevens archieven geïdentificeerd, zoals SQL-tabellen, bestanden, mappen en documenten. Een Azure Storage gekoppelde service koppelt bijvoorbeeld een opslag account aan de data factory. Een Azure Blob-gegevensset vertegenwoordigt de BLOB-container en de map die de invoer-blobs bevat die moeten worden verwerkt.

Hier volgt een voorbeeld scenario. Als u gegevens wilt kopiëren van Blob-opslag naar SQL Database, maakt u twee gekoppelde services: Azure Storage en Azure SQL Database. Maak vervolgens twee gegevens sets: Azure Blob-gegevensset (die verwijst naar de Azure Storage gekoppelde service) en de gegevensset van de Azure SQL-tabel (die verwijst naar de Azure SQL Database gekoppelde service). De Azure Storage-en Azure SQL Database gekoppelde services bevatten verbindings reeksen die Data Factory in runtime gebruiken om respectievelijk verbinding te maken met uw Azure Storage en Azure SQL Database. De Azure Blob-gegevensset bevat de BLOB en BLOB-map die de invoer-blobs in uw Blob-opslag bevat. De gegevensset van de Azure SQL-tabel geeft de SQL-tabel in de SQL database aan waarnaar de gegevens moeten worden gekopieerd.

In het volgende diagram ziet u de relaties tussen pijp lijn, activiteit, gegevensset en gekoppelde service in Data Factory:

![Relatie tussen pijp lijn, activiteit, gegevensset, gekoppelde services](media/data-factory-create-datasets/relationship-between-data-factory-entities.png)

## <a name="dataset-json"></a>JSON van gegevensset
Een gegevensset in Data Factory wordt als volgt in JSON-indeling gedefinieerd:

```json
{
    "name": "<name of dataset>",
    "properties": {
        "type": "<type of dataset: AzureBlob, AzureSql etc...>",
        "external": "<boolean flag to indicate external data. only for input datasets>",
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
| naam |De naam van de gegevensset. Zie [Azure Data Factory naamgevings regels](data-factory-naming-rules.md) voor naamgevings regels. |Ja |NA |
| type |Het type van de gegevensset. Geef een van de typen op die worden ondersteund door Data Factory (bijvoorbeeld: AzureBlob, AzureSqlTable). <br/><br/>Zie [type gegevensset](#Type)voor meer informatie. |Ja |NA |
| structuur |Schema van de gegevensset.<br/><br/>Zie [structuur van gegevensset](#Structure)voor meer informatie. |Nee |NA |
| typeProperties | De type-eigenschappen verschillen voor elk type (bijvoorbeeld: Azure Blob, Azure SQL-tabel). Zie [type gegevensset](#Type)voor meer informatie over de ondersteunde typen en hun eigenschappen. |Ja |NA |
| external | Een Booleaanse vlag die aangeeft of een gegevensset expliciet wordt geproduceerd door een data factory pijp lijn of niet. Als de invoer-gegevensset voor een activiteit niet door de huidige pijp lijn wordt geproduceerd, stelt u deze vlag in op True. Stel deze vlag in op True voor de invoer-gegevensset van de eerste activiteit in de pijp lijn.  |Nee |onjuist |
| availability | Hiermee definieert u het verwerkings venster (bijvoorbeeld per uur of dagelijks) of het segment model voor de productie van de gegevensset. Elke gegevens eenheid die wordt verbruikt en geproduceerd door een uitvoering van een activiteit wordt een gegevens segment genoemd. Als de beschik baarheid van een uitvoer gegevensset dagelijks is ingesteld (Frequency-Day, interval-1), wordt een segment dagelijks geproduceerd. <br/><br/>Zie Beschik baarheid van de gegevensset voor meer informatie. <br/><br/>Zie het artikel [planning en uitvoering](data-factory-scheduling-and-execution.md) voor meer informatie over het model voor het segmenteren van gegevensset. |Ja |NA |
| policy |Hiermee worden de criteria gedefinieerd of de voor waarde waaraan de segmenten van de gegevensset moeten voldoen. <br/><br/>Zie de sectie [gegevensset Policy](#Policy) voor meer informatie. |Nee |NA |

## <a name="dataset-example"></a>Gegevensset-voor beeld
In het volgende voor beeld vertegenwoordigt de gegevensset een tabel met de naam **myTable** in een SQL database.

```json
{
    "name": "DatasetSample",
    "properties": {
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties":
        {
            "tableName": "MyTable"
        },
        "availability":
        {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```

Houd rekening met de volgende punten:

* **type** wordt ingesteld op AzureSqlTable.
* de eigenschap **TableName** type (specifiek voor het type AzureSqlTable) is ingesteld op mytable.
* **linkedServiceName** verwijst naar een gekoppelde service van het type AzureSqlDatabase, die wordt gedefinieerd in het volgende JSON-code fragment.
* de **beschikbaarheids frequentie** wordt ingesteld op dag en het **interval** wordt ingesteld op 1. Dit betekent dat het segment van de gegevensset dagelijks wordt geproduceerd.

**AzureSqlLinkedService** wordt als volgt gedefinieerd:

```json
{
    "name": "AzureSqlLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "description": "",
        "typeProperties": {
            "connectionString": "Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;User ID=<username>@<servername>;Password=<password>;Integrated Security=False;Encrypt=True;Connect Timeout=30"
        }
    }
}
```

In het voor gaande JSON-fragment:

* **type** wordt ingesteld op AzureSqlDatabase.
* de eigenschap **Connections Tring** geeft informatie op om verbinding te maken met een SQL database.

Zoals u kunt zien, definieert de gekoppelde service hoe er verbinding moet worden gemaakt met een SQL database. De gegevensset definieert welke tabel wordt gebruikt als invoer en uitvoer voor de activiteit in een pijp lijn.

> [!IMPORTANT]
> Tenzij er een gegevensset wordt geproduceerd door de pijp lijn, moet deze worden gemarkeerd als **extern**. Deze instelling is doorgaans van toepassing op de invoer van de eerste activiteit in een pijp lijn.

## <a name="dataset-type"></a><a name="Type"></a> Type gegevensset
Het type gegevensset is afhankelijk van het gegevens archief dat u gebruikt. Zie de volgende tabel voor een lijst met gegevens archieven die door Data Factory worden ondersteund. Klik op een gegevens Archief voor informatie over het maken van een gekoppelde service en een gegevensset voor het gegevens archief.

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

> [!NOTE]
> Gegevens archieven met * kunnen on-premises of op een Azure-infra structuur als een service (IaaS) zijn. Voor deze gegevens archieven moet u [Data Management Gateway](data-factory-data-management-gateway.md)installeren.

In het voor beeld in de vorige sectie is het type gegevensset ingesteld op **AzureSqlTable**. Op dezelfde manier wordt voor een Azure Blob-gegevensset het type gegevensset ingesteld op **AzureBlob**, zoals wordt weer gegeven in de volgende JSON:

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

## <a name="dataset-structure"></a><a name="Structure"></a>Structuur van gegevensset
De sectie **structure** is optioneel. Hiermee wordt het schema van de gegevensset gedefinieerd door een verzameling namen en gegevens typen van kolommen te bevatten. U kunt de sectie structure gebruiken om type gegevens op te geven die worden gebruikt voor het converteren van typen en het toewijzen van kolommen van de bron naar het doel. In het volgende voor beeld heeft de gegevensset drie kolommen: `slicetimestamp` , `projectname` en `pageviews` . Ze zijn van het type teken reeks, teken reeks en decimaal.

```json
structure:
[
    { "name": "slicetimestamp", "type": "String"},
    { "name": "projectname", "type": "String"},
    { "name": "pageviews", "type": "Decimal"}
]
```

Elke kolom in de structuur bevat de volgende eigenschappen:

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| naam |De naam van de kolom. |Ja |
| type |Het gegevens type van de kolom.  |Nee |
| culturele |. Op netgebaseerde cultuur die moet worden gebruikt wanneer het type een .NET-type is: `Datetime` of `Datetimeoffset` . De standaardwaarde is `en-us`. |Nee |
| indeling |Indelings teken reeks die moet worden gebruikt wanneer het type een .NET-type is: `Datetime` of `Datetimeoffset` . |Nee |

U kunt aan de hand van de volgende richt lijnen bepalen wanneer u structuur informatie moet toevoegen en wat u in de sectie **structuur** wilt toevoegen.

* **Voor gestructureerde gegevens bronnen** geeft u de sectie structuur alleen op als u de bron kolommen van de kaart wilt opvangen en de namen niet hetzelfde zijn. In dit soort gestructureerde gegevens bronnen worden gegevens schema's en typen informatie opgeslagen samen met de gegevens zelf. Voor beelden van gestructureerde gegevens bronnen zijn SQL Server, Oracle en Azure Table.
  
    Als type-informatie is al beschikbaar voor gestructureerde gegevens bronnen, moet u geen type gegevens opgeven wanneer u de sectie structure insluit.
* **Voor schema op gegevens bronnen lezen (met name Blob Storage)** kunt u ervoor kiezen om gegevens op te slaan zonder schema op te slaan of door gegevens te typen met de gegevens. Neem voor deze typen gegevens bronnen structuur op wanneer u bron kolommen wilt toewijzen aan Sink-kolommen. Neem ook een structuur op wanneer de gegevensset een invoer is voor een Kopieer activiteit en gegevens typen van de bron-gegevensset moeten worden geconverteerd naar systeem eigen typen voor de sink.
    
    Data Factory ondersteunt de volgende waarden voor het leveren van type-informatie in structure: **Int16, Int32, Int64, enkel, dubbel, decimaal, byte [], Boolean, String, GUID, datetime, date time offset en time span**. Deze waarden zijn Common Language Specification (CLS)-compatibel,. Waarden op basis van het type NET.

Data Factory voert automatisch type conversies uit bij het verplaatsen van gegevens uit een brongegevens archief naar een Sink-gegevens archief.

## <a name="dataset-availability"></a>Beschik baarheid van gegevensset
De sectie **Beschik baarheid** in een gegevensset definieert het verwerkings venster (bijvoorbeeld elk uur, dagelijks of wekelijks) voor de gegevensset. Zie [planning en uitvoering](data-factory-scheduling-and-execution.md)voor meer informatie over de activiteit Windows.

In de volgende sectie Beschik baarheid wordt aangegeven dat de uitvoer gegevensset per uur wordt geproduceerd, of de invoer gegevensset is elk uur beschikbaar:

```json
"availability":
{
    "frequency": "Hour",
    "interval": 1
}
```

Als de pijp lijn de volgende begin-en eind tijden heeft:

```json
    "start": "2016-08-25T00:00:00Z",
    "end": "2016-08-25T05:00:00Z",
```

De uitvoer gegevensset wordt per uur geproduceerd binnen de begin-en eind tijd van de pijp lijn. Daarom zijn er vijf gegevensset-segmenten geproduceerd door deze pijp lijn, één voor elk activiteiten venster (12 uur, 1 AM-2 uur, 2 uur, 3 uur, 3 uur-4 uur, 4 AM-5 uur).

In de volgende tabel worden de eigenschappen beschreven die u kunt gebruiken in het gedeelte Beschik baarheid:

| Eigenschap | Beschrijving | Vereist | Standaard |
| --- | --- | --- | --- |
| frequency |Hiermee geeft u de tijds eenheid voor de segment productie van gegevensset op.<br/><br/><b>Ondersteunde frequentie</b>: minuut, uur, dag, week, maand |Ja |NA |
| interval |Hiermee geeft u een vermenigvuldigings factor voor frequentie op.<br/><br/>"Frequentie x-interval" bepaalt hoe vaak het segment wordt geproduceerd. Als u bijvoorbeeld wilt dat de gegevensset op elk uur wordt gesegmenteerd, stelt u de <b>frequentie</b> in op <b>uur</b>en het <b>interval</b> op <b>1</b>.<br/><br/>Houd er rekening mee dat als u een **frequentie** opgeeft als **minuut**, het interval moet worden ingesteld op ten minste 15. |Ja |NA |
| stijl |Hiermee geeft u op of het segment moet worden geproduceerd aan het begin of einde van het interval.<ul><li>StartOfInterval</li><li>EndOfInterval</li></ul>Als de **frequentie** is ingesteld op **maand** en **stijl** is ingesteld op **EndOfInterval**, wordt het segment gemaakt op de laatste dag van de maand. Als **stijl** is ingesteld op **StartOfInterval**, wordt het segment gegenereerd op de eerste dag van de maand.<br/><br/>Als de **frequentie** is ingesteld op **dag** en **stijl** is ingesteld op **EndOfInterval**, wordt het segment gemaakt in het afgelopen uur van de dag.<br/><br/>Als de **frequentie** is ingesteld op **uur** en de **stijl** is ingesteld op **EndOfInterval**, wordt het segment aan het einde van het uur geproduceerd. Bijvoorbeeld, voor een segment voor de periode van 1 PM-2 uur, het segment wordt geproduceerd op 2 uur. |Nee |EndOfInterval |
| anchorDateTime |Definieert de absolute positie in de tijd die door de scheduler wordt gebruikt om segment grenzen van het gegevensset te berekenen. <br/><br/>Houd er rekening mee dat als deze eigenschap datum onderdelen bevat die nauw keuriger zijn dan de opgegeven frequentie, de nauw keurigere delen worden genegeerd. Als het **interval** bijvoorbeeld **elk uur** is (frequentie: uur en interval: 1), en het **anchorDateTime** het **aantal minuten en seconden** bevat, worden de minuten en seconden delen van **anchorDateTime** genegeerd. |Nee |01/01/0001 |
| offset |Tijds duur waarmee het begin en einde van alle segmenten van de gegevensset worden verschoven. <br/><br/>Houd er rekening mee dat als zowel **anchorDateTime** als **Offset** worden opgegeven, het resultaat de gecombineerde verschuiving is. |Nee |NA |

### <a name="offset-example"></a>offset-voor beeld
Standaard `"frequency": "Day", "interval": 1` beginnen segmenten van 12 uur (middernacht) Coordinated Universal Time (UTC). Als u wilt dat de begin tijd 6 uur UTC-tijd in plaats daarvan instelt, stelt u de verschuiving in zoals weer gegeven in het volgende code fragment:

```json
"availability":
{
    "frequency": "Day",
    "interval": 1,
    "offset": "06:00:00"
}
```
### <a name="anchordatetime-example"></a>anchorDateTime-voor beeld
In het volgende voor beeld wordt de gegevensset elke 23 uur gemaakt. Het eerste segment begint op het tijdstip dat is opgegeven door **anchorDateTime**, dat is ingesteld op `2017-04-19T08:00:00` (UTC).

```json
"availability":
{
    "frequency": "Hour",
    "interval": 23,
    "anchorDateTime":"2017-04-19T08:00:00"
}
```

### <a name="offsetstyle-example"></a>voor beeld van verschuiving/stijl
De volgende gegevensset is maandelijks en wordt geproduceerd op het derde van elke maand om 8:00 uur ( `3.08:00:00` ):

```json
"availability": {
    "frequency": "Month",
    "interval": 1,
    "offset": "3.08:00:00", 
    "style": "StartOfInterval"
}
```

## <a name="dataset-policy"></a><a name="Policy"></a>Beleid voor gegevensset
De **beleids** sectie in de definitie van de gegevensset definieert de criteria of de voor waarde waaraan de segmenten van de gegevensset moeten voldoen.

### <a name="validation-policies"></a>Validatie beleid
| Beleidsnaam | Beschrijving | Toegepast op | Vereist | Standaard |
| --- | --- | --- | --- | --- |
| minimumSizeMB |Valideert dat de gegevens in de **Azure Blob-opslag** voldoen aan de minimale grootte vereisten (in mega bytes). |Azure Blob Storage |Nee |NA |
| minimumRows |Valideert dat de gegevens in een **Azure-SQL database** of een **Azure-tabel** het minimum aantal rijen bevatten. |<ul><li>Azure SQL Database</li><li>Azure Table</li></ul> |Nee |NA |

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

**minimumRows:**

```json
"policy":
{
    "validation":
    {
        "minimumRows": 100
    }
}
```

### <a name="external-datasets"></a>Externe gegevens sets
Externe gegevens sets zijn degene die niet worden geproduceerd door een actieve pijp lijn in de data factory. Als de gegevensset is gemarkeerd als **extern**, kan het **ExternalData** -beleid worden gedefinieerd om het gedrag van de beschik baarheid van het gegevensset segment te beïnvloeden.

Tenzij er een gegevensset wordt geproduceerd door Data Factory, moet deze worden gemarkeerd als **extern**. Deze instelling is in het algemeen van toepassing op de invoer van de eerste activiteit in een pijp lijn, tenzij activiteit of pijplijn keten wordt gebruikt.

| Naam | Beschrijving | Vereist | Standaardwaarde |
| --- | --- | --- | --- |
| dataDelay |De tijd om de controle over de beschik baarheid van de externe gegevens voor het opgegeven segment te vertragen. U kunt bijvoorbeeld een controle op elk uur uitstellen met behulp van deze instelling.<br/><br/>De instelling is alleen van toepassing op de huidige tijd. Als het bijvoorbeeld 1:00 PM nu is en deze waarde 10 minuten is, begint de validatie om 1:10 PM.<br/><br/>Houd er rekening mee dat deze instelling geen invloed heeft op segmenten in het verleden. Segmenten met **eind tijd**  +  **dataDelay**  <  worden **nu** zonder vertraging verwerkt.<br/><br/>Tijden groter dan 23:59 uur moeten worden opgegeven met behulp van de `day.hours:minutes:seconds` notatie. Als u bijvoorbeeld 24 uur wilt opgeven, gebruikt u niet 24:00:00. Gebruik in plaats daarvan 1,00:00:00. Als u 24:00:00 gebruikt, wordt dit beschouwd als 24 dagen (24.00:00:00). Geef voor 1 dag en 4 uur 1:04:00:00 op. |Nee |0 |
| retryInterval |De wacht tijd tussen een storing en de volgende poging. Deze instelling is van toepassing op de huidige tijd. Als de vorige poging is mislukt, wordt de volgende poging na de **retryInterval** -periode. <br/><br/>Als deze 1:00 PM nu is, beginnen we met de eerste try. Als de duur voor het volt ooien van de eerste validatie controle 1 minuut is en de bewerking is mislukt, is de volgende nieuwe poging bij 1:00 + 1min (duur) + 1min (interval voor opnieuw proberen) = 1:02 PM. <br/><br/>Voor segmenten in het verleden is er geen vertraging. De nieuwe poging vindt direct plaats. |Nee |00:01:00 (1 minuut) |
| retryTimeout |De time-out voor elke nieuwe poging.<br/><br/>Als deze eigenschap is ingesteld op 10 minuten, moet de validatie binnen 10 minuten worden voltooid. Als het langer dan tien minuten duurt om de validatie uit te voeren, treedt er een time-out op voor de poging.<br/><br/>Als alle pogingen voor de validerings tijd zijn, wordt het segment gemarkeerd als **out**. |Nee |00:10:00 (10 minuten) |
| maximumRetry |Het aantal keren dat er moet worden gecontroleerd op de beschik baarheid van de externe gegevens. De Maxi maal toegestane waarde is 10. |Nee |3 |


## <a name="create-datasets"></a>Gegevenssets maken
U kunt gegevens sets maken met behulp van een van deze hulpprogram ma's of Sdk's:

- De wizard Kopiëren
- Visual Studio
- PowerShell
- Azure Resource Manager-sjabloon
- REST-API
- .NET API

Raadpleeg de volgende zelf studies voor stapsgewijze instructies voor het maken van pijp lijnen en gegevens sets met behulp van een van deze hulpprogram ma's of Sdk's:

- [Een pijplijn met een transformatieactiviteit voor gegevens bouwen](data-factory-build-your-first-pipeline.md)
- [Een pijp lijn bouwen met een activiteit voor gegevens verplaatsing](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)

Nadat een pijp lijn is gemaakt en geïmplementeerd, kunt u uw pijp lijnen beheren en bewaken met behulp van de Azure Portal-Blades of de app voor bewaking en beheer. Zie de volgende onderwerpen voor stapsgewijze instructies:

- [Pijp lijnen bewaken en beheren met behulp van Azure Portal-Blades](data-factory-monitor-manage-pipelines.md)
- [Pijp lijnen bewaken en beheren met behulp van de app voor bewaking en beheer](data-factory-monitor-manage-app.md)

## <a name="scoped-datasets"></a>Bereik gegevens sets
U kunt gegevens sets maken die zijn afgestemd op een pijp lijn met behulp van de eigenschap **data sets** . Deze gegevens sets kunnen alleen worden gebruikt door activiteiten binnen deze pijp lijn, niet door activiteiten in andere pijp lijnen. In het volgende voor beeld wordt een pijp lijn met twee gegevens sets (input dataset-RDC en output dataset-RDC) gedefinieerd die moeten worden gebruikt in de pijp lijn.

> [!IMPORTANT]
> Gegevens sets met een bereik worden alleen ondersteund met eenmalige pijp lijnen (waarbij **pipelineMode** is ingesteld op **eenmalige**). Zie [eenmalige-pijp lijn](data-factory-create-pipelines.md#onetime-pipeline) voor meer informatie.
>
>

```json
{
    "name": "CopyPipeline-rdc",
    "properties": {
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource",
                        "recursive": false
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "InputDataset-rdc"
                    }
                ],
                "outputs": [
                    {
                        "name": "OutputDataset-rdc"
                    }
                ],
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1,
                    "style": "StartOfInterval"
                },
                "name": "CopyActivity-0"
            }
        ],
        "start": "2016-02-28T00:00:00Z",
        "end": "2016-02-28T00:00:00Z",
        "isPaused": false,
        "pipelineMode": "OneTime",
        "expirationTime": "15.00:00:00",
        "datasets": [
            {
                "name": "InputDataset-rdc",
                "properties": {
                    "type": "AzureBlob",
                    "linkedServiceName": "InputLinkedService-rdc",
                    "typeProperties": {
                        "fileName": "emp.txt",
                        "folderPath": "adftutorial/input",
                        "format": {
                            "type": "TextFormat",
                            "rowDelimiter": "\n",
                            "columnDelimiter": ","
                        }
                    },
                    "availability": {
                        "frequency": "Day",
                        "interval": 1
                    },
                    "external": true,
                    "policy": {}
                }
            },
            {
                "name": "OutputDataset-rdc",
                "properties": {
                    "type": "AzureBlob",
                    "linkedServiceName": "OutputLinkedService-rdc",
                    "typeProperties": {
                        "fileName": "emp.txt",
                        "folderPath": "adftutorial/output",
                        "format": {
                            "type": "TextFormat",
                            "rowDelimiter": "\n",
                            "columnDelimiter": ","
                        }
                    },
                    "availability": {
                        "frequency": "Day",
                        "interval": 1
                    },
                    "external": false,
                    "policy": {}
                }
            }
        ]
    }
}
```

## <a name="next-steps"></a>Volgende stappen
- Zie [pijp lijnen maken](data-factory-create-pipelines.md)voor meer informatie over pijp lijnen.
- Zie [planning en uitvoering in azure Data Factory](data-factory-scheduling-and-execution.md)voor meer informatie over hoe pijp lijnen worden gepland en uitgevoerd.
