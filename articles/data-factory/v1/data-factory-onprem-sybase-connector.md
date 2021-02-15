---
title: Gegevens van Sybase verplaatsen met behulp van Azure Data Factory
description: Meer informatie over het verplaatsen van gegevens uit de Sybase-data base met behulp van Azure Data Factory.
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/02/2018
robots: noindex
ms.openlocfilehash: 8142ec51f93a066faafa1f3e44d0f87517187425
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100387237"
---
# <a name="move-data-from-sybase-using-azure-data-factory"></a>Gegevens van Sybase verplaatsen met behulp van Azure Data Factory
> [!div class="op_single_selector" title1="Selecteer de versie van de Data Factory-service die u gebruikt:"]
> * [Versie 1:](data-factory-onprem-sybase-connector.md)
> * [Versie 2 (huidige versie)](../connector-sybase.md)

> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. Als u de huidige versie van de Data Factory-service gebruikt, raadpleegt u [Sybase connector in v2](../connector-sybase.md).

In dit artikel wordt uitgelegd hoe u de Kopieer activiteit in Azure Data Factory kunt gebruiken om gegevens van een on-premises Sybase-data base te verplaatsen. Het is gebaseerd op het artikel [activiteiten voor gegevens verplaatsing](data-factory-data-movement-activities.md) , dat een algemeen overzicht geeft van de verplaatsing van gegevens met de Kopieer activiteit.

U kunt gegevens van een on-premises Sybase-gegevens opslag kopiëren naar elk ondersteund Sink-gegevens archief. Zie de tabel [ondersteunde gegevens archieven](data-factory-data-movement-activities.md#supported-data-stores-and-formats) voor een lijst met gegevens archieven die worden ondersteund als sinks op basis van de Kopieer activiteit. Data Factory biedt momenteel alleen ondersteuning voor het verplaatsen van gegevens van een Sybase-gegevens opslag naar andere gegevens archieven, maar niet voor het verplaatsen van gegevens van andere gegevens archieven naar een Sybase-gegevens archief. 

## <a name="prerequisites"></a>Vereisten
Data Factory-service ondersteunt het maken van verbinding met on-premises Sybase-bronnen met behulp van de Data Management Gateway. Zie [gegevens verplaatsen tussen on-premises locaties en een Cloud](data-factory-move-data-between-onprem-and-cloud.md) artikel voor meer informatie over Data Management Gateway en stapsgewijze instructies voor het instellen van de gateway.

De gateway is vereist, zelfs als de Sybase-data base wordt gehost in een Azure IaaS-VM. U kunt de gateway op dezelfde IaaS-VM installeren als het gegevens archief of op een andere virtuele machine zolang de gateway verbinding kan maken met de data base.

> [!NOTE]
> Zie problemen [met gateway problemen oplossen](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) voor tips over het oplossen van problemen met verbinding/gateway.

## <a name="supported-versions-and-installation"></a>Ondersteunde versies en installatie
Als Data Management Gateway verbinding met de Sybase-Data Base wilt maken, moet u de [gegevens provider voor Sybase iAnywhere. data. SQLAnywhere](https://go.microsoft.com/fwlink/?linkid=324846) 16 of hoger installeren op hetzelfde systeem als de Data Management Gateway. 

SAP Sybase SQL Anywhere (ASA) versie 16 en hoger wordt ondersteund; IQ en ASE worden niet ondersteund.

## <a name="getting-started"></a>Aan de slag
U kunt een pijp lijn maken met een Kopieer activiteit die gegevens verplaatst van een on-premises Cassandra-gegevens opslag met behulp van verschillende hulpprogram ma's/Api's. 

- De eenvoudigste manier om een pijp lijn te maken, is met behulp van de **wizard kopiëren**. Zie [zelf studie: een pijp lijn maken met behulp van de wizard kopiëren](data-factory-copy-data-wizard-tutorial.md) voor een snelle walkthrough over het maken van een pijp lijn met behulp van de wizard gegevens kopiëren. 
- U kunt ook de volgende hulpprogram ma's gebruiken om een pijp lijn te maken: **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager sjabloon**, **.net API** en **rest API**. Zie [zelf studie Kopieer activiteit](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) voor stapsgewijze instructies voor het maken van een pijp lijn met een Kopieer activiteit. 

Ongeacht of u de hulpprogram ma's of Api's gebruikt, voert u de volgende stappen uit om een pijp lijn te maken waarmee gegevens uit een brongegevens archief naar een Sink-gegevens archief worden verplaatst:

1. Maak **gekoppelde services** om invoer-en uitvoer gegevens archieven te koppelen aan uw Data Factory.
2. Gegevens **sets** maken om invoer-en uitvoer gegevens voor de Kopieer bewerking weer te geven. 
3. Maak een **pijp lijn** met een Kopieer activiteit die een gegevensset als invoer en een gegevensset als uitvoer gebruikt. 

Wanneer u de wizard gebruikt, worden automatisch JSON-definities voor deze Data Factory entiteiten (gekoppelde services, gegevens sets en de pijp lijn) gemaakt. Wanneer u hulpprogram ma's/Api's (met uitzonde ring van .NET API) gebruikt, definieert u deze Data Factory entiteiten met behulp van de JSON-indeling.  Zie [JSON-voor beeld: gegevens kopiëren van Sybase naar Azure Blob](#json-example-copy-data-from-sybase-to-azure-blob) in dit artikel voor een voor beeld met JSON-definities voor Data Factory entiteiten die worden gebruikt voor het kopiëren van gegevens uit een on-premises Sybase-gegevens opslag. 

De volgende secties bevatten informatie over de JSON-eigenschappen die worden gebruikt voor het definiëren van Data Factory entiteiten die specifiek zijn voor een Sybase-gegevens opslag:

## <a name="linked-service-properties"></a>Eigenschappen van gekoppelde service
In de volgende tabel vindt u een beschrijving van de JSON-elementen die specifiek zijn voor een met Sybase gekoppelde service.

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| type |De eigenschap type moet worden ingesteld op: **OnPremisesSybase** |Yes |
| server |De naam van de Sybase-server. |Yes |
| database |De naam van de Sybase-data base. |Yes |
| schema |De naam van het schema in de data base. |No |
| authenticationType |Type verificatie dat wordt gebruikt om verbinding te maken met de Sybase-data base. Mogelijke waarden zijn: anoniem, basis en Windows. |Yes |
| gebruikersnaam |Geef de gebruikers naam op als u basis-of Windows-verificatie gebruikt. |No |
| wachtwoord |Geef het wacht woord op voor het gebruikers account dat u hebt opgegeven voor de gebruikers naam. |No |
| gatewayName |De naam van de gateway die de Data Factory-service moet gebruiken om verbinding te maken met de on-premises Sybase-data base. |Yes |

## <a name="dataset-properties"></a>Eigenschappen van gegevensset
Zie het artikel [gegevens sets maken](data-factory-create-datasets.md) voor een volledige lijst met secties & eigenschappen die beschikbaar zijn voor het definiëren van gegevens sets. Secties zoals structuur, Beschik baarheid en beleid van een gegevensset-JSON zijn vergelijkbaar voor alle typen gegevens sets (Azure SQL, Azure Blob, Azure Table, enzovoort).

De sectie typeProperties verschilt voor elk type gegevensset en bevat informatie over de locatie van de gegevens in het gegevens archief. De sectie **typeProperties** voor de gegevensset van het type **RelationalTable** (inclusief Sybase dataset) heeft de volgende eigenschappen:

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| tableName |De naam van de tabel in het data base-exemplaar van Sybase waarnaar de gekoppelde service verwijst. |Nee (als de **query** van **RelationalSource** is opgegeven) |

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit
Zie [het artikel pipelines maken](data-factory-create-pipelines.md) voor een volledige lijst met secties & eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. Eigenschappen zoals naam, beschrijving, invoer-en uitvoer tabellen en beleid zijn beschikbaar voor alle typen activiteiten.

Terwijl de eigenschappen die beschikbaar zijn in de sectie typeProperties van de activiteit, verschillen per activiteitstype. Voor kopieer activiteiten zijn ze afhankelijk van de typen bronnen en Sinks.

Wanneer de bron van het type **RelationalSource** (inclusief Sybase) is, zijn de volgende eigenschappen beschikbaar in de sectie **typeProperties** :

| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| query |Gebruik de aangepaste query om gegevens te lezen. |SQL-query teken reeks. Bijvoorbeeld: Select * from MyTable. |Nee (als **TableName** van **gegevensset** is opgegeven) |


## <a name="json-example-copy-data-from-sybase-to-azure-blob"></a>JSON-voor beeld: gegevens kopiëren van Sybase naar Azure Blob
In het volgende voor beeld worden voor beeld-JSON-definities weer gegeven die u kunt gebruiken om een pijp lijn te maken met behulp van [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) of [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Ze laten zien hoe u gegevens van Sybase-Data Base kunt kopiëren naar Azure Blob Storage. Gegevens kunnen echter worden gekopieerd naar de [hier](data-factory-data-movement-activities.md#supported-data-stores-and-formats) opgegeven sinks met behulp van de Kopieer activiteit in azure Data Factory.   

Het voor beeld heeft de volgende data factory entiteiten:

1. Een gekoppelde service van het type [OnPremisesSybase](data-factory-onprem-sybase-connector.md#linked-service-properties).
2. Een leuk service van het type [opslag](data-factory-azure-blob-connector.md#linked-service-properties).
3. Een invoer- [gegevensset](data-factory-create-datasets.md) van het type [RelationalTable](data-factory-onprem-sybase-connector.md#dataset-properties).
4. Een uitvoer [gegevensset](data-factory-create-datasets.md) van het type [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. De [pijp lijn](data-factory-create-pipelines.md) met Kopieer activiteit die gebruikmaakt van [RelationalSource](data-factory-onprem-sybase-connector.md#copy-activity-properties) en [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

In het voor beeld worden gegevens gekopieerd van een query resultaat in Sybase-Data Base naar een BLOB elk uur. De JSON-eigenschappen die in deze steek proeven worden gebruikt, worden beschreven in secties die volgen op de voor beelden.

De eerste stap is het instellen van de Data Management Gateway. De instructies bevinden zich in het [verplaatsen van gegevens tussen on-premises locaties en Cloud](data-factory-move-data-between-onprem-and-cloud.md) artikelen.

**Gekoppelde Sybase-service:**

```JSON
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

**Gekoppelde Azure Blob Storage-service:**

```JSON
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

**Sybase-invoer gegevensset:**

In het voor beeld wordt ervan uitgegaan dat u een tabel ' MyTable ' hebt gemaakt in Sybase en deze een kolom bevat met de naam Time Stamp voor tijdreeks gegevens.

Als u ' Extern ': True informeert de Data Factory-service dat deze gegevensset extern is voor de data factory en niet wordt geproduceerd door een activiteit in de data factory. U ziet dat het **type** van de gekoppelde service is ingesteld op: **RelationalTable**.

```JSON
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

**Azure Blob-uitvoer gegevensset:**

Gegevens worden elk uur naar een nieuwe BLOB geschreven (frequentie: uur, interval: 1). Het mappad voor de BLOB wordt dynamisch geëvalueerd op basis van de begin tijd van het segment dat wordt verwerkt. Het mappad gebruikt delen van het jaar, de maand, de dag en het uur van de begin tijd.

```JSON
{
    "name": "AzureBlobSybaseDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/sybase/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

De pijp lijn bevat een Kopieer activiteit die is geconfigureerd voor het gebruik van de invoer-en uitvoer gegevens sets en is gepland om elk uur te worden uitgevoerd. In de JSON-definitie van de pijp lijn is het **bron** type ingesteld op **RelationalSource** en het **sink** -type is ingesteld op **BlobSink**. Met de SQL-query die is opgegeven voor de **query** -eigenschap worden de gegevens uit de DBA geselecteerd. De tabel Orders in de data base.

```JSON
{
    "name": "CopySybaseToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
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
                "inputs": [
                    {
                        "name": "SybaseDataSet"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobSybaseDataSet"
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
                "name": "SybaseToBlob"
            }
        ],
        "start": "2014-06-01T18:00:00Z",
        "end": "2014-06-01T19:00:00Z"
    }
}
```

## <a name="type-mapping-for-sybase"></a>Type toewijzing voor Sybase
Zoals vermeld in het artikel [activiteiten voor gegevens verplaatsing](data-factory-data-movement-activities.md) voert de Kopieer activiteit automatisch type conversies uit van bron typen naar Sink-typen met de volgende twee stappen:

1. Converteren van systeem eigen bron typen naar .NET-type
2. Converteren van .NET-type naar systeem eigen Sink-type

Sybase ondersteunt T-SQL-en T-SQL-typen. Zie het artikel over [Azure SQL-connector](data-factory-azure-sql-connector.md) voor een toewijzings tabel van SQL-typen naar .net-type.

## <a name="map-source-to-sink-columns"></a>Bron toewijzen aan Sink-kolommen
Zie [DataSet-kolommen toewijzen in azure Data Factory](data-factory-map-columns.md)voor meer informatie over het toewijzen van kolommen in de bron-gegevensset aan kolommen in Sink-gegevensset.

## <a name="repeatable-read-from-relational-sources"></a>Herhaal bare Lees bewerking van relationele bronnen
Houd bij het kopiëren van gegevens uit relationele gegevens archieven de Herhaal baarheid in de hand om onbedoelde resultaten te voor komen. In Azure Data Factory kunt u een segment hand matig opnieuw uitvoeren. U kunt ook beleid voor opnieuw proberen voor een gegevensset configureren zodat een segment opnieuw wordt uitgevoerd wanneer er een fout optreedt. Wanneer een segment op een van beide manieren opnieuw wordt uitgevoerd, moet u ervoor zorgen dat dezelfde gegevens worden gelezen, ongeacht het aantal keren dat een segment wordt gestart. Zie [Herhaal bare Lees bewerking van relationele bronnen](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Prestaties en afstemming
Zie [Kopieer activiteit prestaties & afstemmings handleiding](data-factory-copy-activity-performance.md) voor meer informatie over de belangrijkste factoren die invloed hebben op de prestaties van het verplaatsen van gegevens (Kopieer activiteit) in azure Data Factory en verschillende manieren om deze te optimaliseren.
