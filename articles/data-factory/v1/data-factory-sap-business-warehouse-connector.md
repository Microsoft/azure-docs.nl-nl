---
title: Gegevens verplaatsen van SAP Business Warehouse met behulp van Azure Data Factory
description: Meer informatie over het verplaatsen van gegevens van SAP Business Warehouse met behulp van Azure Data Factory.
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
robots: noindex
ms.openlocfilehash: 513d6b28908b99594eaa525e86690fa75bffb103
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100386965"
---
# <a name="move-data-from-sap-business-warehouse-using-azure-data-factory"></a>Gegevens verplaatsen van SAP Business Warehouse met behulp van Azure Data Factory
> [!div class="op_single_selector" title1="Selecteer de versie van de Data Factory-service die u gebruikt:"]
> * [Versie 1:](data-factory-sap-business-warehouse-connector.md)
> * [Versie 2 (huidige versie)](../connector-sap-business-warehouse.md)

> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. Als u de huidige versie van de Data Factory-service gebruikt, raadpleegt u [SAP Business Warehouse connector in v2](../connector-sap-business-warehouse.md).


In dit artikel wordt uitgelegd hoe u de Kopieer activiteit in Azure Data Factory kunt gebruiken om gegevens te verplaatsen van een on-premises SAP Business Warehouse (BW). Het is gebaseerd op het artikel [activiteiten voor gegevens verplaatsing](data-factory-data-movement-activities.md) , dat een algemeen overzicht geeft van de verplaatsing van gegevens met de Kopieer activiteit.

U kunt gegevens van een on-premises SAP Business Warehouse-gegevens archief kopiëren naar elk ondersteund Sink-gegevens archief. Zie de tabel [ondersteunde gegevens archieven](data-factory-data-movement-activities.md#supported-data-stores-and-formats) voor een lijst met gegevens archieven die worden ondersteund als sinks op basis van de Kopieer activiteit. Data Factory biedt momenteel alleen ondersteuning voor het verplaatsen van gegevens van een SAP Business Warehouse naar andere gegevens archieven, maar niet voor het verplaatsen van gegevens van andere gegevens archieven naar een SAP Business Warehouse. 

## <a name="supported-versions-and-installation"></a>Ondersteunde versies en installatie
Deze connector ondersteunt SAP Business Warehouse versie 7. x. Het ondersteunt het kopiëren van gegevens uit InfoCubes en QueryCubes (inclusief BEx-query's) met behulp van MDX-query's.

Als u de verbinding met het SAP BW-exemplaar wilt inschakelen, installeert u de volgende onderdelen:
- **Data Management Gateway**: Data Factory-service ondersteunt het maken van verbinding met on-premises gegevens archieven (waaronder SAP Business Warehouse) met behulp van een onderdeel dat Data Management Gateway wordt genoemd. Zie voor meer informatie over Data Management Gateway en stapsgewijze instructies voor het instellen van de gateway, [gegevens verplaatsen tussen een on-premises gegevens archief naar een artikel in de Cloud gegevens opslag](data-factory-move-data-between-onprem-and-cloud.md) . De gateway is vereist, zelfs als het SAP Business Warehouse wordt gehost in een virtuele Azure IaaS-machine (VM). U kunt de gateway op dezelfde VM installeren als het gegevens archief of op een andere virtuele machine zolang de gateway verbinding kan maken met de data base.
- **SAP NetWeaver-bibliotheek** op de gateway computer. U kunt de SAP NetWeaver-bibliotheek ophalen van uw SAP-beheerder of rechtstreeks vanuit het [SAP-software Download centrum](https://support.sap.com/swdc). Zoek naar de **SAP-notitie #1025361** om de download locatie voor de meest recente versie op te halen. Zorg ervoor dat de architectuur voor de SAP NetWeaver-bibliotheek (32-bits of 64-bits) overeenkomt met de installatie van de gateway. Installeer vervolgens alle bestanden die zijn opgenomen in de SAP NetWeaver RFC SDK volgens de SAP-opmerking. De SAP NetWeaver-bibliotheek is ook opgenomen in de installatie van de SAP-client Hulpprogramma's.

> [!TIP]
> Plaats de dll's die zijn geëxtraheerd van de NetWeaver RFC SDK naar de map System32.

## <a name="getting-started"></a>Aan de slag
U kunt een pijp lijn maken met een Kopieer activiteit die gegevens verplaatst van een on-premises Cassandra-gegevens opslag met behulp van verschillende hulpprogram ma's/Api's. 

- De eenvoudigste manier om een pijp lijn te maken, is met behulp van de **wizard kopiëren**. Zie [zelf studie: een pijp lijn maken met behulp van de wizard kopiëren](data-factory-copy-data-wizard-tutorial.md) voor een snelle walkthrough over het maken van een pijp lijn met behulp van de wizard gegevens kopiëren. 
- U kunt ook de volgende hulpprogram ma's gebruiken om een pijp lijn te maken: **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager sjabloon**, **.net API** en **rest API**. Zie [zelf studie Kopieer activiteit](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) voor stapsgewijze instructies voor het maken van een pijp lijn met een Kopieer activiteit. 

Ongeacht of u de hulpprogram ma's of Api's gebruikt, voert u de volgende stappen uit om een pijp lijn te maken waarmee gegevens uit een brongegevens archief naar een Sink-gegevens archief worden verplaatst:

1. Maak **gekoppelde services** om invoer-en uitvoer gegevens archieven te koppelen aan uw Data Factory.
2. Gegevens **sets** maken om invoer-en uitvoer gegevens voor de Kopieer bewerking weer te geven. 
3. Maak een **pijp lijn** met een Kopieer activiteit die een gegevensset als invoer en een gegevensset als uitvoer gebruikt. 

Wanneer u de wizard gebruikt, worden automatisch JSON-definities voor deze Data Factory entiteiten (gekoppelde services, gegevens sets en de pijp lijn) gemaakt. Wanneer u hulpprogram ma's/Api's (met uitzonde ring van .NET API) gebruikt, definieert u deze Data Factory entiteiten met behulp van de JSON-indeling.  Zie voor een voor beeld met JSON-definities voor Data Factory entiteiten die worden gebruikt voor het kopiëren van gegevens van een on-premises SAP Business Warehouse, [JSON-voor beeld: gegevens kopiëren van SAP Business Warehouse naar Azure Blob](#json-example-copy-data-from-sap-business-warehouse-to-azure-blob) sectie van dit artikel. 

De volgende secties bevatten informatie over de JSON-eigenschappen die worden gebruikt voor het definiëren van Data Factory entiteiten die specifiek zijn voor een SAP BW-gegevens archief:

## <a name="linked-service-properties"></a>Eigenschappen van gekoppelde service
In de volgende tabel vindt u een beschrijving van de JSON-elementen die specifiek zijn voor een gekoppelde service van SAP Business Warehouse (BW).

Eigenschap | Beschrijving | Toegestane waarden | Vereist
-------- | ----------- | -------------- | --------
server | De naam van de server waarop het SAP BW-exemplaar zich bevindt. | tekenreeks | Ja
systemNumber | Systeem nummer van het SAP BW systeem. | Decimaal getal van twee cijfers dat wordt weer gegeven als een teken reeks. | Ja
clientId | Client-ID van de client in het SAP W-systeem. | Decimaal getal met drie cijfers dat wordt weer gegeven als een teken reeks. | Ja
gebruikersnaam | Naam van de gebruiker die toegang heeft tot de SAP-server | tekenreeks | Ja
wachtwoord | Het wachtwoord voor de gebruiker. | tekenreeks | Ja
gatewayName | De naam van de gateway die de Data Factory-service moet gebruiken om verbinding te maken met het on-premises SAP BW exemplaar. | tekenreeks | Ja
encryptedCredential | De versleutelde referentie teken reeks. | tekenreeks | No

## <a name="dataset-properties"></a>Eigenschappen van gegevensset
Zie het artikel [gegevens sets maken](data-factory-create-datasets.md) voor een volledige lijst met secties & eigenschappen die beschikbaar zijn voor het definiëren van gegevens sets. Secties zoals structuur, Beschik baarheid en beleid van een gegevensset-JSON zijn vergelijkbaar voor alle typen gegevens sets (Azure SQL, Azure Blob, Azure Table, enzovoort).

De sectie **typeProperties** verschilt voor elk type gegevensset en bevat informatie over de locatie van de gegevens in het gegevens archief. Er zijn geen typespecifieke eigenschappen die worden ondersteund voor de SAP BW-gegevensset van het type **RelationalTable**. 


## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit
Zie het artikel [pijp lijnen maken](data-factory-create-pipelines.md) voor een volledige lijst met secties & eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. Eigenschappen, zoals naam, beschrijving, invoer-en uitvoer tabellen, zijn beschikbaar voor alle typen activiteiten.

Terwijl de eigenschappen die beschikbaar zijn in de sectie **typeProperties** van de activiteit, verschillen per activiteitstype. Voor kopieer activiteiten zijn ze afhankelijk van de typen bronnen en Sinks.

Wanneer de bron in de Kopieer activiteit van het type **RelationalSource** is (inclusief SAP BW), zijn de volgende eigenschappen beschikbaar in de sectie typeProperties:

| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| query | Hiermee geeft u de MDX-query op die gegevens uit het SAP BW exemplaar moet lezen. | MDX-query. | Ja |


## <a name="json-example-copy-data-from-sap-business-warehouse-to-azure-blob"></a>JSON-voor beeld: gegevens kopiëren van SAP Business Warehouse naar Azure Blob
In het volgende voor beeld worden voor beeld-JSON-definities weer gegeven die u kunt gebruiken om een pijp lijn te maken met behulp van [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) of [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Dit voor beeld laat zien hoe u gegevens van een on-premises SAP Business Warehouse kopieert naar een Azure-Blob Storage. Gegevens kunnen echter **rechtstreeks** naar de [hier](data-factory-data-movement-activities.md#supported-data-stores-and-formats) opgegeven sinks worden gekopieerd met behulp van de Kopieer activiteit in azure Data Factory.  

> [!IMPORTANT]
> Dit voor beeld bevat JSON-fragmenten. Het bevat geen stapsgewijze instructies voor het maken van de data factory. Zie [gegevens verplaatsen tussen on-premises locaties en een Cloud](data-factory-move-data-between-onprem-and-cloud.md) artikel voor stapsgewijze instructies.

Het voor beeld heeft de volgende data factory entiteiten:

1. Een gekoppelde service van het type [SapBw](#linked-service-properties).
2. Een gekoppelde service van het type [opslag](data-factory-azure-blob-connector.md#linked-service-properties).
3. Een invoer- [gegevensset](data-factory-create-datasets.md) van het type [RelationalTable](#dataset-properties).
4. Een uitvoer [gegevensset](data-factory-create-datasets.md) van het type [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. Een [pijp lijn](data-factory-create-pipelines.md) met een Kopieer activiteit die gebruikmaakt van [RelationalSource](#copy-activity-properties) en [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Het voor beeld kopieert gegevens van een SAP Business Warehouse-exemplaar naar een Azure-Blob per uur. De JSON-eigenschappen die in deze steek proeven worden gebruikt, worden beschreven in secties die volgen op de voor beelden.

De eerste stap is het instellen van de Data Management Gateway. De instructies bevinden zich in het [verplaatsen van gegevens tussen on-premises locaties en Cloud](data-factory-move-data-between-onprem-and-cloud.md) artikelen.

### <a name="sap-business-warehouse-linked-service"></a>Gekoppelde service van SAP Business Warehouse
Deze gekoppelde service koppelt uw SAP BW-exemplaar aan de data factory. De eigenschap type wordt ingesteld op **SapBw**. De sectie typeProperties bevat verbindings gegevens voor de SAP BW-instantie. 

```json
{
    "name": "SapBwLinkedService",
    "properties":
    {
        "type": "SapBw",
        "typeProperties":
        {
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

### <a name="azure-storage-linked-service"></a>Een gekoppelde Azure Storage-service
Deze gekoppelde service koppelt uw Azure Storage-account aan de data factory. De eigenschap type wordt ingesteld op **opslag**. De sectie typeProperties bevat verbindings gegevens voor het Azure Storage-account.

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

### <a name="sap-bw-input-dataset"></a>Invoer gegevensset SAP BW
Deze gegevensset definieert de SAP Business Warehouse-gegevensset. U stelt het type van de Data Factory gegevensset in op **RelationalTable**. Op dit moment geeft u geen type-specifieke eigenschappen op voor een SAP BW-gegevensset. De query in de definitie van de Kopieer activiteit geeft aan welke gegevens uit het SAP BW exemplaar moeten worden gelezen. 

Als u de eigenschap external instelt op True, wordt de Data Factory-Service getransformeerd dat de tabel zich buiten de data factory bevindt en niet wordt geproduceerd door een activiteit in de data factory.

Met de eigenschappen Frequency en interval wordt de planning gedefinieerd. In dit geval worden de gegevens van het exemplaar van de SAP BW per uur gelezen. 

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



### <a name="azure-blob-output-dataset"></a>Azure Blob-uitvoergegevensset
Deze gegevensset definieert de uitvoer van de Azure Blob-gegevensset. De eigenschap type wordt ingesteld op AzureBlob. De sectie typeProperties geeft aan waar de gekopieerde gegevens uit het SAP BW-exemplaar zijn opgeslagen. De gegevens worden elk uur naar een nieuwe BLOB geschreven (frequentie: uur, interval: 1). Het mappad voor de BLOB wordt dynamisch geëvalueerd op basis van de begin tijd van het segment dat wordt verwerkt. Het mappad gebruikt delen van het jaar, de maand, de dag en het uur van de begin tijd.

```json
{
    "name": "AzureBlobDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/sapbw/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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


### <a name="pipeline-with-copy-activity"></a>Pijp lijn met Kopieer activiteit
De pijp lijn bevat een Kopieer activiteit die is geconfigureerd voor het gebruik van de invoer-en uitvoer gegevens sets en die is gepland om elk uur te worden uitgevoerd. In de JSON-definitie van de pijp lijn is het **bron** type ingesteld op **RelationalSource** (voor de SAP BW bron) en het **sink** -type is ingesteld op **BlobSink**. Met de query die is opgegeven voor de eigenschap **query** selecteert u de gegevens in het afgelopen uur om te kopiëren.

```json
{
    "name": "CopySapBwToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
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
                "inputs": [
                    {
                        "name": "SapBwDataset"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobDataSet"
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
                "name": "SapBwToBlob"
            }
        ],
        "start": "2017-03-01T18:00:00Z",
        "end": "2017-03-01T19:00:00Z"
    }
}
```

### <a name="type-mapping-for-sap-bw"></a>Type toewijzing voor SAP BW
Zoals vermeld in het artikel [activiteiten voor gegevens verplaatsing](data-factory-data-movement-activities.md) voert Kopieer activiteit automatisch type conversies uit van bron typen naar Sink-typen met de volgende twee stappen:

1. Converteren van systeem eigen bron typen naar .NET-type
2. Converteren van .NET-type naar systeem eigen Sink-type

Bij het verplaatsen van gegevens uit SAP BW worden de volgende toewijzingen gebruikt vanuit SAP BW typen naar .NET-typen.

Gegevens type in de ABAP-woorden lijst | .NET-gegevens type
-------------------------------- | --------------
ACCP |    Int
CHAR | Tekenreeks
CLNT | Tekenreeks
LOPEN | Decimaal
CUKY | Tekenreeks
DEC | Decimaal
FLTP | Dubbel
INT1 | Byte
INT2 | Int16
INT4 | Int
LANG | Tekenreeks
LCHR | Tekenreeks
LRAW | Byte []
PREC | Int16
QUAN | Decimaal
UITGANG | Byte []
RAWSTRING | Byte []
STRING | Tekenreeks
TELEENHEID | Tekenreeks
DATS | Tekenreeks
NUMC | Tekenreeks
WISSEN | Tekenreeks

> [!NOTE]
> Als u kolommen van de bron-gegevensset wilt toewijzen aan kolommen uit Sink-gegevensset, raadpleegt u [DataSet-kolommen toewijzen in azure Data Factory](data-factory-map-columns.md).


## <a name="map-source-to-sink-columns"></a>Bron toewijzen aan Sink-kolommen
Zie [DataSet-kolommen toewijzen in azure Data Factory](data-factory-map-columns.md)voor meer informatie over het toewijzen van kolommen in de bron-gegevensset aan kolommen in Sink-gegevensset.

## <a name="repeatable-read-from-relational-sources"></a>Herhaal bare Lees bewerking van relationele bronnen
Houd bij het kopiëren van gegevens uit relationele gegevens archieven de Herhaal baarheid in de hand om onbedoelde resultaten te voor komen. In Azure Data Factory kunt u een segment hand matig opnieuw uitvoeren. U kunt ook beleid voor opnieuw proberen voor een gegevensset configureren zodat een segment opnieuw wordt uitgevoerd wanneer er een fout optreedt. Wanneer een segment op een van beide manieren opnieuw wordt uitgevoerd, moet u ervoor zorgen dat dezelfde gegevens worden gelezen, ongeacht het aantal keren dat een segment wordt gestart. [Herhaal bare Lees bewerking van relationele bronnen](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources) weer geven

## <a name="performance-and-tuning"></a>Prestaties en afstemming
Zie [Kopieer activiteit prestaties & afstemmings handleiding](data-factory-copy-activity-performance.md) voor meer informatie over de belangrijkste factoren die invloed hebben op de prestaties van het verplaatsen van gegevens (Kopieer activiteit) in azure Data Factory en verschillende manieren om deze te optimaliseren.
