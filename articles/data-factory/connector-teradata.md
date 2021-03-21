---
title: Gegevens van Teradata-Vantage kopiëren met behulp van Azure Data Factory
description: Met de Teradata-connector van de Data Factory-service kunt u gegevens kopiëren van een Teradata-Vantage naar gegevens archieven die door Data Factory worden ondersteund als Sinks.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/22/2021
ms.author: jingwang
ms.openlocfilehash: c65b295dca16a3453b2ee5472b9a3ee5e8d62df7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100381763"
---
# <a name="copy-data-from-teradata-vantage-by-using-azure-data-factory"></a>Gegevens van Teradata-Vantage kopiëren met behulp van Azure Data Factory

> [!div class="op_single_selector" title1="Selecteer de versie van de Data Factory-service die u gebruikt:"]
>
> * [Versie 1:](v1/data-factory-onprem-teradata-connector.md)
> * [Huidige versie](connector-teradata.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In dit artikel wordt beschreven hoe u de Kopieer activiteit in Azure Data Factory kunt gebruiken om gegevens uit Teradata Vantage te kopiëren. Het is gebaseerd op het overzicht van de [Kopieer activiteit](copy-activity-overview.md).

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze Teradata-connector wordt ondersteund voor de volgende activiteiten:

- [Kopieer activiteit](copy-activity-overview.md) met een [ondersteunde bron/Sink-matrix](copy-activity-overview.md)
- [Activiteit Lookup](control-flow-lookup-activity.md)

U kunt gegevens van Teradata-Vantage kopiëren naar elk ondersteund Sink-gegevens archief. Zie de tabel [ondersteunde gegevens archieven](copy-activity-overview.md#supported-data-stores-and-formats) voor een lijst met gegevens archieven die worden ondersteund als bron/sinks door de Kopieer activiteit.

Deze Teradata-connector ondersteunt met name:

- Teradata- **versie 14,10, 15,0, 15,10, 16,0, 16,10 en 16,20**.
- Kopiëren van gegevens met behulp van **basis**-, **Windows**-of **LDAP** -verificatie.
- Parallelle kopieën van een Teradata-bron. Zie de sectie [parallelle kopie van Teradata](#parallel-copy-from-teradata) voor meer informatie.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

Als u een zelf-hosted Integration Runtime gebruikt, ziet u hier een ingebouwd Teradata-stuur programma vanaf versie 3,18. U hoeft geen stuur Programma's hand matig te installeren. Voor het stuur programma is ' Visual C++ Redistributable 2012 update 4 ' op de zelf-hostende Integration runtime-computer vereist. Als u deze nog niet hebt geïnstalleerd, downloadt u deze [hier](https://www.microsoft.com/en-sg/download/details.aspx?id=30679).

## <a name="getting-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

De volgende secties bevatten informatie over eigenschappen die worden gebruikt voor het definiëren van Data Factory entiteiten die specifiek zijn voor de Teradata-connector.

## <a name="linked-service-properties"></a>Eigenschappen van gekoppelde service

De gekoppelde Teradata-service ondersteunt de volgende eigenschappen:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type moet worden ingesteld op **Teradata**. | Ja |
| connectionString | Hiermee geeft u de gegevens op die nodig zijn om verbinding te maken met het Teradata-exemplaar. Raadpleeg de volgende voor beelden.<br/>U kunt ook een wacht woord in Azure Key Vault plaatsen en de `password` configuratie uit de Connection String halen. Raadpleeg [referenties opslaan in azure Key Vault](store-credentials-in-key-vault.md) met meer informatie. | Ja |
| gebruikersnaam | Geef een gebruikers naam op om verbinding te maken met Teradata. Van toepassing wanneer u Windows-verificatie gebruikt. | Nee |
| wachtwoord | Geef een wacht woord op voor het gebruikers account dat u hebt opgegeven voor de gebruikers naam. U kunt er ook voor kiezen om te [verwijzen naar een geheim dat is opgeslagen in azure Key Vault](store-credentials-in-key-vault.md). <br>Is van toepassing wanneer u Windows-verificatie gebruikt of een verwijzing naar een wacht woord in Key Vault voor basis verificatie. | Nee |
| connectVia | Het [Integration runtime](concepts-integration-runtime.md) dat moet worden gebruikt om verbinding te maken met het gegevens archief. Meer informatie vindt u in de sectie [vereisten](#prerequisites) . Als u niets opgeeft, wordt de standaard Azure Integration Runtime gebruikt. |Nee |

Meer verbindings eigenschappen die u in connection string per case kunt instellen:

| Eigenschap | Beschrijving | Standaardwaarde |
|:--- |:--- |:--- |
| TdmstPortNumber | Het nummer van de poort die wordt gebruikt voor toegang tot de Teradata-data base.<br>Wijzig deze waarde niet, tenzij dit door de technische ondersteuning wordt gevraagd. | 1025 |
| UseDataEncryption | Hiermee geeft u op of alle communicatie met de Teradata-data base moet worden versleuteld. Toegestane waarden zijn 0 of 1.<br><br/>- **0 (uitgeschakeld, standaard instelling)**: alleen verificatie-informatie wordt versleuteld.<br/>- **1 (ingeschakeld)**: versleutelt alle gegevens die tussen het stuur programma en de Data Base worden door gegeven. | `0` |
| CharacterSet | De tekenset die moet worden gebruikt voor de sessie. Bijvoorbeeld, `CharacterSet=UTF16` .<br><br/>Deze waarde kan een door de gebruiker gedefinieerde tekenset of een van de volgende vooraf gedefinieerde teken sets zijn: <br/>-ASCII<br/>-UTF8<br/>-UTF16<br/>-LATIN1252_0A<br/>-LATIN9_0A<br/>-LATIN1_0A<br/>-Shift-JIS (Windows, DOS-compatibel, KANJISJIS_0S)<br/>-EUC (compatibel met UNIX, KANJIEC_0U)<br/>-IBM mainframe (KANJIEBCDIC5035_0I)<br/>-KANJI932_1S0<br/>-BIG5 (TCHBIG5_1R0)<br/>-GB (SCHGB2312_1T0)<br/>-SCHINESE936_6R0<br/>-TCHINESE950_8R0<br/>-NetworkKorean (HANGULKSC5601_2R4)<br/>-HANGUL949_7R0<br/>-ARABIC1256_6A0<br/>-CYRILLIC1251_2A0<br/>-HEBREW1255_5A0<br/>-LATIN1250_1A0<br/>-LATIN1254_7A0<br/>-LATIN1258_8A0<br/>-THAI874_4A0 | `ASCII` |
| MaxRespSize |De maximale grootte van de antwoord buffer voor SQL-aanvragen, in kilo bytes (Kb's). Bijvoorbeeld, `MaxRespSize=‭10485760‬` .<br/><br/>Voor de Teradata-Data Base versie 16,00 of hoger is de maximum waarde 7361536. De maximum waarde is 1048576 voor verbindingen die gebruikmaken van eerdere versies. | `65536` |
| Mechanisme | Als u het LDAP-protocol wilt gebruiken om de verbinding te verifiëren, geeft u op `MechanismName=LDAP` . | N.v.t. |

**Voor beeld van basis verificatie**

```json
{
    "name": "TeradataLinkedService",
    "properties": {
        "type": "Teradata",
        "typeProperties": {
            "connectionString": "DBCName=<server>;Uid=<username>;Pwd=<password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Voor beeld van het gebruik van Windows-verificatie**

```json
{
    "name": "TeradataLinkedService",
    "properties": {
        "type": "Teradata",
        "typeProperties": {
            "connectionString": "DBCName=<server>",
            "username": "<username>",
            "password": "<password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Voor beeld van het gebruik van LDAP-verificatie**

```json
{
    "name": "TeradataLinkedService",
    "properties": {
        "type": "Teradata",
        "typeProperties": {
            "connectionString": "DBCName=<server>;MechanismName=LDAP;Uid=<username>;Pwd=<password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

> [!NOTE]
>
> De volgende Payload wordt nog steeds ondersteund. U moet de nieuwe echter gebruiken.

**Vorige nettolading:**

```json
{
    "name": "TeradataLinkedService",
    "properties": {
        "type": "Teradata",
        "typeProperties": {
            "server": "<server>",
            "authenticationType": "<Basic/Windows>",
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

Deze sectie bevat een lijst met eigenschappen die door de Teradata-gegevensset worden ondersteund. Zie [gegevens sets](concepts-datasets-linked-services.md)voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevens sets.

Als u gegevens wilt kopiëren uit Teradata, worden de volgende eigenschappen ondersteund:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de DataSet moet worden ingesteld op `TeradataTable` . | Ja |
| database | De naam van het Teradata-exemplaar. | Nee (als "query" in activiteit bron is opgegeven) |
| tabel | De naam van de tabel in het Teradata-exemplaar. | Nee (als "query" in activiteit bron is opgegeven) |

**Voorbeeld:**

```json
{
    "name": "TeradataDataset",
    "properties": {
        "type": "TeradataTable",
        "typeProperties": {},
        "schema": [],        
        "linkedServiceName": {
            "referenceName": "<Teradata linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

> [!NOTE]
>
> `RelationalTable` type gegevensset wordt nog steeds ondersteund. We raden u echter aan de nieuwe gegevensset te gebruiken.

**Vorige nettolading:**

```json
{
    "name": "TeradataDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<Teradata linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit

Deze sectie bevat een lijst met eigenschappen die worden ondersteund door de Teradata-bron. Voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten, Zie [pijp lijnen](concepts-pipelines-activities.md). 

### <a name="teradata-as-source"></a>Teradata als bron

>[!TIP]
>Als u gegevens op efficiënte wijze wilt laden met behulp van gegevens partitioneren, kunt u meer informatie vinden in het gedeelte [parallel kopiëren vanuit Teradata](#parallel-copy-from-teradata) .

Als u gegevens wilt kopiëren uit Teradata, worden de volgende eigenschappen ondersteund in de sectie **bron** van de Kopieer activiteit:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de bron van de Kopieer activiteit moet worden ingesteld op `TeradataSource` . | Ja |
| query | Gebruik de aangepaste SQL-query om gegevens te lezen. Een voorbeeld is `"SELECT * FROM MyTable"`.<br>Wanneer u gepartitioneerde belasting inschakelt, moet u alle bijbehorende ingebouwde partitie parameters in uw query koppelen. Zie de sectie [parallelle kopie van Teradata](#parallel-copy-from-teradata) voor voor beelden. | Nee (als de tabel in de gegevensset is opgegeven) |
| partitionOptions | Hiermee geeft u de opties voor gegevens partities op die worden gebruikt voor het laden van gegevens uit Teradata. <br>Toegestane waarden zijn: **geen** (standaard), **hash** -en **DynamicRange**.<br>Wanneer een partitie optie is ingeschakeld (dat wil zeggen, niet `None` ), is de mate van parallelle uitvoering om gegevens uit de Teradata gelijktijdig te laden, bepaald door de [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) instelling van de Kopieer activiteit. | Nee |
| partitionSettings | Geef de groep van de instellingen voor het partitioneren van gegevens op. <br>Toep assen als de partitie optie niet is `None` . | Nee |
| partitionColumnName | Geef de naam op van de bron kolom die wordt gebruikt door de bereik partitie of hash-partitie voor parallelle kopieën. Als u niets opgeeft, wordt de primaire index van de tabel automatisch gedetecteerd en gebruikt als de kolom partitie. <br>Toep assen wanneer de partitie optie is `Hash` of `DynamicRange` . Als u een query gebruikt om de bron gegevens, de Hook `?AdfHashPartitionCondition` of de  `?AdfRangePartitionColumnName` component WHERE op te halen. Zie voor beeld in [parallelle kopie van](#parallel-copy-from-teradata) de sectie Teradata. | Nee |
| partitionUpperBound | De maximum waarde van de partitie kolom waaruit de gegevens moeten worden gekopieerd. <br>Toep assen wanneer partitie optie is `DynamicRange` . Als u query gebruikt om bron gegevens op te halen, Hook `?AdfRangePartitionUpbound` in de component WHERE. Zie de sectie [parallelle kopie van Teradata](#parallel-copy-from-teradata) voor een voor beeld. | Nee |
| partitionLowerBound | De minimum waarde van de partitie kolom waaruit de gegevens moeten worden gekopieerd. <br>Toep assen wanneer de partitie optie is `DynamicRange` . Als u een query gebruikt om de bron gegevens op te halen, Hook `?AdfRangePartitionLowbound` in de component WHERE. Zie de sectie [parallelle kopie van Teradata](#parallel-copy-from-teradata) voor een voor beeld. | Nee |

> [!NOTE]
>
> `RelationalSource` type Copy source wordt nog steeds ondersteund, maar biedt geen ondersteuning voor de nieuwe ingebouwde parallelle belasting vanuit Teradata (partitie opties). We raden u echter aan de nieuwe gegevensset te gebruiken.

**Voor beeld: gegevens kopiëren met behulp van een basis query zonder partitie**

```json
"activities":[
    {
        "name": "CopyFromTeradata",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Teradata input dataset name>",
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
                "type": "TeradataSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="parallel-copy-from-teradata"></a>Parallelle kopie van Teradata

De Data Factory Teradata-connector biedt ingebouwde gegevens partities voor het parallel kopiëren van gegevens van Teradata. U kunt opties voor gegevens partities vinden in de **bron** tabel van de Kopieer activiteit.

![Scherm opname van partitie opties](./media/connector-teradata/connector-teradata-partition-options.png)

Wanneer u gepartitioneerde kopie inschakelt, voert Data Factory parallelle query's uit op uw Teradata-bron om gegevens op partities te laden. De parallelle graad wordt bepaald door de [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) instelling in de Kopieer activiteit. Als u bijvoorbeeld hebt ingesteld `parallelCopies` op vier, worden met Data Factory gelijktijdig vier query's gegenereerd en uitgevoerd op basis van uw opgegeven partitie optie en instellingen, en elke query haalt een deel van de gegevens op uit uw Teradata.

U wordt aangeraden om parallelle kopieën in te scha kelen met gegevens partities met name wanneer u grote hoeveel heden gegevens uit uw Teradata laadt. Hieronder vindt u de aanbevolen configuraties voor verschillende scenario's. Bij het kopiëren van gegevens naar gegevens opslag op basis van een bestand, is het opnieuw opdracht om naar een map te schrijven als meerdere bestanden (Geef alleen de mapnaam op). in dat geval is de prestaties beter dan het schrijven naar één bestand.

| Scenario                                                     | Aanbevolen instellingen                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Volledige belasting van een grote tabel.                                   | **Partitie optie**: hash. <br><br/>Tijdens de uitvoering van Data Factory detecteert de primaire index kolom automatisch een hash en kopieert gegevens per partitie. |
| Laad grote hoeveelheid gegevens met behulp van een aangepaste query.                 | **Partitie optie**: hash.<br>**Query**: `SELECT * FROM <TABLENAME> WHERE ?AdfHashPartitionCondition AND <your_additional_where_clause>` .<br>**Partitie kolom**: Geef de kolom op die wordt gebruikt voor het Toep assen van de hash-partitie. Als u dit niet opgeeft, detecteert Data Factory automatisch de PK-kolom van de tabel die u in de Teradata-gegevensset hebt opgegeven.<br><br>Tijdens de uitvoering wordt Data Factory vervangen `?AdfHashPartitionCondition` door de hash-partitie logica en verzonden naar Teradata. |
| Laad een grote hoeveelheid gegevens met behulp van een aangepaste query, met een kolom met gehele getallen met een gelijkmatig gedistribueerde waarde voor bereik partitionering. | **Partitie opties**: partitie met dynamisch bereik.<br>**Query**: `SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>` .<br>**Partitie kolom**: Geef de kolom op die wordt gebruikt om gegevens te partitioneren. U kunt de kolom met het gegevens type geheel getal partitioneren.<br>**Bovengrens van partities** en partities met een **ondergrens**: Geef op of u wilt filteren op de partitie kolom om alleen gegevens tussen het onderste en het bovenste bereik op te halen.<br><br>Tijdens de uitvoering Data Factory vervangt `?AdfRangePartitionColumnName` , `?AdfRangePartitionUpbound` , en `?AdfRangePartitionLowbound` met de werkelijke kolom naam en waardeparen voor elke partitie, en verzonden naar Teradata. <br>Als uw partitie kolom "ID" bijvoorbeeld is ingesteld met de ondergrens als 1 en de bovengrens als 80, met een parallelle kopie ingesteld als 4, Data Factory worden gegevens opgehaald met vier partities. Hun Id's liggen respectievelijk tussen [1, 20], [21, 40], [41, 60] en [61, 80]. |

**Voor beeld: query met hash-partitie**

```json
"source": {
    "type": "TeradataSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfHashPartitionCondition AND <your_additional_where_clause>",
    "partitionOption": "Hash",
    "partitionSettings": {
        "partitionColumnName": "<hash_partition_column_name>"
    }
}
```

**Voor beeld: query met een dynamische bereik partitie**

```json
"source": {
    "type": "TeradataSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>",
    "partitionOption": "DynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<dynamic_range_partition_column_name>",
        "partitionUpperBound": "<upper_value_of_partition_column>",
        "partitionLowerBound": "<lower_value_of_partition_column>"
    }
}
```

## <a name="data-type-mapping-for-teradata"></a>Toewijzing van gegevens type voor Teradata

Wanneer u gegevens uit Teradata kopieert, zijn de volgende toewijzingen van toepassing. Zie [schema en gegevens type toewijzingen](copy-activity-schema-and-type-mapping.md)voor meer informatie over hoe de Kopieer activiteit het bron schema en het gegevens type aan de Sink toewijst.

| Teradata-gegevens type | Data Factory tussentijds gegevens type |
|:--- |:--- |
| BigInt |Int64 |
| Blob |Byte [] |
| Byte |Byte [] |
| ByteInt |Int16 |
| Char |Tekenreeks |
| CLOB |Tekenreeks |
| Date |DateTime |
| Decimaal |Decimaal |
| Dubbel |Dubbel |
| Afbeelding |Wordt niet ondersteund. Expliciete cast Toep assen in bron query. |
| Geheel getal |Int32 |
| Interval dag |Wordt niet ondersteund. Expliciete cast Toep assen in bron query. |
| Interval van dag tot uur |Wordt niet ondersteund. Expliciete cast Toep assen in bron query. |
| Interval van dag tot minuut |Wordt niet ondersteund. Expliciete cast Toep assen in bron query. |
| Interval van dag tot seconde |Wordt niet ondersteund. Expliciete cast Toep assen in bron query. |
| Interval-uur |Wordt niet ondersteund. Expliciete cast Toep assen in bron query. |
| Interval van uur tot minuut |Wordt niet ondersteund. Expliciete cast Toep assen in bron query. |
| Interval per seconde |Wordt niet ondersteund. Expliciete cast Toep assen in bron query. |
| Interval minuut |Wordt niet ondersteund. Expliciete cast Toep assen in bron query. |
| Interval minuut tot seconde |Wordt niet ondersteund. Expliciete cast Toep assen in bron query. |
| Interval maand |Wordt niet ondersteund. Expliciete cast Toep assen in bron query. |
| Interval seconde |Wordt niet ondersteund. Expliciete cast Toep assen in bron query. |
| Interval jaar |Wordt niet ondersteund. Expliciete cast Toep assen in bron query. |
| Interval jaar tot maand |Wordt niet ondersteund. Expliciete cast Toep assen in bron query. |
| Aantal |Dubbel |
| Periode (datum) |Wordt niet ondersteund. Expliciete cast Toep assen in bron query. |
| Periode (tijd) |Wordt niet ondersteund. Expliciete cast Toep assen in bron query. |
| Periode (tijd met tijd zone) |Wordt niet ondersteund. Expliciete cast Toep assen in bron query. |
| Periode (tijds tempel) |Wordt niet ondersteund. Expliciete cast Toep assen in bron query. |
| Periode (tijds tempel met tijd zone) |Wordt niet ondersteund. Expliciete cast Toep assen in bron query. |
| SmallInt |Int16 |
| Tijd |TimeSpan |
| Tijd met tijd zone |TimeSpan |
| Tijdstempel |DateTime |
| Tijds tempel met tijd zone |DateTime |
| VarByte |Byte [] |
| VarChar |Tekenreeks |
| VarGraphic |Wordt niet ondersteund. Expliciete cast Toep assen in bron query. |
| Xml |Wordt niet ondersteund. Expliciete cast Toep assen in bron query. |


## <a name="lookup-activity-properties"></a>Eigenschappen van opzoek activiteit

Controleer de [opzoek activiteit](control-flow-lookup-activity.md)voor meer informatie over de eigenschappen.


## <a name="next-steps"></a>Volgende stappen
Zie [ondersteunde gegevens archieven](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met gegevens archieven die worden ondersteund als bronnen en sinks op basis van de Kopieer activiteit in Data Factory.
