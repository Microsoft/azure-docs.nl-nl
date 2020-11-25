---
title: Gegevens kopiëren en transformeren in Azure Cosmos DB (SQL-API)
description: Informatie over het kopiëren van gegevens van en naar Azure Cosmos DB (SQL-API) en het transformeren van gegevens in Azure Cosmos DB (SQL API) met behulp van Data Factory.
services: data-factory, cosmosdb
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: multiple
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/11/2019
ms.openlocfilehash: bb9f2673eb080ee2919297fcbb5199f99d176bce
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96013680"
---
# <a name="copy-and-transform-data-in-azure-cosmos-db-sql-api-by-using-azure-data-factory"></a>Gegevens in Azure Cosmos DB (SQL API) kopiëren en transformeren met behulp van Azure Data Factory

> [!div class="op_single_selector" title1="Selecteer de versie van de Data Factory-service die u gebruikt:"]
> * [Versie 1:](v1/data-factory-azure-documentdb-connector.md)
> * [Huidige versie](connector-azure-cosmos-db.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In dit artikel wordt beschreven hoe u de kopieeractiviteit in Azure Data Factory kunt gebruiken om gegevens te kopiëren van en naar Azure Cosmos DB (SQL API), en een gegevensstroom kunt gebruiken om gegevens in Azure Cosmos DB (SQL API) te transformeren. Lees het [artikel Inleiding](introduction.md)voor meer informatie over Azure Data Factory.



>[!NOTE]
>Deze connector ondersteunt alleen Cosmos DB SQL-API. Raadpleeg voor MongoDB-API [de connector voor de API van Azure Cosmos DB voor MongoDb](connector-azure-cosmos-db-mongodb-api.md). Andere API-typen worden momenteel niet ondersteund.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze Azure Cosmos DB-connector (SQL API) wordt ondersteund voor de volgende activiteiten:

- [Kopieer activiteit](copy-activity-overview.md) met een [ondersteunde bron/Sink-matrix](copy-activity-overview.md)
- [Gegevens stroom toewijzen](concepts-data-flow-overview.md)
- [Activiteit Lookup](control-flow-lookup-activity.md)

Voor kopieer activiteit ondersteunt deze Azure Cosmos DB-connector (SQL API):

- Gegevens kopiëren van en naar de Azure Cosmos DB [SQL-API](../cosmos-db/introduction.md).
- Schrijf naar Azure Cosmos DB als **Insert** of **upsert**.
- JSON-documenten importeren en exporteren als-is of gegevens kopiëren van of naar een gegevensset in tabel vorm. Voor beelden zijn een SQL database en een CSV-bestand. Zie [JSON-documenten importeren en exporteren](#import-and-export-json-documents)als u documenten wilt kopiëren naar of van json-bestanden of naar of van een andere Azure Cosmos DB verzameling.

Data Factory integreert met de [bibliotheek van de Azure Cosmos DB bulk](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started) -uitvoerder voor de beste prestaties wanneer u naar Azure Cosmos DB schrijft.

> [!TIP]
> De [video over gegevens migratie](https://youtu.be/5-SRNiC_qOU) leidt u door de stappen voor het kopiëren van gegevens uit Azure Blob-opslag naar Azure Cosmos db. In de video worden ook de overwegingen voor prestaties afstemmen beschreven voor het opnemen van gegevens aan Azure Cosmos DB in het algemeen.

## <a name="get-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

De volgende secties bevatten informatie over eigenschappen die u kunt gebruiken voor het definiëren van Data Factory entiteiten die specifiek zijn voor Azure Cosmos DB (SQL API).

## <a name="linked-service-properties"></a>Eigenschappen van gekoppelde service

De volgende eigenschappen worden ondersteund voor de gekoppelde service Azure Cosmos DB (SQL API):

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap **type** moet worden ingesteld op **CosmosDb**. | Ja |
| connectionString |Geef de gegevens op die nodig zijn om verbinding te maken met de Azure Cosmos DB-Data Base.<br />**Opmerking**: u moet database gegevens opgeven in de Connection String, zoals wordt weer gegeven in de volgende voor beelden. <br/> U kunt ook account sleutel in Azure Key Vault plaatsen en de `accountKey` configuratie uit de Connection String halen. Raadpleeg de volgende voor beelden en [Sla referenties op in azure Key Vault](store-credentials-in-key-vault.md) artikel met meer informatie. |Yes |
| connectVia | De [Integration runtime](concepts-integration-runtime.md) die moet worden gebruikt om verbinding te maken met het gegevens archief. U kunt de Azure Integration Runtime of een zelf-hostende Integration runtime gebruiken (als uw gegevens archief zich in een particulier netwerk bevindt). Als deze eigenschap niet is opgegeven, wordt de standaard Azure Integration Runtime gebruikt. |No |

**Voorbeeld**

```json
{
    "name": "CosmosDbSQLAPILinkedService",
    "properties": {
        "type": "CosmosDb",
        "typeProperties": {
            "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Voor beeld: account sleutel opslaan in Azure Key Vault**

```json
{
    "name": "CosmosDbSQLAPILinkedService",
    "properties": {
        "type": "CosmosDb",
        "typeProperties": {
            "connectionString": "AccountEndpoint=<EndpointUrl>;Database=<Database>",
            "accountKey": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Eigenschappen van gegevensset

Zie [gegevens sets en gekoppelde services](concepts-datasets-linked-services.md)voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevens sets.

De volgende eigenschappen worden ondersteund voor de gegevensset Azure Cosmos DB (SQL API): 

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap **type** van de DataSet moet worden ingesteld op **CosmosDbSqlApiCollection**. |Yes |
| collectionName |De naam van de Azure Cosmos DB document verzameling. |Yes |

Als u een gegevensset van het type ' DocumentDbCollection ' gebruikt, wordt deze nog steeds ondersteund als-is voor achterwaartse compatibiliteit voor de activiteit Copy en lookup. dit wordt niet ondersteund voor de gegevens stroom. U wordt aangeraden het nieuwe model verder te gebruiken.

**Voorbeeld**

```json
{
    "name": "CosmosDbSQLAPIDataset",
    "properties": {
        "type": "CosmosDbSqlApiCollection",
        "linkedServiceName":{
            "referenceName": "<Azure Cosmos DB linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [],
        "typeProperties": {
            "collectionName": "<collection name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschappen van Kopieer activiteit

In deze sectie vindt u een lijst met eigenschappen die door de Azure Cosmos DB (SQL-API) voor bron-en Sink-ondersteuning worden ondersteund. Zie [pijp lijnen](concepts-pipelines-activities.md)voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten.

### <a name="azure-cosmos-db-sql-api-as-source"></a>Azure Cosmos DB (SQL-API) als bron

Als u gegevens wilt kopiëren van Azure Cosmos DB (SQL-API), stelt u het **bron** type in de Kopieer activiteit in op **DocumentDbCollectionSource**. 

De volgende eigenschappen worden ondersteund in de sectie **bron** van de Kopieer activiteit:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap **type** van de bron van de Kopieer activiteit moet zijn ingesteld op **CosmosDbSqlApiSource**. |Yes |
| query |Geef de Azure Cosmos DB query op om gegevens te lezen.<br/><br/>Voorbeeld:<br /> `SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |No <br/><br/>Als deze SQL-instructie niet wordt opgegeven, wordt deze uitgevoerd: `select <columns defined in structure> from mycollection` |
| preferredRegions | De voorkeurs lijst met regio's waarmee verbinding moet worden gemaakt bij het ophalen van gegevens uit Cosmos DB. | No |
| pageSize | Het aantal documenten per pagina van het query resultaat. De standaard waarde is '-1 ', wat betekent dat de dynamische pagina grootte aan de kant van de service wordt gebruikt tot 1000. | No |

Als u een bron van het type ' DocumentDbCollectionSource ' gebruikt, wordt deze nog steeds ondersteund voor compatibiliteit met eerdere versies. U wordt aangeraden het nieuwe model te gebruiken dat naar uitgebreidere mogelijkheden biedt om gegevens van Cosmos DB te kopiëren.

**Voorbeeld**

```json
"activities":[
    {
        "name": "CopyFromCosmosDBSQLAPI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Cosmos DB SQL API input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "CosmosDbSqlApiSource",
                "query": "SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"",
                "preferredRegions": [
                    "East US"
                ]
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Als u gegevens uit Cosmos DB kopieert, tenzij u [JSON-documenten wilt exporteren](#import-and-export-json-documents), moet u in de best practice de toewijzing opgeven in de Kopieer activiteit. Data Factory voldoet aan de toewijzing die u hebt opgegeven voor de activiteit: als een rij geen waarde voor een kolom bevat, wordt er een null-waarde opgegeven voor de kolom waarde. Als u geen toewijzing opgeeft, wordt door Data Factory het schema afleiden door de eerste rij in de gegevens te gebruiken. Als de eerste rij niet het volledige schema bevat, ontbreken sommige kolommen in het resultaat van de activiteit bewerking.

### <a name="azure-cosmos-db-sql-api-as-sink"></a>Azure Cosmos DB (SQL-API) als Sink

Als u gegevens wilt kopiëren naar Azure Cosmos DB (SQL-API), stelt u het **sink** -type in de Kopieer activiteit in op **DocumentDbCollectionSink**. 

De volgende eigenschappen worden ondersteund in het gedeelte **sink** van de Kopieer activiteit:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap **type** van de Sink voor kopieer activiteiten moet worden ingesteld op **CosmosDbSqlApiSink**. |Yes |
| writeBehavior |Hierin wordt beschreven hoe u gegevens naar Azure Cosmos DB schrijft. Toegestane waarden: **Insert** en **upsert**.<br/><br/>Het gedrag van **upsert** is om het document te vervangen als er al een document met dezelfde id bestaat. Als dat niet het geval is, voegt u het document in.<br /><br />**Opmerking**: de Data Factory genereert automatisch een id voor een document als er geen id is opgegeven in het oorspronkelijke document of op kolom toewijzing. Dit betekent dat u ervoor moet zorgen dat uw document een ID heeft om **upsert** te laten werken zoals verwacht. |No<br />(de standaard waarde is **Invoegen**) |
| writeBatchSize | Data Factory maakt gebruik van de [Azure Cosmos DB bulk](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started) -uitvoerder bibliotheek om gegevens naar Azure Cosmos DB te schrijven. De eigenschap **writeBatchSize** bepaalt de grootte van documenten die door ADF aan de bibliotheek worden verstrekt. U kunt proberen de waarde voor **writeBatchSize** te verhogen om de prestaties te verbeteren en de waarde te verlagen als uw document groot is. Zie de onderstaande tips voor meer informatie. |No<br />(de standaard waarde is **10.000**) |
| disableMetricsCollection | Data Factory verzamelt metrische gegevens zoals Cosmos DB RUs voor het optimaliseren van Kopieer prestaties en aanbevelingen. Als u zich zorgen maakt over dit gedrag, geeft u `true` op dat u deze functie wilt uitschakelen. | Nee (standaard instelling `false` ) |

>[!TIP]
>Als u JSON-documenten wilt importeren als-is, raadpleegt u de sectie [JSON-documenten importeren of exporteren](#import-and-export-json-documents) . Als u wilt kopiëren van gegevens in tabel vorm, raadpleegt u [migreren van relationele data base naar Cosmos DB](#migrate-from-relational-database-to-cosmos-db).

>[!TIP]
>Cosmos DB beperkt de grootte van één aanvraag tot 2 MB. De formule is aanvraag grootte = één document grootte * Batch grootte schrijven. Als u een fout bericht krijgt met de melding dat de grootte van de **aanvraag te groot is**, **vermindert u de `writeBatchSize` waarde** in Sink-configuratie kopiëren.

Als u een bron van het type ' DocumentDbCollectionSink ' gebruikt, wordt deze nog steeds ondersteund voor compatibiliteit met eerdere versies. U wordt aangeraden het nieuwe model te gebruiken dat naar uitgebreidere mogelijkheden biedt om gegevens van Cosmos DB te kopiëren.

**Voorbeeld**

```json
"activities":[
    {
        "name": "CopyToCosmosDBSQLAPI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Document DB output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "CosmosDbSqlApiSink",
                "writeBehavior": "upsert"
            }
        }
    }
]
```

### <a name="schema-mapping"></a>Schema toewijzing

Als u gegevens wilt kopiëren van Azure Cosmos DB naar tabellaire sink of omgekeerd, raadpleegt u [schema toewijzing](copy-activity-schema-and-type-mapping.md#schema-mapping).

## <a name="mapping-data-flow-properties"></a>Eigenschappen van gegevens stroom toewijzen

Wanneer gegevens worden getransformeerd in de toewijzing van gegevens stromen, kunt u lezen en schrijven naar verzamelingen in Cosmos DB. Zie voor meer informatie de [bron transformatie](data-flow-source.md) en [sink-trans](data-flow-sink.md) formatie in gegevens stromen toewijzen.

### <a name="source-transformation"></a>Bron transformatie

Instellingen die specifiek zijn voor Azure Cosmos DB, zijn beschikbaar op het tabblad **bron opties** van de bron transformatie. 

**Systeem kolommen insluiten:** Als waar, ```id``` ,, ```_ts``` en andere systeem kolommen worden opgenomen in de meta gegevens van de gegevensstroom vanuit CosmosDB. Wanneer u verzamelingen bijwerkt, is het belang rijk om dit op te halen, zodat u de bestaande rij-id kunt ophalen.

**Pagina formaat:** Het aantal documenten per pagina van het query resultaat. De standaard waarde is '-1 ' die gebruikmaakt van de dynamische pagina van de service tot 1000.

**Door Voer:** Stel een optionele waarde in voor het aantal RUs dat u wilt Toep assen op uw CosmosDB-verzameling voor elke uitvoering van deze gegevens stroom tijdens de Lees bewerking. Minimum is 400.

**Voorkeurs regio's:** Kies de gewenste Lees regio's voor dit proces.

#### <a name="json-settings"></a>JSON-instellingen

**Eén document:** Selecteer deze optie als ADF het hele bestand moet behandelen als één JSON-document.

Niet- **geciteerde kolom namen:** Selecteer deze optie als kolom namen in de JSON niet in een aanhalings teken staan.

**Bevat opmerkingen:** Gebruik deze selectie als uw JSON-documenten opmerkingen hebben in de gegevens.

**Enkel aanhalings teken:** Dit moet worden geselecteerd als de kolommen en waarden in uw document worden genoteerd met enkele aanhalings tekens.

**Back slash-Escape:** Als u backslashes gebruikt om tekens in uw JSON te escapen, kiest u deze optie.

### <a name="sink-transformation"></a>Sink-trans formatie

Instellingen die specifiek zijn voor Azure Cosmos DB, zijn beschikbaar op het tabblad **instellingen** van de Sink-trans formatie.

**Update methode:** Hiermee wordt bepaald welke bewerkingen zijn toegestaan voor uw database bestemming. De standaard instelling is alleen invoegen toestaan. Als u rijen wilt bijwerken, upsert of verwijderen, moet u een alter-Row trans formatie voor deze acties labelen. Voor updates, upsert en verwijderen moet een sleutel kolom of-kolommen worden ingesteld om te bepalen welke rij moet worden gewijzigd.

**Verzamelings actie:** Hiermee wordt bepaald of de doel verzameling opnieuw moet worden gemaakt voordat deze wordt geschreven.
* Geen: er wordt geen actie uitgevoerd voor de verzameling.
* Opnieuw maken: de verzameling wordt verwijderd en opnieuw gemaakt

**Batch grootte**: bepaalt hoeveel rijen er worden geschreven in elke Bucket. Grotere batch grootten verbeteren de compressie en Optima Lise ring van het geheugen, maar er zijn geen uitzonde ringen in het geheugen bij het opslaan van gegevens.

**Partitie sleutel:** Voer een teken reeks in die de partitie sleutel voor uw verzameling vertegenwoordigt. Voorbeeld: ```/movies/title```

**Door Voer:** Stel een optionele waarde in voor het aantal RUs dat u wilt Toep assen op uw CosmosDB-verzameling voor elke uitvoering van deze gegevens stroom. Minimum is 400.

**Budget voor schrijf doorvoer:** Een geheel getal dat het aantal RUs vertegenwoordigt dat u wilt toewijzen aan de Spark-taak voor bulk opname. Dit aantal ligt buiten de totale door Voer die is toegewezen aan de verzameling.

## <a name="lookup-activity-properties"></a>Eigenschappen van opzoek activiteit

Controleer de [opzoek activiteit](control-flow-lookup-activity.md)voor meer informatie over de eigenschappen.

## <a name="import-and-export-json-documents"></a>JSON-documenten importeren en exporteren

U kunt deze Azure Cosmos DB-connector (SQL API) eenvoudig gebruiken om het volgende te doen:

* Kopieer documenten tussen twee Azure Cosmos DB verzamelingen.
* Importeer JSON-documenten uit verschillende bronnen naar Azure Cosmos DB, met inbegrip van Azure Blob-opslag, Azure Data Lake Store en andere op bestanden gebaseerde archieven die door Azure Data Factory worden ondersteund.
* JSON-documenten exporteren vanuit een Azure Cosmos DB-verzameling naar verschillende op bestanden gebaseerde archieven.

Schema-neutraal kopiëren:

* Wanneer u het hulp programma Gegevens kopiëren gebruikt, selecteert u de optie **exporteren naar json-bestanden of Cosmos DB verzameling** .
* Wanneer u de functie voor het ontwerpen van activiteiten gebruikt, kiest u JSON-indeling met de bijbehorende bestands opslag voor de bron of sink.

## <a name="migrate-from-relational-database-to-cosmos-db"></a>Migreren van relationele data base naar Cosmos DB

Bij het migreren van een relationele data base, zoals SQL Server naar Azure Cosmos DB, kan met de Kopieer activiteit gegevens in tabel vorm eenvoudig worden toegewezen aan het samen voegen van JSON-documenten in Cosmos DB. In sommige gevallen wilt u het gegevens model wellicht opnieuw ontwerpen om het te optimaliseren voor de NoSQL-cases op basis van [gegevens modellering in azure Cosmos DB](../cosmos-db/modeling-data.md), bijvoorbeeld om de gegevens te denormaliseren door alle gerelateerde subitems in één JSON-document in te sluiten. Raadpleeg [dit artikel](../cosmos-db/migrate-relational-to-cosmos-db-sql-api.md) voor een overzicht van hoe u het kunt bedenken met behulp van Azure Data Factory Kopieer activiteit.

## <a name="next-steps"></a>Volgende stappen

Zie [ondersteunde gegevens archieven](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met gegevens archieven die door de Kopieer activiteit worden ondersteund als bronnen en sinks in azure Data Factory.