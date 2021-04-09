---
title: Gegevens verplaatsen vanuit MySQL met Azure Data Factory
description: Meer informatie over het verplaatsen van gegevens uit de MySQL-data base met behulp van Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 06/06/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 83c39435d2249981a45798ffe0717054fa7b0717
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100387322"
---
# <a name="move-data-from-mysql-using-azure-data-factory"></a>Gegevens verplaatsen vanuit MySQL met Azure Data Factory
> [!div class="op_single_selector" title1="Selecteer de versie van de Data Factory-service die u gebruikt:"]
> * [Versie 1:](data-factory-onprem-mysql-connector.md)
> * [Versie 2 (huidige versie)](../connector-mysql.md)

> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. Als u de huidige versie van de Data Factory-service gebruikt, raadpleegt u [mysql-connector in v2](../connector-mysql.md).


In dit artikel wordt uitgelegd hoe u de Kopieer activiteit in Azure Data Factory kunt gebruiken om gegevens van een on-premises MySQL-data base te verplaatsen. Het is gebaseerd op het artikel [activiteiten voor gegevens verplaatsing](data-factory-data-movement-activities.md) , dat een algemeen overzicht geeft van de verplaatsing van gegevens met de Kopieer activiteit.

U kunt gegevens van een on-premises MySQL-gegevens archief kopiëren naar elk ondersteund Sink-gegevens archief. Zie de tabel [ondersteunde gegevens archieven](data-factory-data-movement-activities.md#supported-data-stores-and-formats) voor een lijst met gegevens archieven die worden ondersteund als sinks op basis van de Kopieer activiteit. Data Factory ondersteunt momenteel alleen het verplaatsen van gegevens van een MySQL-gegevens archief naar andere gegevens archieven, maar niet voor het verplaatsen van gegevens van andere gegevens archieven naar een MySQL-gegevens archief. 

## <a name="prerequisites"></a>Vereisten
Data Factory-service ondersteunt het maken van verbinding met on-premises MySQL-bronnen met behulp van de Data Management Gateway. Zie [gegevens verplaatsen tussen on-premises locaties en een Cloud](data-factory-move-data-between-onprem-and-cloud.md) artikel voor meer informatie over Data Management Gateway en stapsgewijze instructies voor het instellen van de gateway.

De gateway is vereist, zelfs als de MySQL-data base wordt gehost op een virtuele Azure IaaS-machine (VM). U kunt de gateway op dezelfde VM installeren als het gegevens archief of op een andere virtuele machine zolang de gateway verbinding kan maken met de data base.

> [!NOTE]
> Zie problemen [met gateway problemen oplossen](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) voor tips over het oplossen van problemen met verbinding/gateway.

## <a name="supported-versions-and-installation"></a>Ondersteunde versies en installatie
Als Data Management Gateway verbinding met de MySQL-data base wilt maken, moet u de [mysql-connector/net voor micro soft Windows](https://dev.mysql.com/downloads/connector/net/) (versie tussen 6.6.5 en 6.10.7) installeren op hetzelfde systeem als de Data Management Gateway. Dit 32 bits stuur programma is compatibel met 64-bits Data Management Gateway. MySQL-versie 5,1 en hoger wordt ondersteund.

> [!TIP]
> Als u de fout melding ' authenticatie is mislukt omdat de externe partij de transport stroom heeft gesloten. ', moet u overwegen om de MySQL-Connector/NET te upgraden naar een hogere versie.

## <a name="getting-started"></a>Aan de slag
U kunt een pijp lijn maken met een Kopieer activiteit die gegevens verplaatst van een on-premises Cassandra-gegevens opslag met behulp van verschillende hulpprogram ma's/Api's. 

- De eenvoudigste manier om een pijp lijn te maken, is met behulp van de **wizard kopiëren**. Zie [zelf studie: een pijp lijn maken met behulp van de wizard kopiëren](data-factory-copy-data-wizard-tutorial.md) voor een snelle walkthrough over het maken van een pijp lijn met behulp van de wizard gegevens kopiëren. 
- U kunt ook de volgende hulpprogram ma's gebruiken om een pijp lijn te maken: **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager sjabloon**, **.net API** en **rest API**. Zie [zelf studie Kopieer activiteit](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) voor stapsgewijze instructies voor het maken van een pijp lijn met een Kopieer activiteit. 

Ongeacht of u de hulpprogram ma's of Api's gebruikt, voert u de volgende stappen uit om een pijp lijn te maken waarmee gegevens uit een brongegevens archief naar een Sink-gegevens archief worden verplaatst:

1. Maak **gekoppelde services** om invoer-en uitvoer gegevens archieven te koppelen aan uw Data Factory.
2. Gegevens **sets** maken om invoer-en uitvoer gegevens voor de Kopieer bewerking weer te geven. 
3. Maak een **pijp lijn** met een Kopieer activiteit die een gegevensset als invoer en een gegevensset als uitvoer gebruikt. 

Wanneer u de wizard gebruikt, worden automatisch JSON-definities voor deze Data Factory entiteiten (gekoppelde services, gegevens sets en de pijp lijn) gemaakt. Wanneer u hulpprogram ma's/Api's (met uitzonde ring van .NET API) gebruikt, definieert u deze Data Factory entiteiten met behulp van de JSON-indeling.  Zie voor een voor beeld met JSON-definities voor Data Factory entiteiten die worden gebruikt voor het kopiëren van gegevens uit een on-premises MySQL-gegevens archief [JSON-voor beeld: gegevens kopiëren van MySQL naar Azure Blob](#json-example-copy-data-from-mysql-to-azure-blob) in dit artikel. 

De volgende secties bevatten informatie over de JSON-eigenschappen die worden gebruikt voor het definiëren van Data Factory entiteiten die specifiek zijn voor een MySQL-gegevens archief:

## <a name="linked-service-properties"></a>Eigenschappen van gekoppelde service
De volgende tabel bevat een beschrijving van de JSON-elementen die specifiek zijn voor de aan MySQL gekoppelde service.

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| type |De eigenschap type moet worden ingesteld op: **OnPremisesMySql** |Yes |
| server |De naam van de MySQL-server. |Yes |
| database |De naam van de MySQL-data base. |Yes |
| schema |De naam van het schema in de data base. |No |
| authenticationType |Type verificatie dat wordt gebruikt om verbinding te maken met de MySQL-data base. Mogelijke waarden zijn: `Basic` . |Yes |
| userName |Geef de gebruikers naam op om verbinding te maken met de MySQL-data base. |Yes |
| wachtwoord |Geef het wacht woord op voor het gebruikers account dat u hebt opgegeven. |Yes |
| gatewayName |De naam van de gateway die de Data Factory-service moet gebruiken om verbinding te maken met de on-premises MySQL-data base. |Yes |

## <a name="dataset-properties"></a>Eigenschappen van gegevensset
Zie het artikel [gegevens sets maken](data-factory-create-datasets.md) voor een volledige lijst met secties & eigenschappen die beschikbaar zijn voor het definiëren van gegevens sets. Secties zoals structuur, Beschik baarheid en beleid van een gegevensset-JSON zijn vergelijkbaar voor alle typen gegevens sets (Azure SQL, Azure Blob, Azure Table, enzovoort).

De sectie **typeProperties** verschilt voor elk type gegevensset en bevat informatie over de locatie van de gegevens in het gegevens archief. De sectie typeProperties voor de gegevensset van het type **RelationalTable** (die mysql-gegevensset bevat) heeft de volgende eigenschappen:

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| tableName |De naam van de tabel in de MySQL-data base-instantie waarnaar de gekoppelde service verwijst. |Nee (als de **query** van **RelationalSource** is opgegeven) |

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit
Zie het artikel [pijp lijnen maken](data-factory-create-pipelines.md) voor een volledige lijst met secties & eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. Eigenschappen, zoals naam, beschrijving, invoer-en uitvoer tabellen, zijn beschikbaar voor alle typen activiteiten.

Terwijl de eigenschappen die beschikbaar zijn in de sectie **typeProperties** van de activiteit, verschillen per activiteitstype. Voor kopieer activiteiten zijn ze afhankelijk van de typen bronnen en Sinks.

Wanneer de bron in de Kopieer activiteit van het type **RelationalSource** (inclusief MySQL) is, zijn de volgende eigenschappen beschikbaar in de sectie typeProperties:

| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| query |Gebruik de aangepaste query om gegevens te lezen. |SQL-query teken reeks. Bijvoorbeeld: Select * from MyTable. |Nee (als **TableName** van **gegevensset** is opgegeven) |


## <a name="json-example-copy-data-from-mysql-to-azure-blob"></a>JSON-voor beeld: gegevens kopiëren van MySQL naar Azure Blob
Dit voor beeld bevat een voor beeld van JSON-definities die u kunt gebruiken om een pijp lijn te maken met behulp van [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) of [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Hierin ziet u hoe u gegevens van een on-premises MySQL-data base kopieert naar een Azure-Blob Storage. Gegevens kunnen echter worden gekopieerd naar de [hier](data-factory-data-movement-activities.md#supported-data-stores-and-formats) opgegeven sinks met behulp van de Kopieer activiteit in azure Data Factory.

> [!IMPORTANT]
> Dit voor beeld bevat JSON-fragmenten. Het bevat geen stapsgewijze instructies voor het maken van de data factory. Zie [gegevens verplaatsen tussen on-premises locaties en een Cloud](data-factory-move-data-between-onprem-and-cloud.md) artikel voor stapsgewijze instructies.

Het voor beeld heeft de volgende data factory entiteiten:

1. Een gekoppelde service van het type [OnPremisesMySql](data-factory-onprem-mysql-connector.md#linked-service-properties).
2. Een gekoppelde service van het type [opslag](data-factory-azure-blob-connector.md#linked-service-properties).
3. Een invoer- [gegevensset](data-factory-create-datasets.md) van het type [RelationalTable](data-factory-onprem-mysql-connector.md#dataset-properties).
4. Een uitvoer [gegevensset](data-factory-create-datasets.md) van het type [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. Een [pijp lijn](data-factory-create-pipelines.md) met een Kopieer activiteit die gebruikmaakt van [RelationalSource](data-factory-onprem-mysql-connector.md#copy-activity-properties) en [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

In het voor beeld worden gegevens gekopieerd van een query resultaat in de MySQL-data base naar een BLOB per uur. De JSON-eigenschappen die in deze steek proeven worden gebruikt, worden beschreven in secties die volgen op de voor beelden.

De eerste stap is het instellen van de Data Management Gateway. De instructies bevinden zich in het [verplaatsen van gegevens tussen on-premises locaties en Cloud](data-factory-move-data-between-onprem-and-cloud.md) artikelen.

**Gekoppelde MySQL-service:**

```JSON
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

**Azure Storage gekoppelde service:**

```JSON
    {
      "name": "AzureStorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
      }
    }
```

**MySQL-invoer gegevensset:**

In het voor beeld wordt ervan uitgegaan dat u in MySQL een tabel ' MyTable ' hebt gemaakt en deze een kolom bevat met de naam ' timestampcolumn ' voor tijdreeks gegevens.

Als u ' Extern ' instelt, informeert de Data Factory-service dat de tabel extern is voor de data factory en wordt deze niet geproduceerd door een activiteit in de data factory.

```JSON
    {
        "name": "MySqlDataSet",
        "properties": {
            "published": false,
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

**Azure Blob-uitvoer gegevensset:**

Gegevens worden elk uur naar een nieuwe BLOB geschreven (frequentie: uur, interval: 1). Het mappad voor de BLOB wordt dynamisch geëvalueerd op basis van de begin tijd van het segment dat wordt verwerkt. Het mappad gebruikt delen van het jaar, de maand, de dag en het uur van de begin tijd.

```JSON
    {
        "name": "AzureBlobMySqlDataSet",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "folderPath": "mycontainer/mysql/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

**Pijp lijn met Kopieer activiteit:**

De pijp lijn bevat een Kopieer activiteit die is geconfigureerd voor het gebruik van de invoer-en uitvoer gegevens sets en die is gepland om elk uur te worden uitgevoerd. In de JSON-definitie van de pijp lijn is het **bron** type ingesteld op **RelationalSource** en het **sink** -type is ingesteld op **BlobSink**. Met de SQL-query die is opgegeven voor de **query** -eigenschap worden de gegevens in het afgelopen uur geselecteerd om te kopiëren.

```JSON
    {
        "name": "CopyMySqlToBlob",
        "properties": {
            "description": "pipeline for copy activity",
            "activities": [
                {
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
                    "inputs": [
                        {
                            "name": "MySqlDataSet"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "AzureBlobMySqlDataSet"
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
                    "name": "MySqlToBlob"
                }
            ],
            "start": "2014-06-01T18:00:00Z",
            "end": "2014-06-01T19:00:00Z"
        }
    }
```


### <a name="type-mapping-for-mysql"></a>Type toewijzing voor MySQL
Zoals vermeld in het artikel [activiteiten voor gegevens verplaatsing](data-factory-data-movement-activities.md) voert Kopieer activiteit automatisch type conversies uit van bron typen naar Sink-typen met de volgende twee stappen:

1. Converteren van systeem eigen bron typen naar .NET-type
2. Converteren van .NET-type naar systeem eigen Sink-type

Bij het verplaatsen van gegevens naar MySQL worden de volgende toewijzingen gebruikt van MySQL-typen naar .NET-typen.

| MySQL-database type | .NET Framework type |
| --- | --- |
| bigint niet ondertekend |Decimaal |
| bigint |Int64 |
| bit |Decimaal |
| blob |Byte [] |
| booleaans |Booleaans |
| char |Tekenreeks |
| date |Datum/tijd |
| datum/tijd |Datum/tijd |
| decimal |Decimaal |
| dubbele precisie |Dubbel |
| double |Dubbel |
| vaste |Tekenreeks |
| float |Enkelvoudig |
| geheel getal niet ondertekend |Int64 |
| int |Int32 |
| geheel getal niet ondertekend |Int64 |
| geheel getal |Int32 |
| lange varbinary |Byte [] |
| lange varchar |Tekenreeks |
| longblob |Byte [] |
| LONGTEXT |Tekenreeks |
| mediumblob |Byte [] |
| mediumint niet ondertekend |Int64 |
| mediumint |Int32 |
| mediumtext |Tekenreeks |
| numeriek |Decimaal |
| werkelijk |Dubbel |
| set |Tekenreeks |
| niet-ondertekend in smallint |Int32 |
| smallint |Int16 |
| tekst |Tekenreeks |
| tijd |TimeSpan |
| tijdstempel |Datum/tijd |
| tinyblob |Byte [] |
| tinyint niet ondertekend |Int16 |
| tinyint |Int16 |
| tinytext |Tekenreeks |
| varchar |Tekenreeks |
| jaar |Int |

## <a name="map-source-to-sink-columns"></a>Bron toewijzen aan Sink-kolommen
Zie [DataSet-kolommen toewijzen in azure Data Factory](data-factory-map-columns.md)voor meer informatie over het toewijzen van kolommen in de bron-gegevensset aan kolommen in Sink-gegevensset.

## <a name="repeatable-read-from-relational-sources"></a>Herhaal bare Lees bewerking van relationele bronnen
Houd bij het kopiëren van gegevens uit relationele gegevens archieven de Herhaal baarheid in de hand om onbedoelde resultaten te voor komen. In Azure Data Factory kunt u een segment hand matig opnieuw uitvoeren. U kunt ook beleid voor opnieuw proberen voor een gegevensset configureren zodat een segment opnieuw wordt uitgevoerd wanneer er een fout optreedt. Wanneer een segment op een van beide manieren opnieuw wordt uitgevoerd, moet u ervoor zorgen dat dezelfde gegevens worden gelezen, ongeacht het aantal keren dat een segment wordt gestart. Zie [Herhaal bare Lees bewerking van relationele bronnen](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Prestaties en afstemming
Zie [Kopieer activiteit prestaties & afstemmings handleiding](data-factory-copy-activity-performance.md) voor meer informatie over de belangrijkste factoren die invloed hebben op de prestaties van het verplaatsen van gegevens (Kopieer activiteit) in azure Data Factory en verschillende manieren om deze te optimaliseren.
