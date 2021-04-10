---
title: Gegevens kopiëren van Cassandra met behulp van Azure Data Factory
description: Meer informatie over het kopiëren van gegevens uit Cassandra naar ondersteunde Sink-gegevens archieven met behulp van een Kopieer activiteit in een Azure Data Factory-pijp lijn.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 08/12/2019
ms.author: jingwang
ms.openlocfilehash: a3cd3c3ae28ae302e9469a71d00054152a9b5fb5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100383701"
---
# <a name="copy-data-from-cassandra-using-azure-data-factory"></a>Gegevens kopiëren van Cassandra met behulp van Azure Data Factory
> [!div class="op_single_selector" title1="Selecteer de versie van de Data Factory-service die u gebruikt:"]
> * [Versie 1:](v1/data-factory-onprem-cassandra-connector.md)
> * [Huidige versie](connector-cassandra.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In dit artikel wordt beschreven hoe u de Kopieer activiteit in Azure Data Factory kunt gebruiken om gegevens uit een Cassandra-data base te kopiëren. Het is gebaseerd op het artikel overzicht van de [Kopieer activiteit](copy-activity-overview.md) . Dit geeft een algemeen overzicht van de Kopieer activiteit.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze Cassandra-connector wordt ondersteund voor de volgende activiteiten:

- [Kopieer activiteit](copy-activity-overview.md) met een [ondersteunde bron/Sink-matrix](copy-activity-overview.md)
- [Activiteit Lookup](control-flow-lookup-activity.md)

U kunt gegevens uit de Cassandra-data base kopiëren naar elk ondersteund Sink-gegevens archief. Zie de tabel [ondersteunde gegevens archieven](copy-activity-overview.md#supported-data-stores-and-formats) voor een lijst met gegevens archieven die worden ondersteund als bron/sinks door de Kopieer activiteit.

Deze Cassandra-connector ondersteunt met name:

- Cassandra **versie 2. x en 3. x**.
- Kopiëren van gegevens met behulp van **eenvoudige** of **anonieme** verificatie.

>[!NOTE]
>Voor activiteiten die worden uitgevoerd op zelf-hostende Integration Runtime, wordt Cassandra 3. x ondersteund, aangezien IR-versie 3,7 en hoger.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

De Integration Runtime biedt een ingebouwd Cassandra-stuur programma, dus u hoeft niet hand matig een stuur programma te installeren bij het kopiëren van gegevens van/naar Cassandra.

## <a name="getting-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

De volgende secties bevatten informatie over eigenschappen die worden gebruikt voor het definiëren van Data Factory-entiteiten die specifiek zijn voor Cassandra-connector.

## <a name="linked-service-properties"></a>Eigenschappen van gekoppelde service

De volgende eigenschappen worden ondersteund voor Cassandra gekoppelde service:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type |De eigenschap type moet worden ingesteld op: **Cassandra** |Yes |
| host |Een of meer IP-adressen of hostnamen van Cassandra-servers.<br/>Geef een door komma's gescheiden lijst met IP-adressen of hostnamen op om gelijktijdig verbinding te maken met alle servers. |Yes |
| poort |De TCP-poort die de Cassandra-server gebruikt om te Luis teren naar client verbindingen. |Nee (de standaard waarde is 9042) |
| authenticationType | Type verificatie dat wordt gebruikt om verbinding te maken met de Cassandra-data base.<br/>Toegestane waarden zijn: **Basic** en **Anonymous**. |Yes |
| gebruikersnaam |Geef de gebruikers naam voor het gebruikers account op. |Ja, als authenticationType is ingesteld op Basic. |
| wachtwoord |Geef het wacht woord voor het gebruikers account op. Markeer dit veld als SecureString om het veilig op te slaan in Data Factory, of om te [verwijzen naar een geheim dat is opgeslagen in azure Key Vault](store-credentials-in-key-vault.md). |Ja, als authenticationType is ingesteld op Basic. |
| connectVia | Het [Integration runtime](concepts-integration-runtime.md) dat moet worden gebruikt om verbinding te maken met het gegevens archief. Meer informatie vindt u in de sectie [vereisten](#prerequisites) . Als u niets opgeeft, wordt de standaard Azure Integration Runtime gebruikt. |No |

>[!NOTE]
>Momenteel wordt geen verbinding met Cassandra met behulp van TLS ondersteund.

**Voorbeeld:**

```json
{
    "name": "CassandraLinkedService",
    "properties": {
        "type": "Cassandra",
        "typeProperties": {
            "host": "<host>",
            "authenticationType": "Basic",
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
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

Zie het artikel [gegevens sets](concepts-datasets-linked-services.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevens sets. Deze sectie bevat een lijst met eigenschappen die worden ondersteund door de Cassandra-gegevensset.

Als u gegevens van Cassandra wilt kopiëren, stelt u de eigenschap type van de gegevensset in op **CassandraTable**. De volgende eigenschappen worden ondersteund:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de gegevensset moet worden ingesteld op: **CassandraTable** | Yes |
| Keys Pace |De naam van de spatie of het schema in de Cassandra-data base. |Nee (als "query" voor "CassandraSource" is opgegeven) |
| tableName |De naam van de tabel in de Cassandra-data base. |Nee (als "query" voor "CassandraSource" is opgegeven) |

**Voorbeeld:**

```json
{
    "name": "CassandraDataset",
    "properties": {
        "type": "CassandraTable",
        "typeProperties": {
            "keySpace": "<keyspace name>",
            "tableName": "<table name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Cassandra linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit


Zie het artikel [pijp lijnen](concepts-pipelines-activities.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. Deze sectie bevat een lijst met eigenschappen die door Cassandra-bron worden ondersteund.

### <a name="cassandra-as-source"></a>Cassandra als bron

Als u gegevens wilt kopiëren uit Cassandra, stelt u het bron type in de Kopieer activiteit in op **CassandraSource**. De volgende eigenschappen worden ondersteund in de sectie **bron** van de Kopieer activiteit:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de bron van de Kopieer activiteit moet zijn ingesteld op: **CassandraSource** | Yes |
| query |Gebruik de aangepaste query om gegevens te lezen. SQL-92-query of CQL-query. Zie [CQL Reference](https://docs.datastax.com/en/cql/3.1/cql/cql_reference/cqlReferenceTOC.html)(Engelstalig). <br/><br/>Wanneer u SQL query gebruikt, geeft u de naam van de **spatie op. tabel naam** voor de tabel die u wilt doorzoeken. |Nee (als "TableName" en "Keys" in de gegevensset zijn opgegeven). |
| consistencyLevel |Het consistentie niveau geeft aan hoeveel replica's moeten reageren op een lees aanvraag voordat gegevens worden geretourneerd naar de client toepassing. Cassandra controleert het opgegeven aantal replica's voor gegevens om te voldoen aan de Lees aanvraag. Zie [gegevens consistentie configureren](https://docs.datastax.com/en/cassandra/2.1/cassandra/dml/dml_config_consistency_c.html) voor meer informatie.<br/><br/>Toegestane waarden zijn: **een**, **twee**, **drie**, **quorum**, **alle**, **LOCAL_QUORUM**, **EACH_QUORUM** en **LOCAL_ONE**. |Nee (standaard instelling `ONE` ) |

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyFromCassandra",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Cassandra input dataset name>",
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
                "type": "CassandraSource",
                "query": "select id, firstname, lastname from mykeyspace.mytable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-cassandra"></a>Toewijzing van gegevens type voor Cassandra

Bij het kopiëren van gegevens uit Cassandra worden de volgende toewijzingen gebruikt van Cassandra-gegevens typen om tussenliggende gegevens typen te Azure Data Factory. Zie [schema-en gegevens type toewijzingen](copy-activity-schema-and-type-mapping.md) voor meer informatie over hoe kopieer activiteit het bron schema en het gegevens type aan de Sink koppelt.

| Cassandra-gegevens type | Data Factory-gegevens type interim |
|:--- |:--- |
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

## <a name="work-with-collections-using-virtual-table"></a>Werken met verzamelingen met virtuele tabel

Azure Data Factory maakt gebruik van een ingebouwd ODBC-stuur programma om verbinding te maken met gegevens uit uw Cassandra-data base en deze te kopiëren. Voor verzamelings typen, waaronder kaart, set en lijst, worden de gegevens in de bijbehorende virtuele tabellen opnieuw genormaliseerd door het stuur programma. Met name als een tabel verzamelings kolommen bevat, worden de volgende virtuele tabellen door het stuur programma gegenereerd:

* Een **basis tabel** met dezelfde gegevens als de echte tabel, met uitzonde ring van de verzamelings kolommen. Voor de basis tabel wordt dezelfde naam gebruikt als voor de echte tabel die deze vertegenwoordigt.
* Een **virtuele tabel** voor elke verzamelings kolom, waarmee de geneste gegevens worden uitgevouwen. De virtuele tabellen die verzamelingen vertegenwoordigen, krijgen een naam met de naam van de tabel Real, een scheidings teken '*VT*' en de naam van de kolom.

Virtuele tabellen verwijzen naar de gegevens in de tabel Real, waardoor het stuur programma toegang kan krijgen tot de Gedenormaliseerde gegevens. Zie de sectie voor beeld voor meer informatie. U hebt toegang tot de inhoud van Cassandra-verzamelingen door de virtuele tabellen te doorzoeken en aan elkaar te koppelen.

### <a name="example"></a>Voorbeeld

Bijvoorbeeld: het volgende ' ExampleTable ' is een Cassandra-database tabel met een primaire-sleutel kolom met een geheel getal met de naam ' pk_int ', een tekst kolom met de naam value, een lijst kolom, een kolom met een kaart en een set-kolom (met de naam ' Stringset ').

| pk_int | Waarde | Lijst | Kaart | Stringset |
| --- | --- | --- | --- | --- |
| 1 |' voorbeeld waarde 1 ' |["1", "2", "3"] |{"S1": "a", "S2": "b"} |{"A", "B", "C"} |
| 3 |"voorbeeld waarde 3" |[' 100 ', ' 101 ', ' 102 ', ' 105 '] |{"S1": "t"} |{"A", "E"} |

Het stuur programma genereert meerdere virtuele tabellen om deze afzonderlijke tabel weer te geven. De refererende-sleutel kolommen in de virtuele tabellen verwijzen naar de primaire-sleutel kolommen in de tabel Real en geven aan in welke tabel de virtuele tabelrij overeenkomt met.

De eerste virtuele tabel is de basis tabel met de naam ' ExampleTable ' wordt weer gegeven in de volgende tabel: 

| pk_int | Waarde |
| --- | --- |
| 1 |' voorbeeld waarde 1 ' |
| 3 |"voorbeeld waarde 3" |

De basis tabel bevat dezelfde gegevens als de oorspronkelijke database tabel, met uitzonde ring van de verzamelingen, die uit deze tabel worden wegge laten en worden uitgevouwen in andere virtuele tabellen.

In de volgende tabellen ziet u de virtuele tabellen die de gegevens van de kolommen lijst, kaart en Reeksset opnieuw normaliseren. De kolommen met namen die eindigen op ' _index ' of ' _key ' geven de positie aan van de gegevens in de oorspronkelijke lijst of kaart. De kolommen met namen die eindigen op ' _value ' bevatten de uitgevouwen gegevens uit de verzameling.

**Tabel "ExampleTable_vt_List":**

| pk_int | List_index | List_value |
| --- | --- | --- |
| 1 |0 |1 |
| 1 |1 |2 |
| 1 |2 |3 |
| 3 |0 |100 |
| 3 |1 |101 |
| 3 |2 |102 |
| 3 |3 |103 |

**Tabel "ExampleTable_vt_Map":**

| pk_int | Map_key | Map_value |
| --- | --- | --- |
| 1 |S1 |A |
| 1 |S2 |b |
| 3 |S1 |t |

**Tabel "ExampleTable_vt_StringSet":**

| pk_int | StringSet_value |
| --- | --- |
| 1 |A |
| 1 |B |
| 1 |C |
| 3 |A |
| 3 |E |

## <a name="lookup-activity-properties"></a>Eigenschappen van opzoek activiteit

Controleer de [opzoek activiteit](control-flow-lookup-activity.md)voor meer informatie over de eigenschappen.

## <a name="next-steps"></a>Volgende stappen
Zie [ondersteunde gegevens archieven](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met gegevens archieven die worden ondersteund als bronnen en sinks op basis van de Kopieer activiteit in azure Data Factory.
