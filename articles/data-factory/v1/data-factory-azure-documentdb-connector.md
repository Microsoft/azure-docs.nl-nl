---
title: Gegevens verplaatsen van/naar Azure Cosmos DB
description: Meer informatie over het verplaatsen van gegevens naar of van Azure Cosmos DB verzameling met behulp van Azure Data Factory
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/22/2018
robots: noindex
ms.openlocfilehash: a90b26c677c3d53794bce3c2ff48c618415e4cf2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100379366"
---
# <a name="move-data-to-and-from-azure-cosmos-db-using-azure-data-factory"></a>Gegevens verplaatsen van en naar Azure Cosmos DB met behulp van Azure Data Factory
> [!div class="op_single_selector" title1="Selecteer de versie van de Data Factory-service die u gebruikt:"]
> * [Versie 1:](data-factory-azure-documentdb-connector.md)
> * [Versie 2 (huidige versie)](../connector-azure-cosmos-db.md)

> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. Als u de huidige versie van de Data Factory-service gebruikt, raadpleegt u [Azure Cosmos DB-connector in v2](../connector-azure-cosmos-db.md).

In dit artikel wordt uitgelegd hoe u de Kopieer activiteit in Azure Data Factory kunt gebruiken om gegevens te verplaatsen van/naar Azure Cosmos DB (SQL-API). Het is gebaseerd op het artikel [activiteiten voor gegevens verplaatsing](data-factory-data-movement-activities.md) , dat een algemeen overzicht geeft van de verplaatsing van gegevens met de Kopieer activiteit.

U kunt gegevens van elk ondersteund bron gegevens archief kopiëren naar Azure Cosmos DB of van Azure Cosmos DB naar een ondersteunde Sink-gegevens opslag. Zie de tabel [ondersteunde gegevens archieven](data-factory-data-movement-activities.md#supported-data-stores-and-formats) voor een lijst met gegevens archieven die worden ondersteund als bronnen of sinks op basis van de Kopieer activiteit.

> [!IMPORTANT]
> Azure Cosmos DB connector ondersteunt alleen de SQL-API.

Zie [JSON-documenten importeren/exporteren](#importexport-json-documents)als u gegevens wilt kopiëren naar/van json-bestanden of een andere Cosmos DB verzameling.

## <a name="getting-started"></a>Aan de slag
U kunt een pijp lijn maken met een Kopieer activiteit die gegevens verplaatst van/naar Azure Cosmos DB met behulp van verschillende hulpprogram ma's/Api's.

De eenvoudigste manier om een pijp lijn te maken, is met behulp van de **wizard kopiëren**. Zie [zelf studie: een pijp lijn maken met behulp van de wizard kopiëren](data-factory-copy-data-wizard-tutorial.md) voor een snelle walkthrough over het maken van een pijp lijn met behulp van de wizard gegevens kopiëren.

U kunt ook de volgende hulpprogram ma's gebruiken om een pijp lijn te maken: **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager sjabloon**, **.net API** en **rest API**. Zie [zelf studie Kopieer activiteit](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) voor stapsgewijze instructies voor het maken van een pijp lijn met een Kopieer activiteit.

Ongeacht of u de hulpprogram ma's of Api's gebruikt, voert u de volgende stappen uit om een pijp lijn te maken waarmee gegevens uit een brongegevens archief naar een Sink-gegevens archief worden verplaatst:

1. Maak **gekoppelde services** om invoer-en uitvoer gegevens archieven te koppelen aan uw Data Factory.
2. Gegevens **sets** maken om invoer-en uitvoer gegevens voor de Kopieer bewerking weer te geven.
3. Maak een **pijp lijn** met een Kopieer activiteit die een gegevensset als invoer en een gegevensset als uitvoer gebruikt.

Wanneer u de wizard gebruikt, worden automatisch JSON-definities voor deze Data Factory entiteiten (gekoppelde services, gegevens sets en de pijp lijn) gemaakt. Wanneer u hulpprogram ma's/Api's (met uitzonde ring van .NET API) gebruikt, definieert u deze Data Factory entiteiten met behulp van de JSON-indeling. Zie de sectie [JSON-voor beelden](#json-examples) in dit artikel voor steek proeven met JSON-definities voor Data Factory entiteiten die worden gebruikt voor het kopiëren van gegevens naar/van Cosmos db.

De volgende secties bevatten informatie over de JSON-eigenschappen die worden gebruikt voor het definiëren van Data Factory entiteiten die specifiek zijn voor Cosmos DB:

## <a name="linked-service-properties"></a>Eigenschappen van gekoppelde service
In de volgende tabel vindt u een beschrijving van de JSON-elementen die specifiek zijn voor Azure Cosmos DB gekoppelde service.

| **Eigenschap** | **Beschrijving** | **Vereist** |
| --- | --- | --- |
| type |De eigenschap type moet worden ingesteld op: **DocumentDb** |Ja |
| connectionString |Geef de gegevens op die nodig zijn om verbinding te maken met Azure Cosmos DB Data Base. |Ja |

Voorbeeld:

```JSON
{
  "name": "CosmosDbLinkedService",
  "properties": {
    "type": "DocumentDb",
    "typeProperties": {
      "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
    }
  }
}
```

## <a name="dataset-properties"></a>Eigenschappen van gegevensset
Raadpleeg het artikel [gegevens sets maken](data-factory-create-datasets.md) voor een volledige lijst met secties & eigenschappen die beschikbaar zijn voor het definiëren van gegevens sets. Secties als structuur, Beschik baarheid en beleid van een gegevensset-JSON zijn vergelijkbaar voor alle typen gegevens sets (Azure SQL, Azure Blob, Azure Table, enzovoort).

De sectie typeProperties verschilt voor elk type gegevensset en bevat informatie over de locatie van de gegevens in het gegevens archief. De sectie typeProperties voor de gegevensset van het type **DocumentDbCollection** heeft de volgende eigenschappen.

| **Eigenschap** | **Beschrijving** | **Vereist** |
| --- | --- | --- |
| collectionName |De naam van de verzameling Cosmos DB documenten. |Ja |

Voorbeeld:

```JSON
{
  "name": "PersonCosmosDbTable",
  "properties": {
    "type": "DocumentDbCollection",
    "linkedServiceName": "CosmosDbLinkedService",
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
### <a name="schema-by-data-factory"></a>Schema door Data Factory
Voor gegevens archieven zonder schema, zoals Azure Cosmos DB, leidt de Data Factory-service het schema op een van de volgende manieren af:

1. Als u de structuur van gegevens opgeeft met behulp van de eigenschap **structure** in de definitie van de gegevensset, wordt deze structuur door de Data Factory-service als schema geaccepteerd. Als in dit geval een rij geen waarde voor een kolom bevat, wordt er een null-waarde voor gegeven.
2. Als u de structuur van gegevens niet opgeeft met behulp van de eigenschap **structure** in de definitie van de gegevensset, wordt het schema door de Data Factory-service door de eerste rij in de gegevens afgeleid. Als in dit geval de eerste rij niet het volledige schema bevat, ontbreken sommige kolommen in het resultaat van de Kopieer bewerking.

Daarom is het best practice voor gegevens bronnen met een schema om de structuur van de gegevens op te geven met behulp van de eigenschap **structure** .

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit
Raadpleeg het artikel [pijp lijnen maken](data-factory-create-pipelines.md) voor een volledige lijst met secties & eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. Eigenschappen zoals naam, beschrijving, invoer-en uitvoer tabellen en beleid zijn beschikbaar voor alle typen activiteiten.

> [!NOTE]
> De Kopieer activiteit heeft slechts één invoer en produceert slechts één uitvoer.

De eigenschappen die beschikbaar zijn in het gedeelte typeProperties van de activiteit, verschillen per type activiteit en in het geval van een Kopieer activiteit, afhankelijk van de typen bronnen en Sinks.

In het geval van een Kopieer activiteit wanneer de bron van het type **DocumentDbCollectionSource** de volgende eigenschappen zijn beschikbaar in de sectie **typeProperties** :

| **Eigenschap** | **Beschrijving** | **Toegestane waarden** | **Vereist** |
| --- | --- | --- | --- |
| query |Geef de query op om gegevens te lezen. |De query teken reeks die wordt ondersteund door Azure Cosmos DB. <br/><br/>Voorbeeld: `SELECT c.BusinessEntityID, c.PersonType, c.NameStyle, c.Title, c.Name.First AS FirstName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |Nee <br/><br/>Indien niet opgegeven, de SQL-instructie die wordt uitgevoerd: `select <columns defined in structure> from mycollection` |
| nestingSeparator |Speciaal teken om aan te geven dat het document is genest |Wille keurig teken. <br/><br/>Azure Cosmos DB is een NoSQL-Archief voor JSON-documenten, waarbij geneste structuren zijn toegestaan. Azure Data Factory stelt de gebruiker in staat om hiërarchie te geven via nestingSeparator, dat wil zeggen "." in de bovenstaande voor beelden. Met het scheidings teken genereert de Kopieer activiteit het object ' name ' met drie onderliggende elementen eerst, middelste en laatste, volgens ' naam. First ', ' naam. midden ' en ' naam. last ' in de tabel definitie. |Nee |

**DocumentDbCollectionSink** ondersteunt de volgende eigenschappen:

| **Eigenschap** | **Beschrijving** | **Toegestane waarden** | **Vereist** |
| --- | --- | --- | --- |
| nestingSeparator |Een speciaal teken in de naam van de bron kolom om aan te geven dat genest document nodig is. <br/><br/>Voor beeld: `Name.First` in de uitvoer tabel levert de volgende JSON-structuur in het Cosmos DB document:<br/><br/>"Naam": {<br/>    "Eerste": "John"<br/>}, |Teken dat wordt gebruikt voor het scheiden van geneste niveaus.<br/><br/>De standaard waarde is `.` (dot). |Teken dat wordt gebruikt voor het scheiden van geneste niveaus. <br/><br/>De standaard waarde is `.` (dot). |
| writeBatchSize |Aantal parallelle aanvragen voor het Azure Cosmos DB-service om documenten te maken.<br/><br/>U kunt de prestaties afstemmen bij het kopiëren van gegevens naar/van Cosmos DB met behulp van deze eigenschap. U kunt een betere prestaties verwachten wanneer u writeBatchSize verhoogt, omdat er meer parallelle aanvragen naar Cosmos DB worden verzonden. U moet er echter voor zorgen dat het fout bericht niet kan worden vertraagd: ' de aanvraag snelheid is groot '.<br/><br/>Beperking wordt bepaald door een aantal factoren, zoals de grootte van documenten, het aantal voor waarden in documenten, het indexerings beleid van de doel verzameling, enzovoort. Voor kopieer bewerkingen kunt u een betere verzameling (bijvoorbeeld S3) gebruiken om de meeste door Voer beschikbaar te hebben (2.500 aanvraag eenheden per seconde). |Geheel getal |Nee (standaard: 5) |
| writeBatchTimeout |Wacht tijd voordat de bewerking is voltooid voordat er een time-out optreedt. |tijdsbestek<br/><br/> Voor beeld: "00:30:00" (30 minuten). |Nee |

## <a name="importexport-json-documents"></a>JSON-documenten importeren/exporteren
Met deze Cosmos DB-connector kunt u eenvoudig

* Importeer JSON-documenten uit verschillende bronnen in Cosmos DB, waaronder Azure Blob, Azure Data Lake, on-premises bestands systeem of andere op bestanden gebaseerde archieven die door Azure Data Factory worden ondersteund.
* JSON-documenten uit Cosmos DB verzameling exporteren naar verschillende archieven op basis van bestanden.
* Migreer gegevens tussen twee Cosmos DB verzamelingen.

Om een dergelijke schema-neutraal kopie te krijgen,
* Wanneer u de wizard kopiëren gebruikt, schakelt u de optie **' exporteren naar json-bestanden of Cosmos DB verzameling ' in** .
* Wanneer u JSON Editing wilt gebruiken, geeft u de sectie ' Structure ' niet op in Cosmos DB gegevensset (s) of de eigenschap ' nestingSeparator ' op Cosmos DB bron/sink in Kopieer activiteit. Als u wilt importeren uit/exporteren naar JSON-bestanden, geeft u in de bestands archief-gegevensset notatie op als ' JsonFormat ', config ' filePattern ' en slaat u de instellingen voor de rest-indeling op. Zie de sectie [JSON-indeling](data-factory-supported-file-and-compression-formats.md#json-format) voor meer informatie.

## <a name="json-examples"></a>JSON-voor beelden
De volgende voor beelden bieden voor beeld van JSON-definities die u kunt gebruiken om een pijp lijn te maken met behulp van [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) of [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Ze laten zien hoe u gegevens kunt kopiëren van en naar Azure Cosmos DB en Azure Blob Storage. Gegevens kunnen echter **rechtstreeks** vanuit een van de bronnen naar een van de [hier](data-factory-data-movement-activities.md#supported-data-stores-and-formats) aangegeven sinks worden gekopieerd met behulp van de Kopieer activiteit in azure Data Factory.

## <a name="example-copy-data-from-azure-cosmos-db-to-azure-blob"></a>Voor beeld: gegevens kopiëren van Azure Cosmos DB naar Azure Blob
In het onderstaande voor beeld ziet u:

1. Een gekoppelde service van het type [DocumentDb](#linked-service-properties).
2. Een gekoppelde service van het type [opslag](data-factory-azure-blob-connector.md#linked-service-properties).
3. Een invoer- [gegevensset](data-factory-create-datasets.md) van het type [DocumentDbCollection](#dataset-properties).
4. Een uitvoer [gegevensset](data-factory-create-datasets.md) van het type [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. Een [pijp lijn](data-factory-create-pipelines.md) met een Kopieer activiteit die gebruikmaakt van [DocumentDbCollectionSource](#copy-activity-properties) en [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

In het voor beeld worden gegevens in Azure Cosmos DB naar Azure Blob gekopieerd. De JSON-eigenschappen die in deze steek proeven worden gebruikt, worden beschreven in secties die volgen op de voor beelden.

**Azure Cosmos DB gekoppelde service:**

```JSON
{
  "name": "CosmosDbLinkedService",
  "properties": {
    "type": "DocumentDb",
    "typeProperties": {
      "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
    }
  }
}
```
**Gekoppelde Azure Blob Storage-service:**

```JSON
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
**Invoer gegevensset van Azure document DB:**

In het voor beeld wordt ervan uitgegaan dat u een verzameling met de naam **persoon** in een Azure Cosmos DB-Data Base hebt.

Externe instelling: "True" en externalData-beleid opgeven de Azure Data Factory-service dat de tabel zich buiten het data factory bevindt en niet door een activiteit in de data factory wordt gemaakt.

```JSON
{
  "name": "PersonCosmosDbTable",
  "properties": {
    "type": "DocumentDbCollection",
    "linkedServiceName": "CosmosDbLinkedService",
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

**Azure Blob-uitvoer gegevensset:**

Gegevens worden elk uur naar een nieuwe BLOB gekopieerd met het pad voor de blob die de specifieke datum/tijd weergeeft met de granulatie van het uur.

```JSON
{
  "name": "PersonBlobTableOut",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "docdb",
      "format": {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "nullValue": "NULL"
      }
    },
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```
Voor beeld van een JSON-document in de verzameling persoon in een Cosmos DB-Data Base:

```JSON
{
  "PersonId": 2,
  "Name": {
    "First": "Jane",
    "Middle": "",
    "Last": "Doe"
  }
}
```
Cosmos DB biedt ondersteuning voor het opvragen van documenten met behulp van een SQL like-syntaxis voor hiërarchische JSON-documenten.

Voorbeeld:

```sql
SELECT Person.PersonId, Person.Name.First AS FirstName, Person.Name.Middle as MiddleName, Person.Name.Last AS LastName FROM Person
```

Met de volgende pijp lijn worden gegevens uit de verzameling persoon in de Azure Cosmos DB-Data Base naar een Azure-Blob gekopieerd. Als onderdeel van de Kopieer activiteit zijn de invoer-en uitvoer gegevens sets opgegeven.

```JSON
{
  "name": "DocDbToBlobPipeline",
  "properties": {
    "activities": [
      {
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
        "inputs": [
          {
            "name": "PersonCosmosDbTable"
          }
        ],
        "outputs": [
          {
            "name": "PersonBlobTableOut"
          }
        ],
        "policy": {
          "concurrency": 1
        },
        "name": "CopyFromDocDbToBlob"
      }
    ],
    "start": "2015-04-01T00:00:00Z",
    "end": "2015-04-02T00:00:00Z"
  }
}
```
## <a name="example-copy-data-from-azure-blob-to-azure-cosmos-db"></a>Voor beeld: gegevens kopiëren van een Azure-Blob naar Azure Cosmos DB
In het onderstaande voor beeld ziet u:

1. Een gekoppelde service van het type DocumentDb.
2. Een gekoppelde service van het type [opslag](data-factory-azure-blob-connector.md#linked-service-properties).
3. Een invoer- [gegevensset](data-factory-create-datasets.md) van het type [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. Een uitvoer [gegevensset](data-factory-create-datasets.md) van het type DocumentDbCollection.
5. Een [pijp lijn](data-factory-create-pipelines.md) met een Kopieer activiteit die gebruikmaakt van [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) en DocumentDbCollectionSink.

In het voor beeld worden gegevens gekopieerd van de Azure-Blob naar Azure Cosmos DB. De JSON-eigenschappen die in deze steek proeven worden gebruikt, worden beschreven in secties die volgen op de voor beelden.

**Gekoppelde Azure Blob Storage-service:**

```JSON
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
**Azure Cosmos DB gekoppelde service:**

```JSON
{
  "name": "CosmosDbLinkedService",
  "properties": {
    "type": "DocumentDb",
    "typeProperties": {
      "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
    }
  }
}
```
**Invoer gegevensset voor Azure Blob:**

```JSON
{
  "name": "PersonBlobTableIn",
  "properties": {
    "structure": [
      {
        "name": "Id",
        "type": "Int"
      },
      {
        "name": "FirstName",
        "type": "String"
      },
      {
        "name": "MiddleName",
        "type": "String"
      },
      {
        "name": "LastName",
        "type": "String"
      }
    ],
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "fileName": "input.csv",
      "folderPath": "docdb",
      "format": {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "nullValue": "NULL"
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
**Uitvoer gegevensset Azure Cosmos DB:**

In het voor beeld worden gegevens gekopieerd naar een verzameling met de naam ' persoon '.

```JSON
{
  "name": "PersonCosmosDbTableOut",
  "properties": {
    "structure": [
      {
        "name": "Id",
        "type": "Int"
      },
      {
        "name": "Name.First",
        "type": "String"
      },
      {
        "name": "Name.Middle",
        "type": "String"
      },
      {
        "name": "Name.Last",
        "type": "String"
      }
    ],
    "type": "DocumentDbCollection",
    "linkedServiceName": "CosmosDbLinkedService",
    "typeProperties": {
      "collectionName": "Person"
    },
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```
Met de volgende pijp lijn worden gegevens gekopieerd van de Azure-Blob naar de verzameling personen in de Cosmos DB. Als onderdeel van de Kopieer activiteit zijn de invoer-en uitvoer gegevens sets opgegeven.

```JSON
{
  "name": "BlobToDocDbPipeline",
  "properties": {
    "activities": [
      {
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
              "ColumnMappings": "FirstName: Name.First, MiddleName: Name.Middle, LastName: Name.Last, BusinessEntityID: BusinessEntityID, PersonType: PersonType, NameStyle: NameStyle, Title: Title, Suffix: Suffix, EmailPromotion: EmailPromotion, rowguid: rowguid, ModifiedDate: ModifiedDate"
          }
        },
        "inputs": [
          {
            "name": "PersonBlobTableIn"
          }
        ],
        "outputs": [
          {
            "name": "PersonCosmosDbTableOut"
          }
        ],
        "policy": {
          "concurrency": 1
        },
        "name": "CopyFromBlobToDocDb"
      }
    ],
    "start": "2015-04-14T00:00:00Z",
    "end": "2015-04-15T00:00:00Z"
  }
}
```
Als de invoer van de voor beeld-BLOB zo is

```
1,John,,Doe
```
De uitvoer JSON in Cosmos DB is dan als volgt:

```JSON
{
  "Id": 1,
  "Name": {
    "First": "John",
    "Middle": null,
    "Last": "Doe"
  },
  "id": "a5e8595c-62ec-4554-a118-3940f4ff70b6"
}
```
Azure Cosmos DB is een NoSQL-Archief voor JSON-documenten, waarbij geneste structuren zijn toegestaan. Met Azure Data Factory kan de gebruiker de hiërarchie aanduiden via **nestingSeparator**, dat wil zeggen "." in dit voor beeld. Met het scheidings teken genereert de Kopieer activiteit het object ' name ' met drie onderliggende elementen eerst, middelste en laatste, volgens ' naam. First ', ' naam. midden ' en ' naam. last ' in de tabel definitie.

## <a name="appendix"></a>Bijlage
1. **Vraag:** Ondersteunt de Kopieer activiteit een update van bestaande records?

    **Antwoord**: Nee.
2. **Vraag:** Hoe probeert een kopie opnieuw te Azure Cosmos DB worden verwerkt met al gekopieerde records?

    **Antwoord:** Als records een ID-veld hebben en er wordt geprobeerd een record met dezelfde ID in te voegen, wordt er een fout gegenereerd door de Kopieer bewerking.
3. **Vraag:** Ondersteunt Data Factory [bereik of gegevens partities op basis van hashes](../../cosmos-db/partitioning-overview.md)?

    **Antwoord**: Nee.
4. **Vraag:** Kan ik meer dan één Azure Cosmos DB verzameling opgeven voor een tabel?

    **Antwoord**: Nee. Op dit moment kan slechts één verzameling worden opgegeven.

## <a name="performance-and-tuning"></a>Prestaties en afstemming
Zie [Kopieer activiteit prestaties & afstemmings handleiding](data-factory-copy-activity-performance.md) voor meer informatie over de belangrijkste factoren die invloed hebben op de prestaties van het verplaatsen van gegevens (Kopieer activiteit) in azure Data Factory en verschillende manieren om deze te optimaliseren.