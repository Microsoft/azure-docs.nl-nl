---
title: Gegevens verplaatsen uit OData-bronnen
description: Meer informatie over het verplaatsen van gegevens uit OData-bronnen met behulp van Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: fae78459a752d78fe47f189bca67667e917ba561
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100380063"
---
# <a name="move-data-from-an-odata-source-using-azure-data-factory"></a>Gegevens verplaatsen vanuit een OData-bron met behulp van Azure Data Factory
> [!div class="op_single_selector" title1="Selecteer de versie van de Data Factory-service die u gebruikt:"]
> * [Versie 1:](data-factory-odata-connector.md)
> * [Versie 2 (huidige versie)](../connector-odata.md)

> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. Als u de huidige versie van de Data Factory-service gebruikt, raadpleegt u [OData-connector in v2](../connector-odata.md).


In dit artikel wordt uitgelegd hoe u de Kopieer activiteit in Azure Data Factory kunt gebruiken om gegevens uit een OData-bron te verplaatsen. Het is gebaseerd op het artikel [activiteiten voor gegevens verplaatsing](data-factory-data-movement-activities.md) , dat een algemeen overzicht geeft van de verplaatsing van gegevens met de Kopieer activiteit.

U kunt gegevens uit een OData-bron kopiëren naar elk ondersteund Sink-gegevens archief. Zie de tabel [ondersteunde gegevens archieven](data-factory-data-movement-activities.md#supported-data-stores-and-formats) voor een lijst met gegevens archieven die worden ondersteund als sinks op basis van de Kopieer activiteit. Data Factory ondersteunt momenteel alleen het verplaatsen van gegevens van een OData-bron naar andere gegevens archieven, maar niet voor het verplaatsen van gegevens van andere gegevens archieven naar een OData-bron.

## <a name="supported-versions-and-authentication-types"></a>Ondersteunde versies en verificatie typen
Deze OData-connector biedt ondersteuning voor OData-versie 3,0 en 4,0, en u kunt gegevens kopiëren van zowel Cloud-OData-als on-premises OData-bronnen. Voor de laatste moet u de Data Management Gateway installeren. Zie [gegevens verplaatsen tussen on-premises en Cloud](data-factory-move-data-between-onprem-and-cloud.md) artikel voor meer informatie over Data Management Gateway.

Onderstaande verificatie typen worden ondersteund:

* Voor toegang tot **Cloud** -OData-feed kunt u anonieme, basis (gebruikers naam en wacht woord) of Azure Active Directory op basis van OAuth-verificatie gebruiken.
* Voor toegang tot **on-premises** OData-feed kunt u anonieme, basis (gebruikers naam en wacht woord) of Windows-verificatie gebruiken.

## <a name="getting-started"></a>Aan de slag
U kunt een pijp lijn maken met een Kopieer activiteit die gegevens verplaatst van een OData-bron met behulp van verschillende hulpprogram ma's/Api's.

De eenvoudigste manier om een pijp lijn te maken, is met behulp van de **wizard kopiëren**. Zie [zelf studie: een pijp lijn maken met behulp van de wizard kopiëren](data-factory-copy-data-wizard-tutorial.md) voor een snelle walkthrough over het maken van een pijp lijn met behulp van de wizard gegevens kopiëren.

U kunt ook de volgende hulpprogram ma's gebruiken om een pijp lijn te maken: **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager sjabloon**, **.net API** en **rest API**. Zie [zelf studie Kopieer activiteit](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) voor stapsgewijze instructies voor het maken van een pijp lijn met een Kopieer activiteit.

Ongeacht of u de hulpprogram ma's of Api's gebruikt, voert u de volgende stappen uit om een pijp lijn te maken waarmee gegevens uit een brongegevens archief naar een Sink-gegevens archief worden verplaatst:

1. Maak **gekoppelde services** om invoer-en uitvoer gegevens archieven te koppelen aan uw Data Factory.
2. Gegevens **sets** maken om invoer-en uitvoer gegevens voor de Kopieer bewerking weer te geven.
3. Maak een **pijp lijn** met een Kopieer activiteit die een gegevensset als invoer en een gegevensset als uitvoer gebruikt.

Wanneer u de wizard gebruikt, worden automatisch JSON-definities voor deze Data Factory entiteiten (gekoppelde services, gegevens sets en de pijp lijn) gemaakt. Wanneer u hulpprogram ma's/Api's (met uitzonde ring van .NET API) gebruikt, definieert u deze Data Factory entiteiten met behulp van de JSON-indeling.  Zie [JSON-voor beeld: gegevens kopiëren van odata-bron naar Azure Blob](#json-example-copy-data-from-odata-source-to-azure-blob) in dit artikel voor een voor beeld met JSON-definities voor Data Factory entiteiten die worden gebruikt voor het kopiëren van gegevens uit een odata-bron.

De volgende secties bevatten informatie over de JSON-eigenschappen die worden gebruikt voor het definiëren van Data Factory entiteiten die specifiek zijn voor de OData-Bron:

## <a name="linked-service-properties"></a>Eigenschappen van gekoppelde service
De volgende tabel bevat een beschrijving van de JSON-elementen die specifiek zijn voor een gekoppelde OData-service.

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| type |De eigenschap type moet worden ingesteld op: **OData** |Yes |
| url |De URL van de OData-service. |Yes |
| authenticationType |Type verificatie dat wordt gebruikt om verbinding te maken met de OData-bron. <br/><br/> Voor Cloud OData zijn mogelijke waarden anoniem, basis en OAuth (Opmerking Azure Data Factory momenteel alleen op Azure Active Directory gebaseerde OAuth). <br/><br/> Voor on-premises OData zijn mogelijke waarden anoniem, basis en Windows. |Yes |
| gebruikersnaam |Geef de gebruikers naam op als u basis verificatie gebruikt. |Ja (alleen als u basis verificatie gebruikt) |
| wachtwoord |Geef het wacht woord op voor het gebruikers account dat u hebt opgegeven voor de gebruikers naam. |Ja (alleen als u basis verificatie gebruikt) |
| authorizedCredential |Als u OAuth gebruikt, klikt u op de knop **machtigen** in de wizard Data Factory kopiëren of de editor en voert u uw referenties in. vervolgens wordt de waarde van deze eigenschap automatisch gegenereerd. |Ja (alleen als u OAuth-verificatie gebruikt) |
| gatewayName |De naam van de gateway die de Data Factory-service moet gebruiken om verbinding te maken met de on-premises OData-service. Geef alleen op als u gegevens kopieert uit on-premises OData-bron. |No |

### <a name="using-basic-authentication"></a>Basis verificatie gebruiken
```json
{
    "name": "inputLinkedService",
    "properties":
    {
        "type": "OData",
        "typeProperties":
        {
            "url": "https://services.odata.org/OData/OData.svc",
            "authenticationType": "Basic",
            "username": "username",
            "password": "password"
        }
    }
}
```

### <a name="using-anonymous-authentication"></a>Anonieme verificatie gebruiken
```json
{
    "name": "ODataLinkedService",
    "properties":
    {
        "type": "OData",
        "typeProperties":
        {
            "url": "https://services.odata.org/OData/OData.svc",
            "authenticationType": "Anonymous"
        }
    }
}
```

### <a name="using-windows-authentication-accessing-on-premises-odata-source"></a>Windows-verificatie gebruiken voor toegang tot de on-premises OData-bron
```json
{
    "name": "inputLinkedService",
    "properties":
    {
        "type": "OData",
        "typeProperties":
        {
            "url": "<endpoint of on-premises OData source e.g. Dynamics CRM>",
            "authenticationType": "Windows",
            "username": "domain\\user",
            "password": "password",
            "gatewayName": "mygateway"
        }
    }
}
```

### <a name="using-oauth-authentication-accessing-cloud-odata-source"></a>OAuth-verificatie gebruiken voor toegang tot Cloud OData-bron
```json
{
    "name": "inputLinkedService",
    "properties":
    {
        "type": "OData",
            "typeProperties":
        {
            "url": "<endpoint of cloud OData source e.g. https://<tenant>.crm.dynamics.com/XRMServices/2011/OrganizationData.svc>",
            "authenticationType": "OAuth",
            "authorizedCredential": "<auto generated by clicking the Authorize button on UI>"
        }
    }
}
```

## <a name="dataset-properties"></a>Eigenschappen van gegevensset
Zie het artikel [gegevens sets maken](data-factory-create-datasets.md) voor een volledige lijst met secties & eigenschappen die beschikbaar zijn voor het definiëren van gegevens sets. Secties zoals structuur, Beschik baarheid en beleid van een gegevensset-JSON zijn vergelijkbaar voor alle typen gegevens sets (Azure SQL, Azure Blob, Azure Table, enzovoort).

De sectie **typeProperties** verschilt voor elk type gegevensset en bevat informatie over de locatie van de gegevens in het gegevens archief. De sectie typeProperties voor de gegevensset van het type **ODataResource** (die OData-gegevensset bevat) heeft de volgende eigenschappen:

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| leertraject |Pad naar de OData-resource |No |

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit
Zie het artikel [pijp lijnen maken](data-factory-create-pipelines.md) voor een volledige lijst met secties & eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. Eigenschappen zoals naam, beschrijving, invoer-en uitvoer tabellen en beleid zijn beschikbaar voor alle typen activiteiten.

De eigenschappen die beschikbaar zijn in de typeProperties-sectie van de activiteit, verschillen per type activiteit. Voor kopieer activiteiten zijn ze afhankelijk van de typen bronnen en Sinks.

Wanneer bron van het type **RelationalSource** (inclusief OData), zijn de volgende eigenschappen beschikbaar in de sectie typeProperties:

| Eigenschap | Beschrijving | Voorbeeld | Vereist |
| --- | --- | --- | --- |
| query |Gebruik de aangepaste query om gegevens te lezen. |"? $select = naam, beschrijving&$top = 5" |No |

## <a name="type-mapping-for-odata"></a>Type toewijzing voor OData
Zoals vermeld in het artikel [activiteiten voor gegevens verplaatsing](data-factory-data-movement-activities.md) voert Kopieer activiteit automatisch type conversies uit van bron typen naar Sink-typen met de volgende twee stappen.

1. Converteren van systeem eigen bron typen naar .NET-type
2. Converteren van .NET-type naar systeem eigen Sink-type

Bij het verplaatsen van gegevens uit OData worden de volgende toewijzingen gebruikt vanuit OData-typen naar .NET-type.

| OData-gegevenstype | .NET-type |
| --- | --- |
| EDM. binary |Byte [] |
| Edm.Boolean |Booleaanse waarde |
| EDM. byte |Byte [] |
| EDM. DateTime |DateTime |
| EDM. decimaal |Decimaal |
| Edm.Double |Dubbel |
| EDM. single |Enkelvoudig |
| EDM. GUID |Guid |
| EDM. Int16 |Int16 |
| Edm.Int32 |Int32 |
| Edm.Int64 |Int64 |
| EDM. SByte |Int16 |
| Edm.String |Tekenreeks |
| EDM. tijd |TimeSpan |
| Edm.DateTimeOffset |Date time offset |

> [!Note]
> Complexe OData-gegevens typen, bijvoorbeeld object, worden niet ondersteund.

## <a name="json-example-copy-data-from-odata-source-to-azure-blob"></a>JSON-voor beeld: gegevens kopiëren van OData-bron naar Azure Blob
Dit voor beeld bevat een voor beeld van JSON-definities die u kunt gebruiken om een pijp lijn te maken met behulp van [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) of [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Ze laten zien hoe gegevens van een OData-bron naar een Azure-Blob Storage kunnen worden gekopieerd. Gegevens kunnen echter worden gekopieerd naar de [hier](data-factory-data-movement-activities.md#supported-data-stores-and-formats) opgegeven sinks met behulp van de Kopieer activiteit in azure Data Factory. Het voor beeld heeft de volgende Data Factory entiteiten:

1. Een gekoppelde service van het type [OData](#linked-service-properties).
2. Een gekoppelde service van het type [opslag](data-factory-azure-blob-connector.md#linked-service-properties).
3. Een invoer- [gegevensset](data-factory-create-datasets.md) van het type [ODataResource](#dataset-properties).
4. Een uitvoer [gegevensset](data-factory-create-datasets.md) van het type [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. Een [pijp lijn](data-factory-create-pipelines.md) met een Kopieer activiteit die gebruikmaakt van [RelationalSource](#copy-activity-properties) en [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Het voor beeld kopieert elk uur gegevens uit een query op een OData-bron naar een Azure-Blob. De JSON-eigenschappen die in deze steek proeven worden gebruikt, worden beschreven in secties die volgen op de voor beelden.

**Gekoppelde OData-service:** In dit voor beeld wordt de anonieme verificatie gebruikt. Zie de sectie [gekoppelde OData-service](#linked-service-properties) voor verschillende typen verificatie die u kunt gebruiken.

```json
{
    "name": "ODataLinkedService",
    "properties":
    {
        "type": "OData",
        "typeProperties":
        {
            "url": "https://services.odata.org/OData/OData.svc",
            "authenticationType": "Anonymous"
        }
    }
}
```

**Azure Storage gekoppelde service:**

```json
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

**OData-invoer gegevensset:**

Als u ' Extern ' instelt, informeert de Data Factory-service dat de gegevensset extern is voor de data factory en wordt deze niet geproduceerd door een activiteit in de data factory.

```json
{
    "name": "ODataDataset",
    "properties":
    {
        "type": "ODataResource",
        "typeProperties":
        {
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

Het opgeven van een **pad** in de definitie van de gegevensset is optioneel.

**Azure Blob-uitvoer gegevensset:**

Gegevens worden elk uur naar een nieuwe BLOB geschreven (frequentie: uur, interval: 1). Het mappad voor de BLOB wordt dynamisch geëvalueerd op basis van de begin tijd van het segment dat wordt verwerkt. Het mappad gebruikt delen van het jaar, de maand, de dag en het uur van de begin tijd.

```json
{
    "name": "AzureBlobODataDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/odata/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

**Kopieer activiteit in een pijp lijn met OData-bron en BLOB-Sink:**

De pijp lijn bevat een Kopieer activiteit die is geconfigureerd voor het gebruik van de invoer-en uitvoer gegevens sets en die is gepland om elk uur te worden uitgevoerd. In de JSON-definitie van de pijp lijn is het **bron** type ingesteld op **RelationalSource** en het **sink** -type is ingesteld op **BlobSink**. De SQL-query die is opgegeven voor de eigenschap **query** selecteert de nieuwste (nieuwste) gegevens uit de OData-bron.

```json
{
    "name": "CopyODataToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "RelationalSource",
                        "query": "?$select=Name, Description&$top=5",
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "ODataDataSet"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobODataDataSet"
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
                "name": "ODataToBlob"
            }
        ],
        "start": "2017-02-01T18:00:00Z",
        "end": "2017-02-03T19:00:00Z"
    }
}
```

Het opgeven van een **query** in de pijplijn definitie is optioneel. De **URL** die de Data Factory-service gebruikt om gegevens op te halen is: de URL die is opgegeven in het gekoppelde service (vereist) + pad dat is opgegeven in de gegevensset (optioneel) + query in de pijp lijn (optioneel).

### <a name="type-mapping-for-odata"></a>Type toewijzing voor OData
Zoals vermeld in het artikel [activiteiten voor gegevens verplaatsing](data-factory-data-movement-activities.md) voert Kopieer activiteit automatisch type conversies uit van bron typen naar Sink-typen met de volgende twee stappen:

1. Converteren van systeem eigen bron typen naar .NET-type
2. Converteren van .NET-type naar systeem eigen Sink-type

Bij het verplaatsen van gegevens uit OData-gegevens archieven worden OData-gegevens typen toegewezen aan .NET-typen.

## <a name="map-source-to-sink-columns"></a>Bron toewijzen aan Sink-kolommen
Zie [DataSet-kolommen toewijzen in azure Data Factory](data-factory-map-columns.md)voor meer informatie over het toewijzen van kolommen in de bron-gegevensset aan kolommen in Sink-gegevensset.

## <a name="repeatable-read-from-relational-sources"></a>Herhaal bare Lees bewerking van relationele bronnen
Houd bij het kopiëren van gegevens uit relationele gegevens archieven de Herhaal baarheid in de hand om onbedoelde resultaten te voor komen. In Azure Data Factory kunt u een segment hand matig opnieuw uitvoeren. U kunt ook beleid voor opnieuw proberen voor een gegevensset configureren zodat een segment opnieuw wordt uitgevoerd wanneer er een fout optreedt. Wanneer een segment op een van beide manieren opnieuw wordt uitgevoerd, moet u ervoor zorgen dat dezelfde gegevens worden gelezen, ongeacht het aantal keren dat een segment wordt gestart. Zie [Herhaal bare Lees bewerking van relationele bronnen](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Prestaties en afstemming
Zie [Kopieer activiteit prestaties & afstemmings handleiding](data-factory-copy-activity-performance.md) voor meer informatie over de belangrijkste factoren die invloed hebben op de prestaties van het verplaatsen van gegevens (Kopieer activiteit) in azure Data Factory en verschillende manieren om deze te optimaliseren.
