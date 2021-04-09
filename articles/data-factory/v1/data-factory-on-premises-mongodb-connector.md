---
title: Gegevens verplaatsen vanuit MongoDB
description: Meer informatie over het verplaatsen van gegevens uit de MongoDB-data base met behulp van Azure Data Factory.
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.topic: conceptual
ms.date: 04/13/2018
ms.openlocfilehash: cedb0b99f04df00763a3ee83287eec90bd5fb45d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100387509"
---
# <a name="move-data-from-mongodb-using-azure-data-factory"></a>Gegevens verplaatsen van MongoDB met behulp van Azure Data Factory

> [!div class="op_single_selector" title1="Selecteer de versie van de Data Factory-service die u gebruikt:"]
> * [Versie 1:](data-factory-on-premises-mongodb-connector.md)
> * [Versie 2 (huidige versie)](../connector-mongodb.md)

> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. Als u de huidige versie van de Data Factory-service gebruikt, raadpleegt u [MongoDb-connector in v2](../connector-mongodb.md).


In dit artikel wordt uitgelegd hoe u de Kopieer activiteit in Azure Data Factory kunt gebruiken om gegevens van een on-premises MongoDB-data base te verplaatsen. Het is gebaseerd op het artikel [activiteiten voor gegevens verplaatsing](data-factory-data-movement-activities.md) , dat een algemeen overzicht geeft van de verplaatsing van gegevens met de Kopieer activiteit.

U kunt gegevens van een on-premises MongoDB-gegevens opslag kopiëren naar elk ondersteund Sink-gegevens archief. Zie de tabel [ondersteunde gegevens archieven](data-factory-data-movement-activities.md#supported-data-stores-and-formats) voor een lijst met gegevens archieven die worden ondersteund als sinks op basis van de Kopieer activiteit. Data Factory biedt momenteel alleen ondersteuning voor het verplaatsen van gegevens van een MongoDB-gegevens archief naar andere gegevens archieven, maar niet voor het verplaatsen van gegevens van andere gegevens archieven naar een MongoDB-Data Store.

## <a name="prerequisites"></a>Vereisten
Als u wilt dat de Azure Data Factory-service verbinding kan maken met uw on-premises MongoDB-data base, moet u de volgende onderdelen installeren:

- Ondersteunde MongoDB-versies zijn: 2,4, 2,6, 3,0, 3,2, 3,4 en 3,6.
- Data Management Gateway op dezelfde computer die als host fungeert voor de data base of op een afzonderlijke machine om te voor komen dat bronnen met de data base concurreren. Data Management Gateway is een software die on-premises gegevens bronnen op een veilige en beheerde manier verbindt met Cloud Services. Zie [Data Management Gateway](data-factory-data-management-gateway.md) artikel voor meer informatie over Data Management Gateway. Zie [gegevens verplaatsen van on-premises naar een Cloud](data-factory-move-data-between-onprem-and-cloud.md) artikel voor stapsgewijze instructies voor het instellen van de gateway met een gegevens pijplijn voor het verplaatsen van gegevens.

    Wanneer u de gateway installeert, wordt automatisch een micro soft MongoDB ODBC-stuur programma geïnstalleerd dat wordt gebruikt om verbinding te maken met MongoDB.

    > [!NOTE]
    > U moet de gateway gebruiken om verbinding te maken met MongoDB, zelfs als deze wordt gehost op virtuele machines van Azure IaaS. Als u probeert verbinding te maken met een instantie van MongoDB die in de Cloud wordt gehost, kunt u ook het gateway-exemplaar installeren in de IaaS-VM.

## <a name="getting-started"></a>Aan de slag
U kunt een pijp lijn maken met een Kopieer activiteit die gegevens verplaatst van een on-premises MongoDB-gegevens opslag met behulp van verschillende hulpprogram ma's/Api's.

De eenvoudigste manier om een pijp lijn te maken, is met behulp van de **wizard kopiëren**. Zie [zelf studie: een pijp lijn maken met behulp van de wizard kopiëren](data-factory-copy-data-wizard-tutorial.md) voor een snelle walkthrough over het maken van een pijp lijn met behulp van de wizard gegevens kopiëren.

U kunt ook de volgende hulpprogram ma's gebruiken om een pijp lijn te maken: **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager sjabloon**, **.net API** en **rest API**. Zie [zelf studie Kopieer activiteit](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) voor stapsgewijze instructies voor het maken van een pijp lijn met een Kopieer activiteit.

Ongeacht of u de hulpprogram ma's of Api's gebruikt, voert u de volgende stappen uit om een pijp lijn te maken waarmee gegevens uit een brongegevens archief naar een Sink-gegevens archief worden verplaatst:

1. Maak **gekoppelde services** om invoer-en uitvoer gegevens archieven te koppelen aan uw Data Factory.
2. Gegevens **sets** maken om invoer-en uitvoer gegevens voor de Kopieer bewerking weer te geven.
3. Maak een **pijp lijn** met een Kopieer activiteit die een gegevensset als invoer en een gegevensset als uitvoer gebruikt.

Wanneer u de wizard gebruikt, worden automatisch JSON-definities voor deze Data Factory entiteiten (gekoppelde services, gegevens sets en de pijp lijn) gemaakt. Wanneer u hulpprogram ma's/Api's (met uitzonde ring van .NET API) gebruikt, definieert u deze Data Factory entiteiten met behulp van de JSON-indeling.  Zie voor een voor beeld met JSON-definities voor Data Factory entiteiten die worden gebruikt voor het kopiëren van gegevens uit een on-premises MongoDB-gegevens opslag [JSON-voor beeld: gegevens kopiëren van MongoDb naar Azure Blob](#json-example-copy-data-from-mongodb-to-azure-blob) in het gedeelte van dit artikel.

De volgende secties bevatten informatie over de JSON-eigenschappen die worden gebruikt voor het definiëren van Data Factory entiteiten die specifiek zijn voor MongoDB Bron:

## <a name="linked-service-properties"></a>Eigenschappen van gekoppelde service
In de volgende tabel vindt u een beschrijving van de JSON-elementen die specifiek zijn voor **OnPremisesMongoDB** gekoppelde service.

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| type |De eigenschap type moet worden ingesteld op: **OnPremisesMongoDb** |Yes |
| server |Het IP-adres of de hostnaam van de MongoDB-server. |Yes |
| poort |TCP-poort die de MongoDB-server gebruikt om te Luis teren naar client verbindingen. |Optioneel, standaard waarde: 27017 |
| authenticationType |Basic of anoniem. |Yes |
| gebruikersnaam |Gebruikers account voor toegang tot MongoDB. |Ja (als basis verificatie wordt gebruikt). |
| wachtwoord |Het wachtwoord voor de gebruiker. |Ja (als basis verificatie wordt gebruikt). |
| authSource |De naam van de MongoDB-data base die u wilt gebruiken om uw referenties voor verificatie te controleren. |Optioneel (als basis verificatie wordt gebruikt). standaard: gebruikt het beheerders account en de data base die is opgegeven met de eigenschap databasename. |
| databaseName |De naam van de MongoDB-data base waartoe u toegang wilt krijgen. |Yes |
| gatewayName |De naam van de gateway die toegang heeft tot het gegevens archief. |Yes |
| encryptedCredential |Referentie versleuteld door gateway. |Optioneel |

## <a name="dataset-properties"></a>Eigenschappen van gegevensset
Zie het artikel [gegevens sets maken](data-factory-create-datasets.md) voor een volledige lijst met secties & eigenschappen die beschikbaar zijn voor het definiëren van gegevens sets. Secties zoals structuur, Beschik baarheid en beleid van een gegevensset-JSON zijn vergelijkbaar voor alle typen gegevens sets (Azure SQL, Azure Blob, Azure Table, enzovoort).

De sectie **typeProperties** verschilt voor elk type gegevensset en bevat informatie over de locatie van de gegevens in het gegevens archief. De sectie typeProperties voor de gegevensset van het type **MongoDbCollection** heeft de volgende eigenschappen:

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| collectionName |De naam van de verzameling in de MongoDB-data base. |Yes |

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit
Zie het artikel [pijp lijnen maken](data-factory-create-pipelines.md) voor een volledige lijst met secties & eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. Eigenschappen zoals naam, beschrijving, invoer-en uitvoer tabellen en beleid zijn beschikbaar voor alle typen activiteiten.

De eigenschappen die beschikbaar zijn in de **typeProperties** -sectie van de activiteit, verschillen per type activiteit. Voor kopieer activiteiten zijn ze afhankelijk van de typen bronnen en Sinks.

Wanneer de bron van het type **MongoDbSource** is, zijn de volgende eigenschappen beschikbaar in de sectie typeProperties:

| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| query |Gebruik de aangepaste query om gegevens te lezen. |SQL-92-query teken reeks. Bijvoorbeeld: Select * from MyTable. |Nee (als de **verzamelings** - **DataSet** is opgegeven) |



## <a name="json-example-copy-data-from-mongodb-to-azure-blob"></a>JSON-voor beeld: gegevens kopiëren van MongoDB naar Azure Blob
Dit voor beeld bevat een voor beeld van JSON-definities die u kunt gebruiken om een pijp lijn te maken met behulp van [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) of [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). U ziet hoe u gegevens van een on-premises MongoDB kopieert naar een Azure-Blob Storage. Gegevens kunnen echter worden gekopieerd naar de [hier](data-factory-data-movement-activities.md#supported-data-stores-and-formats) opgegeven sinks met behulp van de Kopieer activiteit in azure Data Factory.

Het voor beeld heeft de volgende data factory entiteiten:

1. Een gekoppelde service van het type [OnPremisesMongoDb](#linked-service-properties).
2. Een gekoppelde service van het type [opslag](data-factory-azure-blob-connector.md#linked-service-properties).
3. Een invoer- [gegevensset](data-factory-create-datasets.md) van het type [MongoDbCollection](#dataset-properties).
4. Een uitvoer [gegevensset](data-factory-create-datasets.md) van het type [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. Een [pijp lijn](data-factory-create-pipelines.md) met een Kopieer activiteit die gebruikmaakt van [MongoDbSource](#copy-activity-properties) en [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

In het voor beeld worden elk uur gegevens van een query resultaat in MongoDB-Data Base naar een BLOB gekopieerd. De JSON-eigenschappen die in deze steek proeven worden gebruikt, worden beschreven in secties die volgen op de voor beelden.

Als eerste stap stelt u de Data Management Gateway in volgens de instructies in het [Data Management Gateway](data-factory-data-management-gateway.md) -artikel.

**Gekoppelde MongoDB-service:**

```json
{
    "name": "OnPremisesMongoDbLinkedService",
    "properties":
    {
        "type": "OnPremisesMongoDb",
        "typeProperties":
        {
            "authenticationType": "<Basic or Anonymous>",
            "server": "< The IP address or host name of the MongoDB server >",
            "port": "<The number of the TCP port that the MongoDB server uses to listen for client connections.>",
            "username": "<username>",
            "password": "<password>",
            "authSource": "< The database that you want to use to check your credentials for authentication. >",
            "databaseName": "<database name>",
            "gatewayName": "<mygateway>"
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

**MongoDb invoer gegevensset:** Als u ' Extern ' instelt, informeert de Data Factory-service dat de tabel extern is voor de data factory en wordt deze niet geproduceerd door een activiteit in de data factory.

```json
{
    "name": "MongoDbInputDataset",
    "properties": {
        "type": "MongoDbCollection",
        "linkedServiceName": "OnPremisesMongoDbLinkedService",
        "typeProperties": {
            "collectionName": "<Collection name>"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```

**Azure Blob-uitvoer gegevensset:**

Gegevens worden elk uur naar een nieuwe BLOB geschreven (frequentie: uur, interval: 1). Het mappad voor de BLOB wordt dynamisch geëvalueerd op basis van de begin tijd van het segment dat wordt verwerkt. Het mappad gebruikt delen van het jaar, de maand, de dag en het uur van de begin tijd.

```json
{
    "name": "AzureBlobOutputDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/frommongodb/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

**Kopieer activiteit in een pijp lijn met MongoDB-bron en BLOB-Sink:**

De pijp lijn bevat een Kopieer activiteit die is geconfigureerd voor het gebruik van de bovenstaande invoer-en uitvoer gegevens sets en is gepland om elk uur te worden uitgevoerd. In de JSON-definitie van de pijp lijn is het **bron** type ingesteld op **MongoDbSource** en het **sink** -type is ingesteld op **BlobSink**. Met de SQL-query die is opgegeven voor de **query** -eigenschap worden de gegevens in het afgelopen uur geselecteerd om te kopiëren.

```json
{
    "name": "CopyMongoDBToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "MongoDbSource",
                        "query": "$$Text.Format('select * from MyTable where LastModifiedDate >= {{ts\'{0:yyyy-MM-dd HH:mm:ss}\'}} AND LastModifiedDate < {{ts\'{1:yyyy-MM-dd HH:mm:ss}\'}}', WindowStart, WindowEnd)"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "MongoDbInputDataset"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutputDataSet"
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
                "name": "MongoDBToAzureBlob"
            }
        ],
        "start": "2016-06-01T18:00:00Z",
        "end": "2016-06-01T19:00:00Z"
    }
}
```


## <a name="schema-by-data-factory"></a>Schema door Data Factory
Azure Data Factory Service-schema van een MongoDB-verzameling met behulp van de meest recente 100-documenten in de verzameling. Als deze 100-documenten geen volledig schema bevatten, kunnen sommige kolommen tijdens de Kopieer bewerking worden genegeerd.

## <a name="type-mapping-for-mongodb"></a>Type toewijzing voor MongoDB
Zoals vermeld in het artikel [activiteiten voor gegevens verplaatsing](data-factory-data-movement-activities.md) voert Kopieer activiteit automatisch type conversies uit van bron typen naar Sink-typen met de volgende twee stappen:

1. Converteren van systeem eigen bron typen naar .NET-type
2. Converteren van .NET-type naar systeem eigen Sink-type

Bij het verplaatsen van gegevens naar MongoDB worden de volgende toewijzingen gebruikt vanuit MongoDB-typen naar .NET-typen.

| Type MongoDB | .NET Framework type |
| --- | --- |
| Binair |Byte [] |
| Boolean |Booleaans |
| Date |DateTime |
| NumberDouble |Dubbel |
| NumberInt |Int32 |
| NumberLong |Int64 |
| ObjectID |Tekenreeks |
| Tekenreeks |Tekenreeks |
| UUID |Guid |
| Object |Opnieuw genormaliseerd in kolommen met ' _ ' als genest scheidings teken |

> [!NOTE]
> Raadpleeg voor meer informatie over ondersteuning voor matrices met behulp van virtuele tabellen de sectie [ondersteuning voor complexe typen met virtuele tabellen](#support-for-complex-types-using-virtual-tables) .

De volgende MongoDB-gegevens typen worden momenteel niet ondersteund: DBPointer, java script, max/min-sleutel, reguliere expressie, symbool, Time Stamp, niet gedefinieerd

## <a name="support-for-complex-types-using-virtual-tables"></a>Ondersteuning voor complexe typen met virtuele tabellen
Azure Data Factory maakt gebruik van een ingebouwd ODBC-stuur programma om verbinding te maken met gegevens uit uw MongoDB-data base en deze te kopiëren. Voor complexe typen, zoals matrices of objecten met verschillende typen in de documenten, worden de gegevens in de bijbehorende virtuele tabellen opnieuw genormaliseerd. Met name als een tabel dergelijke kolommen bevat, genereert het stuur programma de volgende virtuele tabellen:

* Een **basis tabel** met dezelfde gegevens als de echte tabel, met uitzonde ring van de kolommen van het type complex. Voor de basis tabel wordt dezelfde naam gebruikt als voor de echte tabel die deze vertegenwoordigt.
* Een **virtuele tabel** voor elke kolom met complexe typen, waarmee de geneste gegevens worden uitgevouwen. De virtuele tabellen krijgen een naam met de naam van de tabel Real, een scheidings teken ' _ ' en de naam van de matrix of het object.

Virtuele tabellen verwijzen naar de gegevens in de tabel Real, waardoor het stuur programma toegang kan krijgen tot de Gedenormaliseerde gegevens. Zie het gedeelte voor beeld hieronder voor meer informatie. U kunt toegang krijgen tot de inhoud van MongoDB-matrices door de virtuele tabellen op te vragen en te koppelen.

U kunt de [wizard kopiëren](data-factory-data-movement-activities.md#create-a-pipeline-with-copy-activity) gebruiken om de lijst met tabellen intuïtief weer te geven in de MongoDb-data base, met inbegrip van de virtuele tabellen en een voor beeld van de gegevens in. U kunt ook een query in de wizard kopiëren maken en valideren om het resultaat te bekijken.

### <a name="example"></a>Voorbeeld
Bijvoorbeeld: ' ExampleTable ' hieronder is een MongoDB-tabel met één kolom met een matrix van objecten in elke cel, facturen en één kolom met een matrix van scalaire typen – classificaties.

| _id | Naam van klant | Facturen | Serviceniveau | Waarderingen |
| --- | --- | --- | --- | --- |
| 1111 |ABC |[{invoice_id: "123", item: "pop-uptaak", prijs: "456", korting: "0,2"}, {invoice_id: "124", item: "oven", prijs: "1235", korting: "0,2"}] |Zilver |[5, 6] |
| 2222 |XYZ |[{invoice_id: "135", item: "koel kast", prijs: "12543", korting: "0,0"}] |Goud |[1, 2] |

Het stuur programma genereert meerdere virtuele tabellen om deze afzonderlijke tabel weer te geven. De eerste virtuele tabel is de basis tabel met de naam ' ExampleTable ', zoals hieronder wordt weer gegeven. De basis tabel bevat alle gegevens van de oorspronkelijke tabel, maar de gegevens uit de matrices zijn wegge laten en worden uitgevouwen in de virtuele tabellen.

| _id | Naam van klant | Serviceniveau |
| --- | --- | --- |
| 1111 |ABC |Zilver |
| 2222 |XYZ |Goud |

In de volgende tabellen ziet u de virtuele tabellen die de oorspronkelijke matrices in het voor beeld vertegenwoordigen. Deze tabellen bevatten het volgende:

* Een verwijzing naar de oorspronkelijke primaire-sleutel kolom die overeenkomt met de rij van de oorspronkelijke matrix (via de kolom _id)
* Een indicatie van de positie van de gegevens in de oorspronkelijke matrix
* De uitgevouwen gegevens voor elk element in de matrix

Tabel "ExampleTable_Invoices":

| _id | ExampleTable_Invoices_dim1_idx | invoice_id | item | price | Korting |
| --- | --- | --- | --- | --- | --- |
| 1111 |0 |123 |pop- |456 |0,2 |
| 1111 |1 |124 |droog |1235 |0,2 |
| 2222 |0 |135 |koel kast |12543 |0,0 |

Tabel "ExampleTable_Ratings":

| _id | ExampleTable_Ratings_dim1_idx | ExampleTable_Ratings |
| --- | --- | --- |
| 1111 |0 |5 |
| 1111 |1 |6 |
| 2222 |0 |1 |
| 2222 |1 |2 |

## <a name="map-source-to-sink-columns"></a>Bron toewijzen aan Sink-kolommen
Zie [DataSet-kolommen toewijzen in azure Data Factory](data-factory-map-columns.md)voor meer informatie over het toewijzen van kolommen in de bron-gegevensset aan kolommen in Sink-gegevensset.

## <a name="repeatable-read-from-relational-sources"></a>Herhaal bare Lees bewerking van relationele bronnen
Houd bij het kopiëren van gegevens uit relationele gegevens archieven de Herhaal baarheid in de hand om onbedoelde resultaten te voor komen. In Azure Data Factory kunt u een segment hand matig opnieuw uitvoeren. U kunt ook beleid voor opnieuw proberen voor een gegevensset configureren zodat een segment opnieuw wordt uitgevoerd wanneer er een fout optreedt. Wanneer een segment op een van beide manieren opnieuw wordt uitgevoerd, moet u ervoor zorgen dat dezelfde gegevens worden gelezen, ongeacht het aantal keren dat een segment wordt gestart. Zie [Herhaal bare Lees bewerking van relationele bronnen](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Prestaties en afstemming
Zie [Kopieer activiteit prestaties & afstemmings handleiding](data-factory-copy-activity-performance.md) voor meer informatie over de belangrijkste factoren die invloed hebben op de prestaties van het verplaatsen van gegevens (Kopieer activiteit) in azure Data Factory en verschillende manieren om deze te optimaliseren.

## <a name="next-steps"></a>Volgende stappen
Zie [gegevens verplaatsen tussen on-premises en Cloud](data-factory-move-data-between-onprem-and-cloud.md) artikel voor stapsgewijze instructies voor het maken van een gegevens pijplijn die gegevens verplaatst van een on-premises gegevens opslag naar een Azure-gegevens archief.
