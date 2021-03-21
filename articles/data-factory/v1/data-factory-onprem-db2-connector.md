---
title: Gegevens van DB2 verplaatsen met behulp van Azure Data Factory
description: Meer informatie over het verplaatsen van gegevens uit een on-premises DB2-Data Base met behulp van Azure Data Factory Copy-activiteit
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: bcd1e6f9f9be1765e76399707ae36776a9f1987d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100387356"
---
# <a name="move-data-from-db2-by-using-azure-data-factory-copy-activity"></a>Gegevens van DB2 verplaatsen met behulp van Azure Data Factory Kopieer activiteit
> [!div class="op_single_selector" title1="Selecteer de versie van de Data Factory-service die u gebruikt:"]
> * [Versie 1:](data-factory-onprem-db2-connector.md)
> * [Versie 2 (huidige versie)](../connector-db2.md)

> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. Als u de huidige versie van de Data Factory-service gebruikt, raadpleegt u de [DB2-connector in v2](../connector-db2.md).


In dit artikel wordt beschreven hoe u de Kopieer activiteit in Azure Data Factory kunt gebruiken om gegevens van een on-premises DB2-Data Base naar een gegevens archief te kopiëren. U kunt gegevens naar een Store kopiëren die wordt vermeld als een ondersteunde sink in het artikel [Data Factory activiteiten voor gegevens verplaatsing](data-factory-data-movement-activities.md#supported-data-stores-and-formats) . In dit onderwerp vindt u informatie over het Data Factory-artikel, waarin een overzicht wordt gegeven van de verplaatsing van gegevens met behulp van Kopieer activiteit en een lijst met de ondersteunde combi Naties van gegevens archieven. 

Data Factory ondersteunt momenteel alleen het verplaatsen van gegevens van een DB2-Data Base naar een [ondersteund Sink-gegevens archief](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Het verplaatsen van gegevens van andere gegevens archieven naar een DB2-Data Base wordt niet ondersteund.

## <a name="prerequisites"></a>Vereisten
Data Factory ondersteunt het verbinding maken met een on-premises DB2-Data Base met behulp van de [Data Management Gateway](data-factory-data-management-gateway.md). Zie het artikel [gegevens verplaatsen van on-premises naar de Cloud](data-factory-move-data-between-onprem-and-cloud.md) voor stapsgewijze instructies voor het instellen van de gateway gegevens pijplijn om uw gegevens te verplaatsen.

Een gateway is vereist, zelfs als de DB2 wordt gehost op Azure IaaS VM. U kunt de gateway op dezelfde IaaS-VM installeren als het gegevens archief. Als de gateway verbinding kan maken met de data base, kunt u de gateway installeren op een andere virtuele machine.

De Data Management Gateway biedt een ingebouwd DB2-stuur programma, zodat u niet hand matig een stuur programma hoeft te installeren om gegevens van DB2 te kopiëren.

> [!NOTE]
> Zie het artikel problemen [met de gateway oplossen](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) voor tips voor het oplossen van problemen met verbindingen en gateways.


## <a name="supported-versions"></a>Ondersteunde versies
De Data Factory DB2-connector ondersteunt de volgende IBM DB2-platforms en-versies met distributed relation data base Architecture (DRDA) SQL Access Manager-versie 9, 10 en 11:

* IBM DB2 voor z/OS-versie 11,1
* IBM DB2 voor z/OS-versie 10,1
* IBM DB2 for i (AS400) versie 7,2
* IBM DB2 for i (AS400) versie 7,1
* IBM DB2 voor Linux, UNIX en Windows (LUW) versie 11
* IBM DB2 voor LUW-versie 10,5
* IBM DB2 voor LUW-versie 10,1

> [!TIP]
> Als u het fout bericht ' kan het pakket dat overeenkomt met een SQL-instructie, is niet gevonden. SQLSTATE = 51002 SQLCODE =-805, ' de reden is dat er geen vereist pakket is gemaakt voor de normale gebruiker van het besturings systeem. Volg deze instructies voor het type DB2-Server om dit probleem op te lossen:
> - DB2 for i (AS400): laat een hoofd gebruiker de verzameling voor de normale gebruiker maken voordat de Kopieer activiteit wordt uitgevoerd. Als u de verzameling wilt maken, gebruikt u de opdracht: `create collection <username>`
> - DB2 voor z/O'S of LUW: gebruik een account met hoge bevoegdheden--een hoofd gebruiker of beheerder met pakket instanties en binding, BINDADD, toestemming geven voor open bare machtigingen--om de kopie één keer uit te voeren. Het benodigde pakket wordt automatisch gemaakt tijdens de kopie. Daarna kunt u teruggaan naar de normale gebruiker voor de volgende Kopieer uitvoeringen.

## <a name="getting-started"></a>Aan de slag
U kunt een pijp lijn maken met een Kopieer activiteit om gegevens te verplaatsen van een on-premises DB2-gegevens opslag met behulp van verschillende hulpprogram ma's en Api's: 

- De eenvoudigste manier om een pijp lijn te maken, is met behulp van de wizard kopiëren Azure Data Factory. Zie de [zelf studie: een pijp lijn maken met behulp van de wizard kopiëren](data-factory-copy-data-wizard-tutorial.md)voor een snelle walkthrough over het maken van een pijp lijn met behulp van de wizard kopiëren. 
- U kunt ook hulpprogram ma's gebruiken om een pijp lijn te maken, zoals Visual Studio, Azure PowerShell, een Azure Resource Manager sjabloon, de .NET API en de REST API. Zie de [zelf studie Kopieer activiteit](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)voor stapsgewijze instructies voor het maken van een pijp lijn met een Kopieer activiteit. 

Ongeacht of u de hulpprogram ma's of Api's gebruikt, voert u de volgende stappen uit om een pijp lijn te maken waarmee gegevens uit een brongegevens archief naar een Sink-gegevens archief worden verplaatst:

1. Maak gekoppelde services om invoer-en uitvoer gegevens archieven te koppelen aan uw data factory.
2. Gegevens sets maken om invoer-en uitvoer gegevens voor de Kopieer bewerking weer te geven. 
3. Maak een pijp lijn met een Kopieer activiteit die een gegevensset als invoer en een gegevensset als uitvoer gebruikt. 

Wanneer u de wizard kopiëren gebruikt, worden de JSON-definities voor de Data Factory gekoppelde services, gegevens sets en pijplijn entiteiten automatisch voor u gemaakt. Wanneer u hulpprogram ma's of Api's gebruikt (met uitzonde ring van de .NET API), definieert u de Data Factory entiteiten met behulp van de JSON-indeling. Het JSON-voor beeld: gegevens kopiëren van DB2 naar Azure Blob-opslag toont de JSON-definities voor de Data Factory entiteiten die worden gebruikt voor het kopiëren van gegevens uit een on-premises DB2-gegevens opslag.

De volgende secties bevatten informatie over de JSON-eigenschappen die worden gebruikt voor het definiëren van de Data Factory entiteiten die specifiek zijn voor een DB2-gegevens archief.

## <a name="db2-linked-service-properties"></a>Eigenschappen van gekoppelde service met DB2
De volgende tabel geeft een lijst van de JSON-eigenschappen die specifiek zijn voor een gekoppelde DB2-service.

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| **type** |Deze eigenschap moet worden ingesteld op **OnPremisesDb2**. |Ja |
| **naam** |De naam van de DB2-Server. |Ja |
| **enddatabase** |De naam van de DB2-Data Base. |Ja |
| **schema** |De naam van het schema in de DB2-Data Base. Deze eigenschap is hoofdletter gevoelig. |Nee |
| **authenticationType** |Het type verificatie dat wordt gebruikt om verbinding te maken met de DB2-Data Base. De mogelijke waarden zijn: anoniem, basis en Windows. |Ja |
| **gebruikers** |De naam voor het gebruikers account als u basis-of Windows-verificatie gebruikt. |Nee |
| **password** |Het wacht woord voor het gebruikers account. |Nee |
| **gatewayName** |De naam van de gateway die de Data Factory-service moet gebruiken om verbinding te maken met de on-premises DB2-Data Base. |Ja |

## <a name="dataset-properties"></a>Eigenschappen van gegevensset
Zie het artikel [gegevens sets maken](data-factory-create-datasets.md) voor een lijst met de secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevens sets. Secties, zoals **structuur**, **Beschik baarheid** en het **beleid** voor een gegevensset-JSON, zijn vergelijkbaar voor alle typen gegevens sets (Azure SQL, Azure Blob Storage, Azure-tabel opslag, enzovoort).

De sectie **typeProperties** verschilt voor elk type gegevensset en bevat informatie over de locatie van de gegevens in het gegevens archief. De sectie **typeProperties** voor een gegevensset van het type **RelationalTable**, die de DB2-gegevensset bevat, heeft de volgende eigenschap:

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| **tableName** |De naam van de tabel in het exemplaar van de DB2-Data Base waarnaar de gekoppelde service verwijst. Deze eigenschap is hoofdletter gevoelig. |Nee (als de eigenschap **query** van een Kopieer activiteit van het type **RelationalSource** is opgegeven) |

## <a name="copy-activity-properties"></a>Eigenschappen van Kopieer activiteit
Zie het artikel [pijp lijnen maken](data-factory-create-pipelines.md) voor een lijst met de secties en eigenschappen die beschikbaar zijn voor het definiëren van Kopieer activiteiten. Eigenschappen van de Kopieer activiteit, zoals **naam**, **Beschrijving**, **invoer** tabel, **uitvoer** tabel en **beleid**, zijn beschikbaar voor alle typen activiteiten. De eigenschappen die beschikbaar zijn in de sectie **typeProperties** van de activiteit variëren per type activiteit. Voor kopieer activiteiten zijn de eigenschappen afhankelijk van de typen gegevens bronnen en Sinks.

Als de bron van het type **RelationalSource** (inclusief DB2) is, zijn de volgende eigenschappen beschikbaar in de sectie **TypeProperties** van Kopieer activiteit:

| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| **ophalen** |Gebruik de aangepaste query om de gegevens te lezen. |SQL-query teken reeks. Bijvoorbeeld: `"query": "select * from "MySchema"."MyTable""` |Nee (als de eigenschap **TableName** van een gegevensset is opgegeven) |

> [!NOTE]
> Schema-en tabel namen zijn hoofdletter gevoelig. Plaats in de query-instructie eigenschaps namen met behulp van "" (dubbele aanhalings tekens).

## <a name="json-example-copy-data-from-db2-to-azure-blob-storage"></a>JSON-voor beeld: gegevens kopiëren van DB2 naar Azure Blob-opslag
In dit voor beeld worden voor beelden van JSON-definities gegeven die u kunt gebruiken om een pijp lijn te maken met behulp van [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)of [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). In het voor beeld ziet u hoe u gegevens kopieert van een DB2-Data Base naar Blob Storage. Gegevens kunnen echter worden gekopieerd naar het [sink-type van een ondersteund gegevens archief](data-factory-data-movement-activities.md#supported-data-stores-and-formats) met behulp van Azure Data Factory Kopieer activiteit.

Het voor beeld heeft de volgende Data Factory entiteiten:

- Een gekoppelde DB2-service van het type [OnPremisesDb2](data-factory-onprem-db2-connector.md)
- Een gekoppelde Azure Blob Storage-service van het type [opslag](data-factory-azure-blob-connector.md#linked-service-properties)
- Een invoer- [gegevensset](data-factory-create-datasets.md) van het type [RelationalTable](data-factory-onprem-db2-connector.md#dataset-properties)
- Een uitvoer [gegevensset](data-factory-create-datasets.md) van het type [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)
- Een [pijp lijn](data-factory-create-pipelines.md) met een Kopieer activiteit die gebruikmaakt van de eigenschappen [RelationalSource](data-factory-onprem-db2-connector.md#copy-activity-properties) en [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)

In het voor beeld worden gegevens gekopieerd van een query resultaat in een DB2-Data Base naar een Azure-Blob per uur. De JSON-eigenschappen die in het voor beeld worden gebruikt, worden beschreven in de secties die volgen op de entiteits definities.

Als eerste stap installeert en configureert u een gegevens gateway. Instructies bevinden zich in het [verplaatsen van gegevens tussen on-premises locaties en Cloud](data-factory-move-data-between-onprem-and-cloud.md) artikelen.

**Gekoppelde DB2-service**

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

**Gekoppelde Azure Blob Storage-service**

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorageLinkedService",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"
        }
    }
}
```

**DB2-invoer gegevensset**

In het voor beeld wordt ervan uitgegaan dat u een tabel in DB2 met de naam ' MyTable ' hebt gemaakt met een kolom met het label ' tijds tempel ' voor de tijdreeks gegevens.

De eigenschap **External** wordt ingesteld op ' True '. Met deze instelling wordt de Data Factory service informeert dat deze gegevensset extern is voor de data factory en niet wordt geproduceerd door een activiteit in de data factory. U ziet dat de eigenschap **type** is ingesteld op **RelationalTable**.


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

**Azure Blob-uitvoergegevensset**

Gegevens worden elk uur naar een nieuwe BLOB geschreven door de eigenschap **Frequency** in te stellen op ' hour ' en de eigenschap **interval** op 1. De eigenschap **FolderPath** voor de BLOB wordt dynamisch geëvalueerd op basis van de begin tijd van het segment dat wordt verwerkt. Het mappad maakt gebruik van het jaar, de maand, de dag en het uur van de begin tijd.

```json
{
    "name": "AzureBlobDb2DataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/db2/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            },
            "partitionedBy": [
                {
                    "name": "Year",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "yyyy"
                    }
                },
                {
                    "name": "Month",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "MM"
                    }
                },
                {
                    "name": "Day",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "dd"
                    }
                },
                {
                    "name": "Hour",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "HH"
                    }
                }
            ]
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Pijp lijn voor de Kopieer activiteit**

De pijp lijn bevat een Kopieer activiteit die is geconfigureerd voor het gebruik van opgegeven invoer-en uitvoer gegevens sets en die is ingepland om elk uur te worden uitgevoerd. In de JSON-definitie voor de pijp lijn is het **bron** type ingesteld op **RelationalSource** en het **sink** -type is ingesteld op **BlobSink**. De SQL-query die is opgegeven voor de eigenschap **query** selecteert de gegevens uit de tabel Orders.

```json
{
    "name": "CopyDb2ToBlob",
    "properties": {
        "description": "pipeline for the copy activity",
        "activities": [
            {
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
                "inputs": [
                    {
                        "name": "Db2DataSet"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobDb2DataSet"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "Db2ToBlob"
            }
        ],
        "start": "2014-06-01T18:00:00Z",
        "end": "2014-06-01T19:00:00Z"
    }
}
```

## <a name="type-mapping-for-db2"></a>Type toewijzing voor DB2
Zoals vermeld in het artikel [activiteiten voor gegevens verplaatsing](data-factory-data-movement-activities.md) voert Kopieer activiteit automatisch type conversies uit van bron type naar Sink-type met behulp van de volgende twee stappen:

1. Converteren van een systeem eigen bron type naar een .NET-type
2. Converteren van een .NET-type naar een systeem eigen Sink-type

De volgende toewijzingen worden gebruikt wanneer Kopieer activiteit de gegevens van een DB2-type converteert naar een .NET-type:

| DB2-database type | .NET Framework type |
| --- | --- |
| SmallInt |Int16 |
| Geheel getal |Int32 |
| BigInt |Int64 |
| Realistische |Enkelvoudig |
| Dubbel |Dubbel |
| Float |Dubbel |
| Decimaal |Decimaal |
| DecimalFloat |Decimaal |
| Numeriek |Decimaal |
| Date |DateTime |
| Tijd |TimeSpan |
| Tijdstempel |DateTime |
| Xml |Byte [] |
| Char |Tekenreeks |
| VarChar |Tekenreeks |
| LongVarChar |Tekenreeks |
| DB2DynArray |Tekenreeks |
| Binair |Byte [] |
| VarBinary |Byte [] |
| LongVarBinary |Byte [] |
| Afbeelding |Tekenreeks |
| VarGraphic |Tekenreeks |
| LongVarGraphic |Tekenreeks |
| CLOB |Tekenreeks |
| Blob |Byte [] |
| DbClob |Tekenreeks |
| SmallInt |Int16 |
| Geheel getal |Int32 |
| BigInt |Int64 |
| Realistische |Enkelvoudig |
| Dubbel |Dubbel |
| Float |Dubbel |
| Decimaal |Decimaal |
| DecimalFloat |Decimaal |
| Numeriek |Decimaal |
| Date |DateTime |
| Tijd |TimeSpan |
| Tijdstempel |DateTime |
| Xml |Byte [] |
| Char |Tekenreeks |

## <a name="map-source-to-sink-columns"></a>Bron toewijzen aan Sink-kolommen
Zie [DataSet-kolommen toewijzen in azure Data Factory](data-factory-map-columns.md)voor meer informatie over het toewijzen van kolommen in de bron-gegevensset aan kolommen in de Sink-gegevensset.

## <a name="repeatable-reads-from-relational-sources"></a>Herhaal bare Lees bewerkingen van relationele bronnen
Wanneer u gegevens uit een relationele gegevens opslag kopieert, moet u zich herhalen om onbedoelde resultaten te voor komen. In Azure Data Factory kunt u een segment hand matig opnieuw uitvoeren. U kunt ook de eigenschap **beleid** voor opnieuw proberen configureren voor een gegevensset om een segment opnieuw uit te voeren als er een fout optreedt. Zorg ervoor dat dezelfde gegevens worden gelezen, ongeacht het aantal keren dat het segment opnieuw wordt uitgevoerd en onafhankelijk van hoe u het segment opnieuw uitvoert. Zie [Herhaal bare Lees bewerkingen van relationele bronnen](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources)voor meer informatie.

## <a name="performance-and-tuning"></a>Prestaties en afstemmen
Meer informatie over de belangrijkste factoren die van invloed zijn op de prestaties van de Kopieer activiteit en op manieren om de prestaties te optimaliseren in de hand leiding voor het kopiëren van de [Kopieer activiteit](data-factory-copy-activity-performance.md).
