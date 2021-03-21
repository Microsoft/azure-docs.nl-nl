---
title: Gegevens van DB2 kopiëren met behulp van Azure Data Factory
description: Meer informatie over het kopiëren van gegevens van DB2 naar ondersteunde Sink-gegevens archieven met behulp van een Kopieer activiteit in een Azure Data Factory-pijp lijn.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: jingwang
ms.openlocfilehash: 642f12386a7695e026eb0c30016acf6f53fc9e95
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100381117"
---
# <a name="copy-data-from-db2-by-using-azure-data-factory"></a>Gegevens van DB2 kopiëren met behulp van Azure Data Factory
> [!div class="op_single_selector" title1="Selecteer de versie van de Data Factory-service die u gebruikt:"]
> * [Versie 1:](v1/data-factory-onprem-db2-connector.md)
> * [Huidige versie](connector-db2.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In dit artikel wordt beschreven hoe u de Kopieer activiteit in Azure Data Factory kunt gebruiken om gegevens uit een DB2-Data Base te kopiëren. Het is gebaseerd op het artikel overzicht van de [Kopieer activiteit](copy-activity-overview.md) . Dit geeft een algemeen overzicht van de Kopieer activiteit.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze DB2 Data Base-connector wordt ondersteund voor de volgende activiteiten:

- [Kopieer activiteit](copy-activity-overview.md) met een [ondersteunde bron/Sink-matrix](copy-activity-overview.md)
- [Activiteit Lookup](control-flow-lookup-activity.md)

U kunt gegevens uit de DB2-Data Base kopiëren naar elk ondersteund Sink-gegevens archief. Zie de tabel [ondersteunde gegevens archieven](copy-activity-overview.md#supported-data-stores-and-formats) voor een lijst met gegevens archieven die worden ondersteund als bron/sinks door de Kopieer activiteit.

In het bijzonder ondersteunt deze DB2-connector de volgende IBM DB2-platforms en-versies met distributed Relationed data base Architecture (DRDA) SQL Access Manager (SQLAM) versie 9, 10 en 11.  Er wordt gebruikgemaakt van het DDM/DRDA-protocol.

* IBM DB2 voor z/OS 12,1
* IBM DB2 voor z/OS 11,1
* IBM DB2 voor z/OS 10,1
* IBM DB2 voor i 7,3
* IBM DB2 voor i 7,2
* IBM DB2 voor i 7,1
* IBM DB2 voor LUW 11
* IBM DB2 voor LUW 10,5
* IBM DB2 voor LUW 10,1

>[!TIP]
>DB2-connector is gebaseerd op Microsoft OLE DB-provider voor DB2. Raadpleeg de [fout codes van de gegevens provider](/host-integration-server/db2oledbv/data-provider-error-codes#drda-protocol-errors)om problemen met de DB2-connector op te lossen.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

De Integration Runtime biedt een ingebouwd DB2-stuur programma. Daarom hoeft u niet hand matig een stuur programma te installeren bij het kopiëren van gegevens uit DB2.

## <a name="getting-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

De volgende secties bevatten informatie over eigenschappen die worden gebruikt voor het definiëren van Data Factory entiteiten die specifiek zijn voor de DB2-connector.

## <a name="linked-service-properties"></a>Eigenschappen van gekoppelde service

De volgende eigenschappen worden ondersteund voor gekoppelde DB2-service:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type moet worden ingesteld op: **Db2** | Ja |
| connectionString | Geef de gegevens op die nodig zijn om verbinding te maken met het DB2-exemplaar.<br/> U kunt ook wacht woord in Azure Key Vault plaatsen en de `password` configuratie uit de Connection String halen. Raadpleeg de volgende voor beelden en [Sla referenties op in azure Key Vault](store-credentials-in-key-vault.md) artikel met meer informatie. | Ja |
| connectVia | Het [Integration runtime](concepts-integration-runtime.md) dat moet worden gebruikt om verbinding te maken met het gegevens archief. Meer informatie vindt u in de sectie [vereisten](#prerequisites) . Als u niets opgeeft, wordt de standaard Azure Integration Runtime gebruikt. |Nee |

Typische eigenschappen in de connection string:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| server |Naam van de DB2-Server. U kunt het poort nummer opgeven na de server naam gescheiden door een dubbele punt, `server:port` bijvoorbeeld.<br>De DB2-connector gebruikt het DDM/DRDA-protocol en maakt standaard gebruik van poort 50000 indien niet opgegeven. De poort die uw specifieke DB2-Data Base gebruikt, kan afwijken van de versie en uw instellingen, bijvoorbeeld voor DB2 LUW de standaard poort is 50000, voor AS400 de standaard poort is 446 of 448 als TLS is ingeschakeld. Raadpleeg de volgende DB2-documenten over de manier waarop de poort wordt geconfigureerd: [DB2 z/OS](https://www.ibm.com/support/knowledgecenter/SSEPGG_11.5.0/com.ibm.db2.luw.qb.dbconn.doc/doc/t0008229.html), [Db2 ISERIES](https://www.ibm.com/support/knowledgecenter/ssw_ibm_i_74/ddp/rbal1ports.htm)en [DB2 LUW](https://www.ibm.com/support/knowledgecenter/en/SSEKCU_1.1.3.0/com.ibm.psc.doc/install/psc_t_install_typical_db2_port.html). |Ja |
| database |Naam van de DB2-Data Base. |Ja |
| authenticationType |Type verificatie dat wordt gebruikt om verbinding te maken met de DB2-Data Base.<br/>Toegestane waarde is: **Basic**. |Ja |
| gebruikersnaam |Geef de gebruikers naam op om verbinding te maken met de DB2-Data Base. |Ja |
| wachtwoord |Geef het wacht woord op voor het gebruikers account dat u hebt opgegeven voor de gebruikers naam. Markeer dit veld als SecureString om het veilig op te slaan in Data Factory, of om te [verwijzen naar een geheim dat is opgeslagen in azure Key Vault](store-credentials-in-key-vault.md). |Ja |
| packageCollection    | Geef op onder waar de benodigde pakketten automatisch worden gemaakt door ADF bij het uitvoeren van query's op de data base. Als deze niet is ingesteld, gebruikt Data Factory de {username} als de standaard waarde. | Nee |
| certificateCommonName | Wanneer u Secure Sockets Layer (SSL) of Transport Layer Security (TLS)-code ring gebruikt, moet u een waarde opgeven voor de algemene naam van het certificaat. | Nee |

> [!TIP]
> Als er een fout bericht wordt weer gegeven met de melding dat `The package corresponding to an SQL statement execution request was not found. SQLSTATE=51002 SQLCODE=-805` de reden is dat er geen pakket is gemaakt voor de gebruiker. Standaard probeert ADF een pakket te maken onder de verzameling met de naam van de gebruiker die u hebt gebruikt voor verbinding met de DB2. Geef de verzamelings eigenschap van het pakket op om aan te geven onder waar u de benodigde pakketten wilt maken bij het uitvoeren van een query op de data base.

**Voorbeeld:**

```json
{
    "name": "Db2LinkedService",
    "properties": {
        "type": "Db2",
        "typeProperties": {
            "connectionString": "server=<server:port>;database=<database>;authenticationType=Basic;username=<username>;password=<password>;packageCollection=<packagecollection>;certificateCommonName=<certname>;"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```
**Voor beeld: wacht woord opslaan in Azure Key Vault**

```json
{
    "name": "Db2LinkedService",
    "properties": {
        "type": "Db2",
        "typeProperties": {
            "connectionString": "server=<server:port>;database=<database>;authenticationType=Basic;username=<username>;packageCollection=<packagecollection>;certificateCommonName=<certname>;",
            "password": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

Als u een gekoppelde DB2-service met de volgende Payload gebruikt, wordt deze nog steeds ondersteund als-is, terwijl u wordt geadviseerd om het nieuwe item te gebruiken.

**Vorige nettolading:**

```json
{
    "name": "Db2LinkedService",
    "properties": {
        "type": "Db2",
        "typeProperties": {
            "server": "<servername:port>",
            "database": "<dbname>",
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

Zie het artikel [gegevens sets](concepts-datasets-linked-services.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevens sets. Deze sectie bevat een lijst met eigenschappen die door de DB2-gegevensset worden ondersteund.

Als u gegevens wilt kopiëren uit DB2, worden de volgende eigenschappen ondersteund:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de gegevensset moet worden ingesteld op: **Db2Table** | Ja |
| schema | De naam van het schema. |Nee (als "query" in activiteit bron is opgegeven)  |
| tabel | De naam van de tabel. |Nee (als "query" in activiteit bron is opgegeven)  |
| tableName | De naam van de tabel met schema. Deze eigenschap wordt ondersteund voor achterwaartse compatibiliteit. Gebruik `schema` en `table` voor nieuwe werk belasting. | Nee (als "query" in activiteit bron is opgegeven) |

**Voorbeeld**

```json
{
    "name": "DB2Dataset",
    "properties":
    {
        "type": "Db2Table",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<DB2 linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

Als u `RelationalTable` getypte gegevensset gebruikt, wordt deze nog steeds ondersteund als-is, terwijl u wordt geadviseerd om het nieuwe item te gebruiken.

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit

Zie het artikel [pijp lijnen](concepts-pipelines-activities.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. Deze sectie bevat een lijst met eigenschappen die worden ondersteund door de DB2-bron.

### <a name="db2-as-source"></a>DB2 als bron

Als u gegevens wilt kopiëren uit DB2, worden de volgende eigenschappen ondersteund in de sectie **bron** van de Kopieer activiteit:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de bron van de Kopieer activiteit moet zijn ingesteld op: **Db2Source** | Ja |
| query | Gebruik de aangepaste SQL-query om gegevens te lezen. Bijvoorbeeld: `"query": "SELECT * FROM \"DB2ADMIN\".\"Customers\""`. | Nee (als ' Tablename ' in gegevensset is opgegeven) |

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyFromDB2",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<DB2 input dataset name>",
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
                "type": "Db2Source",
                "query": "SELECT * FROM \"DB2ADMIN\".\"Customers\""
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Als u `RelationalSource` getypte bron gebruikt, wordt deze nog steeds ondersteund als-is, terwijl u wordt geadviseerd om het nieuwe item te gebruiken.

## <a name="data-type-mapping-for-db2"></a>Toewijzing van gegevens type voor DB2

Bij het kopiëren van gegevens uit de DB2 worden de volgende toewijzingen gebruikt vanuit DB2-gegevens typen voor het Azure Data Factory van tussenliggende gegevens typen. Zie [schema-en gegevens type toewijzingen](copy-activity-schema-and-type-mapping.md) voor meer informatie over hoe kopieer activiteit het bron schema en het gegevens type aan de Sink koppelt.

| DB2-database type | Data Factory-gegevens type interim |
|:--- |:--- |
| BigInt |Int64 |
| Binair |Byte [] |
| Blob |Byte [] |
| Char |Tekenreeks |
| CLOB |Tekenreeks |
| Datum |Datum/tijd |
| DB2DynArray |Tekenreeks |
| DbClob |Tekenreeks |
| Decimaal |Decimaal |
| DecimalFloat |Decimaal |
| Dubbel |Dubbel |
| Float |Dubbel |
| Afbeelding |Tekenreeks |
| Geheel getal |Int32 |
| LongVarBinary |Byte [] |
| LongVarChar |Tekenreeks |
| LongVarGraphic |Tekenreeks |
| Numeriek |Decimaal |
| Realistische |Enkelvoudig |
| SmallInt |Int16 |
| Tijd |TimeSpan |
| Tijdstempel |DateTime |
| VarBinary |Byte [] |
| VarChar |Tekenreeks |
| VarGraphic |Tekenreeks |
| Xml |Byte [] |

## <a name="lookup-activity-properties"></a>Eigenschappen van opzoek activiteit

Controleer de [opzoek activiteit](control-flow-lookup-activity.md)voor meer informatie over de eigenschappen.

## <a name="next-steps"></a>Volgende stappen
Zie [ondersteunde gegevens archieven](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met gegevens archieven die worden ondersteund als bronnen en sinks op basis van de Kopieer activiteit in azure Data Factory.