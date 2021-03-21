---
title: Gegevens verplaatsen van PostgreSQL met behulp van Azure Data Factory
description: Meer informatie over het verplaatsen van gegevens uit de PostgreSQL-data base met behulp van Azure Data Factory.
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
robots: noindex
ms.openlocfilehash: 8ca8770944a12c6e0dd3e4f95d286758ebee5f9e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100387271"
---
# <a name="move-data-from-postgresql-using-azure-data-factory"></a>Gegevens verplaatsen van PostgreSQL met behulp van Azure Data Factory
> [!div class="op_single_selector" title1="Selecteer de versie van de Data Factory-service die u gebruikt:"]
> * [Versie 1:](data-factory-onprem-postgresql-connector.md)
> * [Versie 2 (huidige versie)](../connector-postgresql.md)

> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. Als u de huidige versie van de Data Factory-service gebruikt, raadpleegt u [postgresql-connector in v2](../connector-postgresql.md).


In dit artikel wordt uitgelegd hoe u de Kopieer activiteit in Azure Data Factory kunt gebruiken om gegevens van een on-premises PostgreSQL-data base te verplaatsen. Het is gebaseerd op het artikel [activiteiten voor gegevens verplaatsing](data-factory-data-movement-activities.md) , dat een algemeen overzicht geeft van de verplaatsing van gegevens met de Kopieer activiteit.

U kunt gegevens van een on-premises PostgreSQL-gegevens opslag kopiëren naar elk ondersteund Sink-gegevens archief. Zie [ondersteunde gegevens archieven](data-factory-data-movement-activities.md#supported-data-stores-and-formats)voor een lijst met gegevens archieven die worden ondersteund als sinks op basis van de Kopieer activiteit. Data Factory biedt momenteel ondersteuning voor het verplaatsen van gegevens van een PostgreSQL-Data Base naar andere gegevens archieven, maar niet voor het verplaatsen van gegevens van andere gegevens archieven naar een PostgreSQL-data base.

## <a name="prerequisites"></a>Vereisten

Data Factory-service ondersteunt het maken van verbinding met on-premises PostgreSQL-bronnen met behulp van de Data Management Gateway. Zie [gegevens verplaatsen tussen on-premises locaties en een Cloud](data-factory-move-data-between-onprem-and-cloud.md) artikel voor meer informatie over Data Management Gateway en stapsgewijze instructies voor het instellen van de gateway.

De gateway is vereist, zelfs als de PostgreSQL-data base wordt gehost in een Azure IaaS-VM. U kunt de gateway op dezelfde IaaS-VM installeren als het gegevens archief of op een andere virtuele machine zolang de gateway verbinding kan maken met de data base.

> [!NOTE]
> Zie problemen [met gateway problemen oplossen](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) voor tips over het oplossen van problemen met verbinding/gateway.

## <a name="supported-versions-and-installation"></a>Ondersteunde versies en installatie
Voor Data Management Gateway verbinding maken met de PostgreSQL-data base, installeert u de [Ngpsql-gegevens provider voor postgresql](https://go.microsoft.com/fwlink/?linkid=282716) met een versie tussen 2.0.12 en 3.1.9 op hetzelfde systeem als de Data Management Gateway. PostgreSQL-versie 7,4 en hoger wordt ondersteund.

## <a name="getting-started"></a>Aan de slag
U kunt een pijp lijn maken met een Kopieer activiteit die gegevens verplaatst van een on-premises PostgreSQL-gegevens opslag met behulp van verschillende hulpprogram ma's/Api's.

- De eenvoudigste manier om een pijp lijn te maken, is met behulp van de **wizard kopiëren**. Zie [zelf studie: een pijp lijn maken met behulp van de wizard kopiëren](data-factory-copy-data-wizard-tutorial.md) voor een snelle walkthrough over het maken van een pijp lijn met behulp van de wizard gegevens kopiëren.
- U kunt ook de volgende hulpprogram ma's gebruiken om een pijp lijn te maken:
  - Visual Studio
  - Azure PowerShell
  - Azure Resource Manager-sjabloon
  - .NET API
  - REST-API

    Zie [zelf studie Kopieer activiteit](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) voor stapsgewijze instructies voor het maken van een pijp lijn met een Kopieer activiteit.

Ongeacht of u de hulpprogram ma's of Api's gebruikt, voert u de volgende stappen uit om een pijp lijn te maken waarmee gegevens uit een brongegevens archief naar een Sink-gegevens archief worden verplaatst:

1. Maak **gekoppelde services** om invoer-en uitvoer gegevens archieven te koppelen aan uw Data Factory.
2. Gegevens **sets** maken om invoer-en uitvoer gegevens voor de Kopieer bewerking weer te geven.
3. Maak een **pijp lijn** met een Kopieer activiteit die een gegevensset als invoer en een gegevensset als uitvoer gebruikt.

Wanneer u de wizard gebruikt, worden automatisch JSON-definities voor deze Data Factory entiteiten (gekoppelde services, gegevens sets en de pijp lijn) gemaakt. Wanneer u hulpprogram ma's/Api's (met uitzonde ring van .NET API) gebruikt, definieert u deze Data Factory entiteiten met behulp van de JSON-indeling. Zie voor een voor beeld met JSON-definities voor Data Factory entiteiten die worden gebruikt voor het kopiëren van gegevens uit een on-premises PostgreSQL-gegevens opslag [JSON-voor beeld: gegevens kopiëren van postgresql naar Azure Blob](#json-example-copy-data-from-postgresql-to-azure-blob) in het gedeelte van dit artikel.

De volgende secties bevatten informatie over de JSON-eigenschappen die worden gebruikt voor het definiëren van Data Factory entiteiten die specifiek zijn voor een PostgreSQL-gegevens archief:

## <a name="linked-service-properties"></a>Eigenschappen van gekoppelde service
In de volgende tabel vindt u een beschrijving van de JSON-elementen die specifiek zijn voor PostgreSQL gekoppelde service.

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| type |De eigenschap type moet worden ingesteld op: **OnPremisesPostgreSql** |Ja |
| server |De naam van de PostgreSQL-server. |Ja |
| database |De naam van de PostgreSQL-data base. |Ja |
| schema |De naam van het schema in de data base. De schema naam is hoofdletter gevoelig. |Nee |
| authenticationType |Type verificatie dat wordt gebruikt om verbinding te maken met de PostgreSQL-data base. Mogelijke waarden zijn: anoniem, basis en Windows. |Ja |
| gebruikersnaam |Geef de gebruikers naam op als u basis-of Windows-verificatie gebruikt. |Nee |
| wachtwoord |Geef het wacht woord op voor het gebruikers account dat u hebt opgegeven voor de gebruikers naam. |Nee |
| gatewayName |De naam van de gateway die de Data Factory-service moet gebruiken om verbinding te maken met de on-premises PostgreSQL-data base. |Ja |

## <a name="dataset-properties"></a>Eigenschappen van gegevensset
Zie het artikel [gegevens sets maken](data-factory-create-datasets.md) voor een volledige lijst met secties & eigenschappen die beschikbaar zijn voor het definiëren van gegevens sets. Secties, zoals structuur, Beschik baarheid en beleid van een gegevensset-JSON, zijn vergelijkbaar voor alle typen gegevensset.

De sectie typeProperties verschilt voor elk type gegevensset en bevat informatie over de locatie van de gegevens in het gegevens archief. De sectie typeProperties voor de gegevensset van het type **RelationalTable** (die de postgresql-gegevensset bevat) heeft de volgende eigenschappen:

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| tableName |De naam van de tabel in de PostgreSQL-data base-instantie waarnaar de gekoppelde service verwijst. De tabel naam is hoofdletter gevoelig. |Nee (als de **query** van **RelationalSource** is opgegeven) |

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit
Zie het artikel [pijp lijnen maken](data-factory-create-pipelines.md) voor een volledige lijst met secties & eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. Eigenschappen zoals naam, beschrijving, invoer-en uitvoer tabellen en beleid zijn beschikbaar voor alle typen activiteiten.

Terwijl de eigenschappen die beschikbaar zijn in de sectie typeProperties van de activiteit, verschillen per activiteitstype. Voor kopieer activiteiten zijn ze afhankelijk van de typen bronnen en Sinks.

Wanneer bron van het type **RelationalSource** (inclusief postgresql), zijn de volgende eigenschappen beschikbaar in de sectie typeProperties:

| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| query |Gebruik de aangepaste query om gegevens te lezen. |SQL-query teken reeks. Bijvoorbeeld: `"query": "select * from \"MySchema\".\"MyTable\""`. |Nee (als **TableName** van **gegevensset** is opgegeven) |

> [!NOTE]
> Schema-en tabel namen zijn hoofdletter gevoelig. Plaats deze in `""` (dubbele aanhalings tekens) in de query.

**Voorbeeld:**

 `"query": "select * from \"MySchema\".\"MyTable\""`

## <a name="json-example-copy-data-from-postgresql-to-azure-blob"></a>JSON-voor beeld: gegevens kopiëren van PostgreSQL naar Azure Blob
Dit voor beeld bevat een voor beeld van JSON-definities die u kunt gebruiken om een pijp lijn te maken met behulp van [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) of [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Ze laten zien hoe u gegevens van PostgreSQL-Data Base naar Azure Blob Storage kopieert. Gegevens kunnen echter worden gekopieerd naar de [hier](data-factory-data-movement-activities.md#supported-data-stores-and-formats) opgegeven sinks met behulp van de Kopieer activiteit in azure Data Factory.

> [!IMPORTANT]
> Dit voor beeld bevat JSON-fragmenten. Het bevat geen stapsgewijze instructies voor het maken van de data factory. Zie [gegevens verplaatsen tussen on-premises locaties en een Cloud](data-factory-move-data-between-onprem-and-cloud.md) artikel voor stapsgewijze instructies.

Het voor beeld heeft de volgende data factory entiteiten:

1. Een gekoppelde service van het type [OnPremisesPostgreSql](data-factory-onprem-postgresql-connector.md#linked-service-properties).
2. Een gekoppelde service van het type [opslag](data-factory-azure-blob-connector.md#linked-service-properties).
3. Een invoer- [gegevensset](data-factory-create-datasets.md) van het type [RelationalTable](data-factory-onprem-postgresql-connector.md#dataset-properties).
4. Een uitvoer [gegevensset](data-factory-create-datasets.md) van het type [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. De [pijp lijn](data-factory-create-pipelines.md) met Kopieer activiteit die gebruikmaakt van [RelationalSource](data-factory-onprem-postgresql-connector.md#copy-activity-properties) en [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

In het voor beeld worden elk uur gegevens van een query resultaat in PostgreSQL-Data Base naar een BLOB gekopieerd. De JSON-eigenschappen die in deze steek proeven worden gebruikt, worden beschreven in secties die volgen op de voor beelden.

De eerste stap is het instellen van de Data Management Gateway. De instructies bevinden zich in het [verplaatsen van gegevens tussen on-premises locaties en Cloud](data-factory-move-data-between-onprem-and-cloud.md) artikelen.

**Gekoppelde PostgreSQL-service:**

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
**Gekoppelde Azure Blob Storage-service:**

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"
        }
    }
}
```
**PostgreSQL invoer gegevensset:**

In het voor beeld wordt ervan uitgegaan dat u in PostgreSQL een tabel ' MyTable ' hebt gemaakt en een kolom bevat met de naam Time Stamp voor tijdreeks gegevens.

De instelling `"external": true` informeert de Data Factory-service dat de gegevensset zich buiten het Data Factory bevindt en wordt niet geproduceerd door een activiteit in de Data Factory.

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

**Azure Blob-uitvoer gegevensset:**

Gegevens worden elk uur naar een nieuwe BLOB geschreven (frequentie: uur, interval: 1). Het mappad en de bestands naam voor de BLOB worden dynamisch geëvalueerd op basis van de begin tijd van het segment dat wordt verwerkt. Het mappad gebruikt delen van het jaar, de maand, de dag en het uur van de begin tijd.

```json
{
    "name": "AzureBlobPostgreSqlDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/postgresql/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

De pijp lijn bevat een Kopieer activiteit die is geconfigureerd voor het gebruik van de invoer-en uitvoer gegevens sets en is gepland om elk uur te worden uitgevoerd. In de JSON-definitie van de pijp lijn is het **bron** type ingesteld op **RelationalSource** en het **sink** -type is ingesteld op **BlobSink**. De SQL-query die is opgegeven voor de eigenschap **query** selecteert de gegevens uit de open bare tabel. usstates in de postgresql-data base.

```json
{
    "name": "CopyPostgreSqlToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
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
                "inputs": [
                    {
                        "name": "PostgreSqlDataSet"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobPostgreSqlDataSet"
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
                "name": "PostgreSqlToBlob"
            }
        ],
        "start": "2014-06-01T18:00:00Z",
        "end": "2014-06-01T19:00:00Z"
    }
}
```
## <a name="type-mapping-for-postgresql"></a>Type toewijzing voor PostgreSQL
Zoals vermeld in het artikel Kopieer activiteit van [gegevens verplaatsing](data-factory-data-movement-activities.md) voert automatische type conversies uit van bron typen naar Sink-typen met de volgende twee stappen:

1. Converteren van systeem eigen bron typen naar .NET-type
2. Converteren van .NET-type naar systeem eigen Sink-type

Bij het verplaatsen van gegevens naar PostgreSQL worden de volgende toewijzingen gebruikt van PostgreSQL type naar .NET-type.

| PostgreSQL-database type | PostgresSQL-aliassen | .NET Framework type |
| --- | --- | --- |
| abstime | |Datum/tijd |
| bigint |int8 |Int64 |
| bigserial |serial8 |Int64 |
| bits [(n)] | |Byte [], teken reeks |
| bits variërend [(n)] |varbit |Byte [], teken reeks |
| booleaans |booleaans |Booleaans |
| keuzelijst | |Byte [], teken reeks |
| bytea | |Byte [], teken reeks |
| teken [(n)] |teken [(n)] |Tekenreeks |
| teken variërend [(n)] |varchar [(n)] |Tekenreeks |
| Cid | |Tekenreeks |
| cidr | |Tekenreeks |
| cirkel | |Byte [], teken reeks |
| date | |Datum/tijd |
| daterange | |Tekenreeks |
| dubbele precisie |float8 |Dubbel |
| inet | |Byte [], teken reeks |
| intarry | |Tekenreeks |
| int4range | |Tekenreeks |
| int8range | |Tekenreeks |
| geheel getal |int, INT4 |Int32 |
| interval [Fields] [(p)] | |Periode |
| json | |Tekenreeks |
| jsonb | |Byte [] |
| lijn | |Byte [], teken reeks |
| lseg | |Byte [], teken reeks |
| macaddr | |Byte [], teken reeks |
| money | |Decimaal |
| numeriek [(p, s)] |decimaal [(p, s)] |Decimaal |
| numrange | |Tekenreeks |
| nogmaals | |Int32 |
| leertraject | |Byte [], teken reeks |
| pg_lsn | |Int64 |
| spreek | |Byte [], teken reeks |
| polygoon | |Byte [], teken reeks |
| werkelijk |float4 |Enkelvoudig |
| smallint |int2 |Int16 |
| smallserial |serial2 |Int16 |
| wel |serial4 |Int32 |
| tekst | |Tekenreeks |

## <a name="map-source-to-sink-columns"></a>Bron toewijzen aan Sink-kolommen
Zie [DataSet-kolommen toewijzen in azure Data Factory](data-factory-map-columns.md)voor meer informatie over het toewijzen van kolommen in de bron-gegevensset aan kolommen in Sink-gegevensset.

## <a name="repeatable-read-from-relational-sources"></a>Herhaal bare Lees bewerking van relationele bronnen
Houd bij het kopiëren van gegevens uit relationele gegevens archieven de Herhaal baarheid in de hand om onbedoelde resultaten te voor komen. In Azure Data Factory kunt u een segment hand matig opnieuw uitvoeren. U kunt ook beleid voor opnieuw proberen voor een gegevensset configureren zodat een segment opnieuw wordt uitgevoerd wanneer er een fout optreedt. Wanneer een segment op een van beide manieren opnieuw wordt uitgevoerd, moet u ervoor zorgen dat dezelfde gegevens worden gelezen, ongeacht het aantal keren dat een segment wordt gestart. Zie [Herhaal bare Lees bewerking van relationele bronnen](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Prestaties en afstemming
Zie [Kopieer activiteit prestaties & afstemmings handleiding](data-factory-copy-activity-performance.md) voor meer informatie over de belangrijkste factoren die invloed hebben op de prestaties van het verplaatsen van gegevens (Kopieer activiteit) in azure Data Factory en verschillende manieren om deze te optimaliseren.
