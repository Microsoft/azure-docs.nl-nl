---
title: Gegevens verplaatsen van Cassandra met behulp van Data Factory
description: Meer informatie over het verplaatsen van gegevens uit een on-premises Cassandra-data base met behulp van Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 06/07/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 005fd85a152ee2765facda0d961bd9119d1598e8
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100387407"
---
# <a name="move-data-from-an-on-premises-cassandra-database-using-azure-data-factory"></a>Gegevens verplaatsen van een on-premises Cassandra-data base met behulp van Azure Data Factory
> [!div class="op_single_selector" title1="Selecteer de versie van de Data Factory-service die u gebruikt:"]
> * [Versie 1:](data-factory-onprem-cassandra-connector.md)
> * [Versie 2 (huidige versie)](../connector-cassandra.md)

> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. Als u de huidige versie van de Data Factory-service gebruikt, raadpleegt u [Cassandra-connector in v2](../connector-cassandra.md).

In dit artikel wordt uitgelegd hoe u de Kopieer activiteit in Azure Data Factory kunt gebruiken om gegevens van een on-premises Cassandra-data base te verplaatsen. Het is gebaseerd op het artikel [activiteiten voor gegevens verplaatsing](data-factory-data-movement-activities.md) , dat een algemeen overzicht geeft van de verplaatsing van gegevens met de Kopieer activiteit.

U kunt gegevens van een on-premises Cassandra-gegevens opslag kopiëren naar elk ondersteund Sink-gegevens archief. Zie de tabel [ondersteunde gegevens archieven](data-factory-data-movement-activities.md#supported-data-stores-and-formats) voor een lijst met gegevens archieven die worden ondersteund als sinks op basis van de Kopieer activiteit. Data Factory ondersteunt momenteel alleen het verplaatsen van gegevens van een Cassandra-gegevens archief naar andere gegevens archieven, maar niet voor het verplaatsen van gegevens van andere gegevens archieven naar een Cassandra-gegevens archief.

## <a name="supported-versions"></a>Ondersteunde versies
De Cassandra-connector ondersteunt de volgende versies van Cassandra: 2. x en 3. x. Voor activiteiten die worden uitgevoerd op zelf-hostende Integration Runtime, wordt Cassandra 3. x ondersteund, aangezien IR-versie 3,7 en hoger.

## <a name="prerequisites"></a>Vereisten
Als u wilt dat de Azure Data Factory-service verbinding kan maken met uw on-premises Cassandra-data base, moet u een Data Management Gateway installeren op dezelfde computer die als host fungeert voor de data base of op een afzonderlijke machine om te voor komen dat bronnen met de data base concurreren. Data Management Gateway is een onderdeel dat on-premises gegevens bronnen met Cloud Services op een veilige en beheerde manier verbindt. Zie [Data Management Gateway](data-factory-data-management-gateway.md) artikel voor meer informatie over Data Management Gateway. Zie [gegevens verplaatsen van on-premises naar een Cloud](data-factory-move-data-between-onprem-and-cloud.md) artikel voor stapsgewijze instructies voor het instellen van de gateway met een gegevens pijplijn voor het verplaatsen van gegevens.

U moet de gateway gebruiken om verbinding te maken met een Cassandra-data base, zelfs als de data base in de Cloud wordt gehost, bijvoorbeeld op een Azure IaaS VM. U kunt de gateway op dezelfde VM die als host fungeert voor de-data base of op een afzonderlijke virtuele machine, mits de gateway verbinding kan maken met de data base.

Wanneer u de gateway installeert, wordt automatisch een micro soft Cassandra ODBC-stuur programma geïnstalleerd dat wordt gebruikt om verbinding te maken met de Cassandra-data base. Daarom hoeft u niet hand matig een stuur programma te installeren op de gateway computer wanneer u gegevens kopieert vanuit de Cassandra-data base.

> [!NOTE]
> Zie problemen [met gateway problemen oplossen](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) voor tips over het oplossen van problemen met verbinding/gateway.

## <a name="getting-started"></a>Aan de slag
U kunt een pijp lijn maken met een Kopieer activiteit die gegevens verplaatst van een on-premises Cassandra-gegevens opslag met behulp van verschillende hulpprogram ma's/Api's.

- De eenvoudigste manier om een pijp lijn te maken, is met behulp van de **wizard kopiëren**. Zie [zelf studie: een pijp lijn maken met behulp van de wizard kopiëren](data-factory-copy-data-wizard-tutorial.md) voor een snelle walkthrough over het maken van een pijp lijn met behulp van de wizard gegevens kopiëren.
- U kunt ook de volgende hulpprogram ma's gebruiken om een pijp lijn te maken: **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager sjabloon**, **.net API** en **rest API**. Zie [zelf studie Kopieer activiteit](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) voor stapsgewijze instructies voor het maken van een pijp lijn met een Kopieer activiteit.

Ongeacht of u de hulpprogram ma's of Api's gebruikt, voert u de volgende stappen uit om een pijp lijn te maken waarmee gegevens uit een brongegevens archief naar een Sink-gegevens archief worden verplaatst:

1. Maak **gekoppelde services** om invoer-en uitvoer gegevens archieven te koppelen aan uw Data Factory.
2. Gegevens **sets** maken om invoer-en uitvoer gegevens voor de Kopieer bewerking weer te geven.
3. Maak een **pijp lijn** met een Kopieer activiteit die een gegevensset als invoer en een gegevensset als uitvoer gebruikt.

Wanneer u de wizard gebruikt, worden automatisch JSON-definities voor deze Data Factory entiteiten (gekoppelde services, gegevens sets en de pijp lijn) gemaakt. Wanneer u hulpprogram ma's/Api's (met uitzonde ring van .NET API) gebruikt, definieert u deze Data Factory entiteiten met behulp van de JSON-indeling. Zie voor een voor beeld met JSON-definities voor Data Factory entiteiten die worden gebruikt voor het kopiëren van gegevens uit een on-premises Cassandra-gegevens opslag [JSON-voor beeld: gegevens kopiëren van Cassandra naar Azure Blob](#json-example-copy-data-from-cassandra-to-azure-blob) in het gedeelte van dit artikel.

De volgende secties bevatten informatie over de JSON-eigenschappen die worden gebruikt voor het definiëren van Data Factory entiteiten die specifiek zijn voor een Cassandra-gegevens archief:

## <a name="linked-service-properties"></a>Eigenschappen van gekoppelde service
In de volgende tabel vindt u een beschrijving van de JSON-elementen die specifiek zijn voor Cassandra gekoppelde service.

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| type |De eigenschap type moet worden ingesteld op: **OnPremisesCassandra** |Yes |
| host |Een of meer IP-adressen of hostnamen van Cassandra-servers.<br/><br/>Geef een door komma's gescheiden lijst met IP-adressen of hostnamen op om gelijktijdig verbinding te maken met alle servers. |Yes |
| poort |De TCP-poort die de Cassandra-server gebruikt om te Luis teren naar client verbindingen. |Nee, standaard waarde: 9042 |
| authenticationType |Basic of anoniem |Yes |
| gebruikersnaam |Geef de gebruikers naam voor het gebruikers account op. |Ja, als authenticationType is ingesteld op Basic. |
| wachtwoord |Geef het wacht woord voor het gebruikers account op. |Ja, als authenticationType is ingesteld op Basic. |
| gatewayName |De naam van de gateway die wordt gebruikt om verbinding te maken met de on-premises Cassandra-data base. |Yes |
| encryptedCredential |Referentie versleuteld door de gateway. |No |

>[!NOTE]
>Momenteel wordt geen verbinding met Cassandra met behulp van TLS ondersteund.

## <a name="dataset-properties"></a>Eigenschappen van gegevensset
Zie het artikel [gegevens sets maken](data-factory-create-datasets.md) voor een volledige lijst met secties & eigenschappen die beschikbaar zijn voor het definiëren van gegevens sets. Secties zoals structuur, Beschik baarheid en beleid van een gegevensset-JSON zijn vergelijkbaar voor alle typen gegevens sets (Azure SQL, Azure Blob, Azure Table, enzovoort).

De sectie **typeProperties** verschilt voor elk type gegevensset en bevat informatie over de locatie van de gegevens in het gegevens archief. De sectie typeProperties voor de gegevensset van het type **CassandraTable** heeft de volgende eigenschappen:

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| Keys Pace |De naam van de spatie of het schema in de Cassandra-data base. |Ja (als de **query** voor **CassandraSource** niet is gedefinieerd). |
| tableName |De naam van de tabel in de Cassandra-data base. |Ja (als de **query** voor **CassandraSource** niet is gedefinieerd). |

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit
Zie het artikel [pijp lijnen maken](data-factory-create-pipelines.md) voor een volledige lijst met secties & eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. Eigenschappen zoals naam, beschrijving, invoer-en uitvoer tabellen en beleid zijn beschikbaar voor alle typen activiteiten.

Terwijl de eigenschappen die beschikbaar zijn in de sectie typeProperties van de activiteit, verschillen per activiteitstype. Voor kopieer activiteiten zijn ze afhankelijk van de typen bronnen en Sinks.

Wanneer bron van het type **CassandraSource** is, zijn de volgende eigenschappen beschikbaar in de sectie typeProperties:

| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| query |Gebruik de aangepaste query om gegevens te lezen. |SQL-92-query of CQL-query. Zie [CQL Reference](https://docs.datastax.com/en/cql/3.1/cql/cql_reference/cqlReferenceTOC.html)(Engelstalig). <br/><br/>Wanneer u SQL query gebruikt, geeft u de naam van de **spatie op. tabel naam** voor de tabel die u wilt doorzoeken. |Nee (als TableName en Keys op gegevensset zijn gedefinieerd). |
| consistencyLevel |Het consistentie niveau geeft aan hoeveel replica's moeten reageren op een lees aanvraag voordat gegevens worden geretourneerd naar de client toepassing. Cassandra controleert het opgegeven aantal replica's voor gegevens om te voldoen aan de Lees aanvraag. |EEN, TWEE, DRIE, QUORUM, ALLE, LOCAL_QUORUM, EACH_QUORUM LOCAL_ONE. Zie [gegevens consistentie configureren](https://docs.datastax.com/en/cassandra/2.1/cassandra/dml/dml_config_consistency_c.html) voor meer informatie. |Nee. De standaard waarde is één. |

## <a name="json-example-copy-data-from-cassandra-to-azure-blob"></a>JSON-voor beeld: gegevens kopiëren van Cassandra naar Azure Blob
Dit voor beeld bevat een voor beeld van JSON-definities die u kunt gebruiken om een pijp lijn te maken met behulp van [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) of [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Hierin ziet u hoe u gegevens kopieert van een on-premises Cassandra-Data Base naar een Azure-Blob Storage. Gegevens kunnen echter worden gekopieerd naar de [hier](data-factory-data-movement-activities.md#supported-data-stores-and-formats) opgegeven sinks met behulp van de Kopieer activiteit in azure Data Factory.

> [!IMPORTANT]
> Dit voor beeld bevat JSON-fragmenten. Het bevat geen stapsgewijze instructies voor het maken van de data factory. Zie [gegevens verplaatsen tussen on-premises locaties en een Cloud](data-factory-move-data-between-onprem-and-cloud.md) artikel voor stapsgewijze instructies.

Het voor beeld heeft de volgende data factory entiteiten:

* Een gekoppelde service van het type [OnPremisesCassandra](#linked-service-properties).
* Een gekoppelde service van het type [opslag](data-factory-azure-blob-connector.md#linked-service-properties).
* Een invoer- [gegevensset](data-factory-create-datasets.md) van het type [CassandraTable](#dataset-properties).
* Een uitvoer [gegevensset](data-factory-create-datasets.md) van het type [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* Een [pijp lijn](data-factory-create-pipelines.md) met een Kopieer activiteit die gebruikmaakt van [CassandraSource](#copy-activity-properties) en [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

**Gekoppelde Cassandra-service:**

In dit voor beeld wordt de gekoppelde **Cassandra** -service gebruikt. Zie [Cassandra sectie gekoppelde service](#linked-service-properties) voor de eigenschappen die worden ondersteund door deze gekoppelde service.

```json
{
    "name": "CassandraLinkedService",
    "properties":
    {
        "type": "OnPremisesCassandra",
        "typeProperties":
        {
            "authenticationType": "Basic",
            "host": "mycassandraserver",
            "port": 9042,
            "username": "user",
            "password": "password",
            "gatewayName": "mygateway"
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

**Cassandra invoer gegevensset:**

```json
{
    "name": "CassandraInput",
    "properties": {
        "linkedServiceName": "CassandraLinkedService",
        "type": "CassandraTable",
        "typeProperties": {
            "tableName": "mytable",
            "keySpace": "mykeyspace"
        },
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

Als u **extern** instelt op **True** , informeert de Data Factory-service dat de gegevensset zich buiten de Data Factory bevindt en die niet wordt geproduceerd door een activiteit in de Data Factory.

**Azure Blob-uitvoer gegevensset:**

Gegevens worden elk uur naar een nieuwe BLOB geschreven (frequentie: uur, interval: 1).

```json
{
    "name": "AzureBlobOutput",
    "properties":
    {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties":
        {
            "folderPath": "adfgetstarted/fromcassandra"
        },
        "availability":
        {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Kopieer activiteit in een pijp lijn met Cassandra-bron en BLOB-Sink:**

De pijp lijn bevat een Kopieer activiteit die is geconfigureerd voor het gebruik van de invoer-en uitvoer gegevens sets en die is gepland om elk uur te worden uitgevoerd. In de JSON-definitie van de pijp lijn is het **bron** type ingesteld op **CassandraSource** en het **sink** -type is ingesteld op **BlobSink**.

Zie [Eigenschappen van het type RelationalSource](#copy-activity-properties) voor de lijst met eigenschappen die worden ondersteund door de RelationalSource.

```json
{
    "name":"SamplePipeline",
    "properties":{
        "start":"2016-06-01T18:00:00",
        "end":"2016-06-01T19:00:00",
        "description":"pipeline with copy activity",
        "activities":[
        {
            "name": "CassandraToAzureBlob",
            "description": "Copy from Cassandra to an Azure blob",
            "type": "Copy",
            "inputs": [
            {
                "name": "CassandraInput"
            }
            ],
            "outputs": [
            {
                "name": "AzureBlobOutput"
            }
            ],
            "typeProperties": {
                "source": {
                    "type": "CassandraSource",
                    "query": "select id, firstname, lastname from mykeyspace.mytable"

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

### <a name="type-mapping-for-cassandra"></a>Type toewijzing voor Cassandra
| Type Cassandra | Op .NET gebaseerd type |
| --- | --- |
| ASCII |Tekenreeks |
| BIGINT |Int64 |
| BLOBCACHE |Byte [] |
| BOOLEAN |Booleaans |
| DECIMAL |Decimaal |
| DOUBLE |Dubbel |
| FLOAT |Enkelvoudig |
| INET |Tekenreeks |
| INT |Int32 |
| TEXT |Tekenreeks |
| TIMESTAMP |DateTime |
| TIMEUUID |Guid |
| UUID |Guid |
| VARCHAR |Tekenreeks |
| VARINT |Decimaal |

> [!NOTE]
> Voor verzamelings typen (kaart, set, lijst, enzovoort) raadpleegt u [werken met Cassandra-verzamelings typen met behulp](#work-with-collections-using-virtual-table) van de sectie virtuele tabel.
>
> Door de gebruiker gedefinieerde typen worden niet ondersteund.
>
> De lengte van de binaire kolom-en teken reeks kolom lengten kan niet groter zijn dan 4000.
>
>

## <a name="work-with-collections-using-virtual-table"></a>Werken met verzamelingen met virtuele tabel
Azure Data Factory maakt gebruik van een ingebouwd ODBC-stuur programma om verbinding te maken met gegevens uit uw Cassandra-data base en deze te kopiëren. Voor verzamelings typen, waaronder kaart, set en lijst, worden de gegevens in de bijbehorende virtuele tabellen opnieuw genormaliseerd door het stuur programma. Met name als een tabel verzamelings kolommen bevat, worden de volgende virtuele tabellen door het stuur programma gegenereerd:

* Een **basis tabel** met dezelfde gegevens als de echte tabel, met uitzonde ring van de verzamelings kolommen. Voor de basis tabel wordt dezelfde naam gebruikt als voor de echte tabel die deze vertegenwoordigt.
* Een **virtuele tabel** voor elke verzamelings kolom, waarmee de geneste gegevens worden uitgevouwen. De virtuele tabellen die verzamelingen vertegenwoordigen, krijgen een naam met de naam van de tabel Real, een scheidings teken '*VT*' en de naam van de kolom.

Virtuele tabellen verwijzen naar de gegevens in de tabel Real, waardoor het stuur programma toegang kan krijgen tot de Gedenormaliseerde gegevens. Zie de sectie voor beeld voor meer informatie. U hebt toegang tot de inhoud van Cassandra-verzamelingen door de virtuele tabellen te doorzoeken en aan elkaar te koppelen.

U kunt de [wizard kopiëren](data-factory-data-movement-activities.md#create-a-pipeline-with-copy-activity) gebruiken om de lijst met tabellen intuïtief weer te geven in de Cassandra-data base, met inbegrip van de virtuele tabellen en een voor beeld van de gegevens in. U kunt ook een query in de wizard kopiëren maken en valideren om het resultaat te bekijken.

### <a name="example"></a>Voorbeeld
Bijvoorbeeld: het volgende ' ExampleTable ' is een Cassandra-database tabel met een primaire-sleutel kolom met een geheel getal met de naam ' pk_int ', een tekst kolom met de naam value, een lijst kolom, een kolom met een kaart en een set-kolom (met de naam ' Stringset ').

| pk_int | Waarde | Lijst | Kaart | Stringset |
| --- | --- | --- | --- | --- |
| 1 |' voorbeeld waarde 1 ' |["1", "2", "3"] |{"S1": "a", "S2": "b"} |{"A", "B", "C"} |
| 3 |"voorbeeld waarde 3" |[' 100 ', ' 101 ', ' 102 ', ' 105 '] |{"S1": "t"} |{"A", "E"} |

Het stuur programma genereert meerdere virtuele tabellen om deze afzonderlijke tabel weer te geven. De refererende-sleutel kolommen in de virtuele tabellen verwijzen naar de primaire-sleutel kolommen in de tabel Real en geven aan in welke tabel de virtuele tabelrij overeenkomt met.

De eerste virtuele tabel is de basis tabel met de naam ' ExampleTable ' wordt weer gegeven in de volgende tabel. De basis tabel bevat dezelfde gegevens als de oorspronkelijke database tabel, met uitzonde ring van de verzamelingen, die uit deze tabel worden wegge laten en worden uitgevouwen in andere virtuele tabellen.

| pk_int | Waarde |
| --- | --- |
| 1 |' voorbeeld waarde 1 ' |
| 3 |"voorbeeld waarde 3" |

In de volgende tabellen ziet u de virtuele tabellen die de gegevens van de kolommen lijst, kaart en Reeksset opnieuw normaliseren. De kolommen met namen die eindigen op ' _index ' of ' _key ' geven de positie aan van de gegevens in de oorspronkelijke lijst of kaart. De kolommen met namen die eindigen op ' _value ' bevatten de uitgevouwen gegevens uit de verzameling.

#### <a name="table-exampletable_vt_list"></a>Tabel "ExampleTable_vt_List":
| pk_int | List_index | List_value |
| --- | --- | --- |
| 1 |0 |1 |
| 1 |1 |2 |
| 1 |2 |3 |
| 3 |0 |100 |
| 3 |1 |101 |
| 3 |2 |102 |
| 3 |3 |103 |

#### <a name="table-exampletable_vt_map"></a>Tabel "ExampleTable_vt_Map":
| pk_int | Map_key | Map_value |
| --- | --- | --- |
| 1 |S1 |A |
| 1 |S2 |b |
| 3 |S1 |t |

#### <a name="table-exampletable_vt_stringset"></a>Tabel "ExampleTable_vt_StringSet":
| pk_int | StringSet_value |
| --- | --- |
| 1 |A |
| 1 |B |
| 1 |C |
| 3 |A |
| 3 |E |

## <a name="map-source-to-sink-columns"></a>Bron toewijzen aan Sink-kolommen
Zie [DataSet-kolommen toewijzen in azure Data Factory](data-factory-map-columns.md)voor meer informatie over het toewijzen van kolommen in de bron-gegevensset aan kolommen in Sink-gegevensset.

## <a name="repeatable-read-from-relational-sources"></a>Herhaal bare Lees bewerking van relationele bronnen
Houd bij het kopiëren van gegevens uit relationele gegevens archieven de Herhaal baarheid in de hand om onbedoelde resultaten te voor komen. In Azure Data Factory kunt u een segment hand matig opnieuw uitvoeren. U kunt ook beleid voor opnieuw proberen voor een gegevensset configureren zodat een segment opnieuw wordt uitgevoerd wanneer er een fout optreedt. Wanneer een segment op een van beide manieren opnieuw wordt uitgevoerd, moet u ervoor zorgen dat dezelfde gegevens worden gelezen, ongeacht het aantal keren dat een segment wordt gestart. Zie [Herhaal bare Lees bewerking van relationele bronnen](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Prestaties en afstemming
Zie [Kopieer activiteit prestaties & afstemmings handleiding](data-factory-copy-activity-performance.md) voor meer informatie over de belangrijkste factoren die invloed hebben op de prestaties van het verplaatsen van gegevens (Kopieer activiteit) in azure Data Factory en verschillende manieren om deze te optimaliseren.
