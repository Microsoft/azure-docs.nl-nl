---
title: Gegevens kopiëren van of naar Oracle met behulp van Data Factory
description: Meer informatie over het kopiëren van gegevens naar of van een on-premises Oracle-data base met behulp van Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 05/15/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 02fc142a08176aa577250417c0e394218e832f34
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100387339"
---
# <a name="copy-data-to-or-from-oracle-on-premises-by-using-azure-data-factory"></a>Gegevens naar of van Oracle on-premises kopiëren met behulp van Azure Data Factory

> [!div class="op_single_selector" title1="Selecteer de versie van de Data Factory-service die u gebruikt:"]
> * [Versie 1:](data-factory-onprem-oracle-connector.md)
> * [Versie 2 (huidige versie)](../connector-oracle.md)

> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Azure Data Factory. Als u de huidige versie van de Azure Data Factory-service gebruikt, raadpleegt u [Oracle Connector in v2](../connector-oracle.md).


In dit artikel wordt uitgelegd hoe u de Kopieer activiteit in Azure Data Factory kunt gebruiken om gegevens naar of van een on-premises Oracle-Data Base te verplaatsen. Het artikel bouwt voort op [activiteiten voor gegevens verplaatsing](data-factory-data-movement-activities.md), waarmee een algemeen overzicht wordt gegeven van de verplaatsing van gegevens met behulp van de Kopieer activiteit.

## <a name="supported-scenarios"></a>Ondersteunde scenario's

U kunt gegevens *uit een Oracle-data base* kopiëren naar de volgende gegevens archieven:

[!INCLUDE [data-factory-supported-sink](../../../includes/data-factory-supported-sinks.md)]

U kunt gegevens uit de volgende gegevens archieven kopiëren *naar een Oracle-data base*:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

## <a name="prerequisites"></a>Vereisten

Data Factory ondersteunt het maken van verbinding met on-premises Oracle-bronnen met behulp van Data Management Gateway. Zie [Data Management Gateway](data-factory-data-management-gateway.md) voor meer informatie over Data Management Gateway. Zie [gegevens verplaatsen van on-premises naar de Cloud](data-factory-move-data-between-onprem-and-cloud.md)voor stapsgewijze instructies voor het instellen van de gateway in een gegevens pijplijn om gegevens te verplaatsen.

De gateway is vereist, zelfs als de Oracle wordt gehost in een Azure Infrastructure as a Service (IaaS)-VM. U kunt de gateway op dezelfde IaaS-VM installeren als het gegevens archief of op een andere virtuele machine, zolang de gateway verbinding kan maken met de data base.

> [!NOTE]
> Zie problemen [met Gateway oplossen](data-factory-data-management-gateway.md#troubleshooting-gateway-issues)voor tips over het oplossen van problemen met de verbinding en de gateway.

## <a name="supported-versions-and-installation"></a>Ondersteunde versies en installatie

Deze Oracle-connector ondersteunt twee versies van Stuur Programma's:

- **Micro soft-stuur programma voor Oracle (aanbevolen)**: vanaf Data Management Gateway versie 2,7 wordt automatisch een micro soft-stuur programma voor Oracle geïnstalleerd met de gateway. U hoeft het stuur programma niet te installeren of bij te werken om verbinding te maken met Oracle. U kunt ook betere Kopieer prestaties krijgen met behulp van dit stuur programma. Deze versies van Oracle-data bases worden ondersteund:
  - Oracle 12c R1 (12,1)
  - Oracle 11g R1, R2 (11,1, 11,2)
  - Oracle 10g R1, R2 (10,1, 10,2)
  - Oracle 9i R1, R2 (9.0.1, 9,2)
  - Oracle 8i R3 (8.1.7)

    > [!NOTE]
    > Oracle-proxy server wordt niet ondersteund.

    > [!IMPORTANT]
    > Het micro soft-stuur programma voor Oracle ondersteunt momenteel alleen het kopiëren van gegevens van Oracle. Het stuur programma biedt geen ondersteuning voor het schrijven naar Oracle. De functionaliteit voor het testen van de verbinding op het tabblad Data Management Gateway **Diagnostische gegevens** biedt geen ondersteuning voor dit stuur programma. U kunt ook de wizard kopiëren gebruiken om de connectiviteit te valideren.
    >

- **Oracle-gegevens provider voor .net**: u kunt Oracle data provider gebruiken om gegevens van of naar Oracle te kopiëren. Dit onderdeel is opgenomen in [Oracle Data Access Components voor Windows](https://www.oracle.com/technetwork/topics/dotnet/downloads/). Installeer de relevante versie (32-bits of 64-bits) op de computer waarop de gateway is geïnstalleerd. [Oracle data provider .net 12,1](https://docs.oracle.com/database/121/ODPNT/InstallSystemRequirements.htm#ODPNT149) heeft toegang tot Oracle database 10g versie 2 en hoger.

    Als u **xcopy-installatie** selecteert, voert u de stappen uit die worden beschreven in het readme.htm-bestand. U kunt het beste het installatie programma selecteren dat de gebruikers interface heeft (niet het installatie programma XCopy).

    Nadat u de provider hebt geïnstalleerd, start u de Data Management Gateway host-service op uw computer opnieuw met behulp van het onderdeel Services of Data Management Gateway Configuration Manager.

Als u de Kopieer wizard gebruikt om de Kopieer pijplijn te maken, wordt het type van het stuur programma autobepaald. Het micro soft-stuur programma wordt standaard gebruikt, tenzij uw gateway versie ouder is dan versie 2,7 of als u Oracle selecteert als sink.

## <a name="get-started"></a>Aan de slag

U kunt een pijp lijn met een Kopieer activiteit maken. De pijp lijn verplaatst gegevens naar of van een on-premises Oracle-data base met behulp van verschillende hulpprogram ma's of Api's.

De eenvoudigste manier om een pijp lijn te maken, is met behulp van de wizard kopiëren. Zie [zelf studie: een pijp lijn maken met behulp van de wizard kopiëren](data-factory-copy-data-wizard-tutorial.md) voor een snelle walkthrough over het maken van een pijp lijn met behulp van de wizard gegevens kopiëren.

U kunt ook een van de volgende hulpprogram ma's gebruiken om een pijp lijn te maken: **Visual Studio**, **Azure PowerShell**, een **Azure Resource Manager sjabloon**, de **.net API** of de **rest API**. Raadpleeg de [zelf studie activiteit kopiëren](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) voor stapsgewijze instructies voor het maken van een pijp lijn met een Kopieer activiteit.

Voer de volgende stappen uit om een pijp lijn te maken waarmee gegevens uit een brongegevens archief naar een Sink-gegevens archief worden verplaatst, ongeacht of u de hulpprogram ma's of Api's gebruikt.

1. Maak een **Data Factory**. Een data factory kan een of meer pijp lijnen bevatten.
2. Maak **gekoppelde services** om invoer-en uitvoer gegevens archieven te koppelen aan uw Data Factory. Als u bijvoorbeeld gegevens uit een Oracle-Data Base naar Azure Blob-opslag kopieert, maakt u twee gekoppelde services om uw Oracle-data base en Azure Storage-account aan uw data factory te koppelen. Zie [Eigenschappen van gekoppelde service](#linked-service-properties)voor gekoppelde service-eigenschappen die specifiek zijn voor Oracle.
3. Gegevens **sets** maken om invoer-en uitvoer gegevens voor de Kopieer bewerking weer te geven. In het voor beeld in de vorige stap maakt u een gegevensset om de tabel op te geven in de Oracle-data base die de invoer gegevens bevat. U maakt een andere gegevensset om de BLOB-container en de map op te geven die de gegevens bevat die zijn gekopieerd uit de Oracle-data base. Zie [Eigenschappen](#dataset-properties)van gegevensset voor eigenschappen van gegevens sets die specifiek zijn voor Oracle.
4. Maak een **pijp lijn** met een Kopieer activiteit die een gegevensset als invoer en een gegevensset als uitvoer gebruikt. In het voor gaande voor beeld gebruikt u **OracleSource** als bron en **BlobSink** als Sink voor de Kopieer activiteit. En als u kopieert vanuit Azure Blob-opslag naar een Oracle-data base, gebruikt u **BlobSource** en **OracleSink** in de Kopieer activiteit. Zie [Eigenschappen van Kopieer activiteit](#copy-activity-properties)voor eigenschappen van de Kopieer activiteit die specifiek zijn voor een Oracle-data base. Voor meer informatie over het gebruik van een gegevens archief als een bron of sink selecteert u de koppeling voor uw gegevens opslag in de voor gaande sectie.

Wanneer u de wizard gebruikt, worden de JSON-definities voor deze Data Factory entiteiten automatisch voor u gemaakt: gekoppelde services, gegevens sets en de pijp lijn. Wanneer u hulpprogram ma's of Api's gebruikt (met uitzonde ring van de .NET API), definieert u deze Data Factory entiteiten met behulp van de JSON-indeling. Zie JSON-voor beelden voor voor beelden met JSON-definities voor Data Factory entiteiten die u gebruikt om gegevens naar of van een on-premises Oracle-Data Base te kopiëren.

De volgende secties bevatten informatie over de JSON-eigenschappen die u gebruikt om Data Factory entiteiten te definiëren.

## <a name="linked-service-properties"></a>Eigenschappen van gekoppelde service

De volgende tabel beschrijft de JSON-elementen die specifiek zijn voor de aan Oracle gekoppelde service:

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| type |De eigenschap **type** moet worden ingesteld op **OnPremisesOracle**. |Yes |
| driverType | Opgeven welk stuur programma moet worden gebruikt voor het kopiëren van gegevens van of naar een Oracle-data base. Toegestane waarden zijn **micro soft** en **ODP** (standaard). Zie de [ondersteunde versie en de installatie](#supported-versions-and-installation) voor details van Stuur Programma's. | No |
| connectionString | Geef de gegevens op die nodig zijn om verbinding te maken met het Oracle data base-exemplaar voor de **Connections Tring** -eigenschap. | Yes |
| gatewayName | De naam van de gateway die wordt gebruikt om verbinding te maken met de on-premises Oracle-server. |Yes |

**Voor beeld: het micro soft-stuur programma gebruiken**

> [!TIP]
> Als u een fout ziet met de melding "ORA-01025: UPI para meter buiten bereik" en uw Oracle versie-8i, voegt u toe `WireProtocolMode=1` aan uw Connection String en probeert u het opnieuw:

```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "driverType": "Microsoft",
            "connectionString":"Host=<host>;Port=<port>;Sid=<service ID>;User Id=<user name>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

**Voor beeld: het ODP-stuur programma gebruiken**

Voor meer informatie over toegestane indelingen raadpleegt u [Oracle data provider voor .net ODP](https://www.connectionstrings.com/oracle-data-provider-for-net-odp-net/).

```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "connectionString": "Data Source=(DESCRIPTION=(ADDRESS=(PROTOCOL=TCP)(HOST=<host name>)(PORT=<port number>))(CONNECT_DATA=(SERVICE_NAME=<service ID>))); User Id=<user name>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

## <a name="dataset-properties"></a>Eigenschappen van gegevensset

Zie [gegevens sets maken](data-factory-create-datasets.md)voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevens sets.

De secties van een JSON-bestand van een gegevensset, zoals de structuur, Beschik baarheid en het beleid, zijn vergelijkbaar voor alle typen gegevens sets (bijvoorbeeld voor Oracle, Azure Blob Storage en Azure Table Storage).

De sectie **typeProperties** verschilt voor elk type gegevensset en bevat informatie over de locatie van de gegevens in het gegevens archief. De sectie **typeProperties** voor de gegevensset van het type **OracleTable** heeft de volgende eigenschappen:

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| tableName |De naam van de tabel in de Oracle-data base waarnaar de gekoppelde service verwijst. |Nee (als **oracleReaderQuery** of **OracleSource** is opgegeven) |

## <a name="copy-activity-properties"></a>Eigenschappen van Kopieer activiteit

Zie [pijp lijnen maken](data-factory-create-pipelines.md)voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten.

Eigenschappen zoals naam, beschrijving, invoer-en uitvoer tabellen en beleid zijn beschikbaar voor alle typen activiteiten.

> [!NOTE]
> Voor de Kopieer activiteit wordt slechts één invoer uitgevoerd en er wordt slechts één uitvoer gegenereerd.

Eigenschappen die beschikbaar zijn in de sectie **typeProperties** van de activiteit, variëren per type activiteit. De eigenschappen van de Kopieer activiteit variëren afhankelijk van het type bron en Sink.

### <a name="oraclesource"></a>OracleSource

Als de bron van het type **OracleSource** in Kopieer activiteit is, zijn de volgende eigenschappen beschikbaar in de sectie **typeProperties** :

| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| oracleReaderQuery |Gebruik de aangepaste query om gegevens te lezen. |Een SQL-query teken reeks. Bijvoorbeeld ' Select \* from **myTable**'. <br/><br/>Als dit niet wordt opgegeven, wordt deze SQL-instructie uitgevoerd: ' Select \* from **myTable**' |No<br />(als **TableName** van **gegevensset** is opgegeven) |

### <a name="oraclesink"></a>OracleSink

**OracleSink** ondersteunt de volgende eigenschappen:

| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| writeBatchTimeout |De wacht tijd voor het volt ooien van de batch INSERT-bewerking voordat er een time-out optreedt. |**tijdsbestek**<br/><br/> Voor beeld: 00:30:00 (30 minuten) |No |
| writeBatchSize |Hiermee worden gegevens in de SQL-tabel ingevoegd wanneer de buffer grootte de waarde van **writeBatchSize** bereikt. |Geheel getal (aantal rijen) |Nee (standaard: 100) |
| sqlWriterCleanupScript |Hiermee geeft u een query op voor het uitvoeren van de Kopieer activiteit, zodat de gegevens van een specifiek segment worden opgeruimd. |Een query-instructie. |No |
| sliceIdentifierColumnName |Hiermee geeft u de kolom naam voor de Kopieer activiteit moet worden gevuld met een segment-id die automatisch is gegenereerd. De waarde voor **sliceIdentifierColumnName** wordt gebruikt voor het opschonen van gegevens van een specifiek segment wanneer het opnieuw wordt uitgevoerd. |De kolom naam van een kolom met het gegevens type **binary (32)**. |No |

## <a name="json-examples-for-copying-data-to-and-from-the-oracle-database"></a>JSON-voor beelden voor het kopiëren van gegevens van en naar de Oracle-data base

De volgende voor beelden bieden voor beeld van JSON-definities die u kunt gebruiken om een pijp lijn te maken met behulp van [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) of [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). De voor beelden laten zien hoe u gegevens kopieert vanuit of naar een Oracle-data base en naar of van Azure Blob-opslag. Gegevens kunnen echter worden gekopieerd naar een van de sinks in [ondersteunde gegevens archieven en-indelingen](data-factory-data-movement-activities.md#supported-data-stores-and-formats) met behulp van de Kopieer activiteit in azure Data Factory.

**Voor beeld: gegevens van Oracle naar Azure Blob-opslag kopiëren**

Het voor beeld heeft de volgende Data Factory entiteiten:

* Een gekoppelde service van het type [OnPremisesOracle](data-factory-onprem-oracle-connector.md#linked-service-properties).
* Een gekoppelde service van het type [opslag](data-factory-azure-blob-connector.md#linked-service-properties).
* Een invoer- [gegevensset](data-factory-create-datasets.md) van het type [OracleTable](data-factory-onprem-oracle-connector.md#dataset-properties).
* Een uitvoer [gegevensset](data-factory-create-datasets.md) van het type [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* Een [pijp lijn](data-factory-create-pipelines.md) met een Kopieer activiteit die gebruikmaakt van [OracleSource](data-factory-onprem-oracle-connector.md#copy-activity-properties) als bron en [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) als sink.

In het voor beeld worden gegevens uit een tabel in een on-premises Oracle-Data Base naar een BLOB per uur gekopieerd. Zie de secties die volgen op de voor beelden voor meer informatie over de verschillende eigenschappen die in het voor beeld worden gebruikt.

**Gekoppelde Oracle-Service**

```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "driverType": "Microsoft",
            "connectionString":"Host=<host>;Port=<port>;Sid=<service ID>;User Id=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

**Gekoppelde Azure Blob Storage-service**

```json
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>"
        }
    }
}
```

**Oracle-invoer gegevensset**

In het voor beeld wordt ervan uitgegaan dat u een tabel hebt gemaakt met de naam **myTable** in Oracle. Het bevat een kolom met de naam **timestampcolumn** voor time series-gegevens.

Als **extern** instellen: **True** informeert de Data Factory-service dat de gegevensset extern is voor de Data Factory en dat de gegevensset niet wordt geproduceerd door een activiteit in de Data Factory.

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
            "anchorDateTime": "2014-02-27T12:00:00",
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

**Azure Blob-uitvoergegevensset**

Gegevens worden elk uur naar een nieuwe BLOB geschreven (**frequentie**: **uur**, **interval**: **1**). Het mappad en de bestands naam voor de BLOB worden dynamisch geëvalueerd op basis van de begin tijd van het segment dat wordt verwerkt. Het mappad maakt gebruik van het jaar, de maand, de dag en het uur gedeelte van de begin tijd.

```json
{
    "name": "AzureBlobOutput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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
            ],
            "format": {
                "type": "TextFormat",
                "columnDelimiter": "\t",
                "rowDelimiter": "\n"
            }
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Pijplijn met een kopieeractiviteit**

De pijp lijn bevat een Kopieer activiteit die is geconfigureerd voor het gebruik van de invoer-en uitvoer gegevens sets en die zijn gepland om per uur te worden uitgevoerd. In de JSON-definitie van de pijp lijn is het **bron** type ingesteld op **OracleSource** en het **sink** -type is ingesteld op **BlobSink**. De SQL-query die u opgeeft met behulp van de eigenschap **oracleReaderQuery** selecteert de gegevens in het afgelopen uur om te kopiëren.

```json
{
    "name":"SamplePipeline",
    "properties":{
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline for a copy activity",
        "activities":[
            {
                "name": "OracletoBlob",
                "description": "copy activity",
                "type": "Copy",
                "inputs": [
                    {
                        "name": " OracleInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutput"
                    }
                ],
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
            }
        ]
    }
}
```

**Voor beeld: gegevens kopiëren van Azure Blob-opslag naar Oracle**

In dit voor beeld ziet u hoe u gegevens kopieert van een Azure Blob-opslag account naar een on-premises Oracle-data base. U kunt gegevens echter *rechtstreeks* kopiëren vanuit een van de bronnen die worden vermeld in [ondersteunde gegevens archieven en-indelingen](data-factory-data-movement-activities.md#supported-data-stores-and-formats) met behulp van de Kopieer activiteit in azure Data Factory.

Het voor beeld heeft de volgende Data Factory entiteiten:

* Een gekoppelde service van het type [OnPremisesOracle](data-factory-onprem-oracle-connector.md#linked-service-properties).
* Een gekoppelde service van het type [opslag](data-factory-azure-blob-connector.md#linked-service-properties).
* Een invoer- [gegevensset](data-factory-create-datasets.md) van het type [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* Een uitvoer [gegevensset](data-factory-create-datasets.md) van het type [OracleTable](data-factory-onprem-oracle-connector.md#dataset-properties).
* Een [pijp lijn](data-factory-create-pipelines.md) met een Kopieer activiteit die gebruikmaakt van [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) als bron [OracleSink](data-factory-onprem-oracle-connector.md#copy-activity-properties) als sink.

In het voor beeld worden elk uur gegevens van een BLOB gekopieerd naar een tabel in een on-premises Oracle-data base. Zie de secties die volgen op de voor beelden voor meer informatie over de verschillende eigenschappen die in het voor beeld worden gebruikt.

**Gekoppelde Oracle-Service**

```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "connectionString": "Data Source=(DESCRIPTION=(ADDRESS=(PROTOCOL=TCP)(HOST=<host name>)(PORT=<port number>))(CONNECT_DATA=(SERVICE_NAME=<service ID>)));
            User Id=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

**Gekoppelde Azure Blob Storage-service**

```json
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>"
        }
    }
}
```

**De Azure Blob-invoergegevensset**

Gegevens worden elk uur uit een nieuwe BLOB opgehaald (**frequentie**: **uur**, **interval**: **1**). Het mappad en de bestands naam voor de BLOB worden dynamisch geëvalueerd op basis van de begin tijd van het segment dat wordt verwerkt. Het mappad gebruikt het deel van het jaar, de maand en de dag van de begin tijd. De bestands naam maakt gebruik van het uur gedeelte van de begin tijd. Met de instelling **extern**: **True** wordt de Data Factory-service informeert dat deze tabel extern is voor de Data Factory en niet wordt geproduceerd door een activiteit in de Data Factory.

```json
{
    "name": "AzureBlobInput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
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
                }
            ],
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            }
        },
        "external": true,
        "availability": {
            "frequency": "Day",
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

**Oracle-uitvoer gegevensset**

In het voor beeld wordt ervan uitgegaan dat u een tabel hebt gemaakt met de naam **myTable** in Oracle. Maak de tabel in Oracle met hetzelfde aantal kolommen waarvan u verwacht dat het CSV-bestand van de BLOB bevat. Nieuwe rijen worden elk uur aan de tabel toegevoegd.

```json
{
    "name": "OracleOutput",
    "properties": {
        "type": "OracleTable",
        "linkedServiceName": "OnPremisesOracleLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "availability": {
            "frequency": "Day",
            "interval": "1"
        }
    }
}
```

**Pijplijn met een kopieeractiviteit**

De pijp lijn bevat een Kopieer activiteit die is geconfigureerd voor het gebruik van de invoer-en uitvoer gegevens sets en die zijn gepland om elk uur te worden uitgevoerd. In de JSON-definitie van de pijp lijn is het **bron** type ingesteld op **BlobSource** en het **sink** -type is ingesteld op **OracleSink**.

```json
{
    "name":"SamplePipeline",
    "properties":{
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-05T19:00:00",
        "description":"pipeline with a copy activity",
        "activities":[
            {
                "name": "AzureBlobtoOracle",
                "description": "Copy Activity",
                "type": "Copy",
                "inputs": [
                    {
                        "name": "AzureBlobInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "OracleOutput"
                    }
                ],
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
            }
        ]
    }
}
```


## <a name="troubleshooting-tips"></a>Tips voor probleemoplossing

### <a name="problem-1-net-framework-data-provider"></a>Probleem 1: .NET Framework gegevens provider

**Fout bericht**

```text
Copy activity met invalid parameters: 'UnknownParameterName', Detailed message: Unable to find the requested .NET Framework Data Provider. It may not be installed.
```

**Mogelijke oorzaken**

* De .NET Framework gegevens provider voor Oracle is niet geïnstalleerd.
* De .NET Framework-gegevens provider voor Oracle is geïnstalleerd op .NET Framework 2,0 en is niet gevonden in de .NET Framework 4,0-mappen.

**Oplossing**

* Als u de .NET-Provider voor Oracle nog niet hebt geïnstalleerd, [installeert u deze](https://www.oracle.com/technetwork/topics/dotnet/downloads/)en voert u het scenario opnieuw uit.
* Als u het fout bericht ziet, zelfs nadat u de provider hebt geïnstalleerd, voert u de volgende stappen uit:
    1. Open het computer configuratie bestand voor .NET 2,0 vanuit de map <systeem schijf \>:\Windows\Microsoft.NET\Framework64\v2.0.50727\CONFIG\machine.config.
    2. Zoek naar de **Oracle-gegevens provider voor .net**. U moet een vermelding kunnen vinden, zoals wordt weer gegeven in het volgende voor beeld onder **System. data**  >  **DbProviderFactories**:`<add name="Oracle Data Provider for .NET" invariant="Oracle.DataAccess.Client" description="Oracle Data Provider for .NET" type="Oracle.DataAccess.Client.OracleClientFactory, Oracle.DataAccess, Version=2.112.3.0, Culture=neutral, PublicKeyToken=89b483f429c47342" />`
* Kopieer deze vermelding naar het machine.config-bestand in de volgende .NET 4,0-map: <systeem schijf \>:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config. Wijzig vervolgens de versie in 4. xxx. x.x.
* Installeer <ODP.NET geïnstalleerd pad \>\11.2.0\client_1\odp.net\bin\4\Oracle.DataAccess.dll in de Global assembly cache (GAC) door **gacutil/i** uit te voeren.

### <a name="problem-2-datetime-formatting"></a>Probleem 2: notatie van datum/tijd

**Fout bericht**

```text
Message=Operation failed in Oracle Database with the following error: 'ORA-01861: literal does not match format string'.,Source=,''Type=Oracle.DataAccess.Client.OracleException,Message=ORA-01861: literal does not match format string,Source=Oracle Data Provider for .NET,'.
```

**Oplossing**

Mogelijk moet u de query reeks in uw Kopieer activiteit aanpassen op basis van de manier waarop datums worden geconfigureerd in de Oracle-data base. Hier volgt een voor beeld (met behulp van de functie **to_date** ):

```console   
"oracleReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= to_date(\\'{0:MM-dd-yyyy HH:mm}\\',\\'MM/DD/YYYY HH24:MI\\') AND timestampcolumn < to_date(\\'{1:MM-dd-yyyy HH:mm}\\',\\'MM/DD/YYYY HH24:MI\\') ', WindowStart, WindowEnd)"
```


## <a name="type-mapping-for-oracle"></a>Type toewijzing voor Oracle

Zoals vermeld in [activiteiten voor gegevens verplaatsing](data-factory-data-movement-activities.md), voert Kopieer activiteit automatische type conversies uit van bron typen naar Sink-typen met behulp van de volgende twee stappen:

1. Conversie van systeem eigen bron typen naar het .NET-type.
2. Converteer van het .NET-type naar het systeem eigen Sink-type.

Wanneer u gegevens van Oracle verplaatst, worden de volgende toewijzingen gebruikt vanuit het Oracle-gegevens type naar het .NET-type en vice versa:

| Oracle-gegevens type | .NET Framework gegevens type |
| --- | --- |
| MENU |Byte [] |
| BLOBCACHE |Byte []<br/>(alleen ondersteund op Oracle 10g en latere versies wanneer u een micro soft-stuur programma gebruikt) |
| CHAR |Tekenreeks |
| CLOB |Tekenreeks |
| DATE |DateTime |
| FLOAT |Decimaal, teken reeks (als precisie > 28) |
| INTEGER |Decimaal, teken reeks (als precisie > 28) |
| INTERVAL JAAR TOT MAAND |Int32 |
| INTERVAL VAN DAG TOT SECONDE |TimeSpan |
| OMVANG |Tekenreeks |
| LANGE ONBEWERKTE |Byte [] |
| NCHAR |Tekenreeks |
| NCLOB |Tekenreeks |
| TELWOORD |Decimaal, teken reeks (als precisie > 28) |
| NVARCHAR2 |Tekenreeks |
| UITGANG |Byte [] |
| ROWID |Tekenreeks |
| TIMESTAMP |DateTime |
| TIJDS TEMPEL MET LOKALE TIJD ZONE |DateTime |
| TIJDS TEMPEL MET TIJD ZONE |DateTime |
| NIET-ONDERTEKEND GEHEEL GETAL |Aantal |
| VARCHAR2 |Tekenreeks |
| XML |Tekenreeks |

> [!NOTE]
> De gegevens typen **interval jaar tot maand** en **interval dag tot seconde** worden niet ondersteund wanneer u een micro soft-stuur programma gebruikt.

## <a name="map-source-to-sink-columns"></a>Bron toewijzen aan Sink-kolommen

Zie [DataSet-kolommen toewijzen in Data Factory](data-factory-map-columns.md)voor meer informatie over het toewijzen van kolommen in de bron-gegevensset naar kolommen in de Sink-gegevensset.

## <a name="repeatable-read-from-relational-sources"></a>Herhaal bare Lees bewerking van relationele bronnen

Wanneer u gegevens van relationele gegevens archieven kopieert, moet u zich herhalen om onbedoelde resultaten te voor komen. In Azure Data Factory kunt u hand matig een segment opnieuw uitvoeren. U kunt ook een beleid voor opnieuw proberen voor een gegevensset configureren zodat een segment opnieuw wordt uitgevoerd wanneer er een fout optreedt. Wanneer een segment opnieuw wordt uitgevoerd, hetzij hand matig of door een beleid voor opnieuw proberen, moet u ervoor zorgen dat dezelfde gegevens worden gelezen, ongeacht het aantal keren dat een segment is gestart. Zie [Herhaal bare Lees bewerking van relationele bronnen](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources)voor meer informatie.

## <a name="performance-and-tuning"></a>Prestaties en afstemmen

Raadpleeg de [hand leiding Copy activity Performance and Tuning (Engelstalig](data-factory-copy-activity-performance.md) ) voor meer informatie over de belangrijkste factoren die van invloed zijn op de prestaties van het verplaatsen van gegevens (Kopieer activiteit) in azure Data Factory. U kunt ook meer informatie over de verschillende manieren om deze te optimaliseren.
