---
title: Gegevens kopiëren van en naar Oracle met behulp van Azure Data Factory
description: Meer informatie over het kopiëren van gegevens van ondersteunde bron archieven naar een Oracle-data base of van Oracle naar ondersteunde Sink-archieven, met behulp van Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/17/2021
ms.author: jingwang
ms.openlocfilehash: 9e6be88af13d5dd7ddceba32ec08cab54ca5e3a0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104587267"
---
# <a name="copy-data-from-and-to-oracle-by-using-azure-data-factory"></a>Gegevens kopiëren van en naar Oracle met behulp van Azure Data Factory

> [!div class="op_single_selector" title1="Selecteer de versie van de Data Factory-service die u gebruikt:"]
> * [Versie 1:](v1/data-factory-onprem-oracle-connector.md)
> * [Huidige versie](connector-oracle.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In dit artikel wordt beschreven hoe u de Kopieer activiteit in Azure Data Factory kunt gebruiken om gegevens van en naar een Oracle-Data Base te kopiëren. Het is gebaseerd op het overzicht van de [Kopieer activiteit](copy-activity-overview.md).

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze Oracle-Connector wordt ondersteund voor de volgende activiteiten:

- [Kopieer activiteit](copy-activity-overview.md) met een [ondersteunde bron/Sink-matrix](copy-activity-overview.md)
- [Activiteit Lookup](control-flow-lookup-activity.md)

U kunt gegevens uit een Oracle-data base kopiëren naar elk ondersteund Sink-gegevens archief. U kunt ook gegevens van elk ondersteund brongegevens archief kopiëren naar een Oracle-data base. Zie de tabel [ondersteunde gegevens archieven](copy-activity-overview.md#supported-data-stores-and-formats) voor een lijst met gegevens archieven die worden ondersteund als bronnen of sinks op basis van de Kopieer activiteit.

Deze Oracle-connector ondersteunt met name:

- De volgende versies van een Oracle-Data Base:
    - Oracle 19c R1 (19,1) en hoger
    - Oracle 18c R1 (18,1) en hoger
    - Oracle 12c R1 (12,1) en hoger
    - Oracle 11g R1 (11,1) en hoger
    - Oracle 10g R1 (10,1) en hoger
    - Oracle 9i R2 (9,2) en hoger
    - Oracle 8i R3 (8.1.7) en hoger
    - Oracle Database Cloud Exadata-service
- Parallelle kopieën van een Oracle-bron. Zie de sectie [parallelle kopie van Oracle](#parallel-copy-from-oracle) voor meer informatie.

> [!Note]
> Oracle-proxy server wordt niet ondersteund.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)] 

Integration runtime biedt een ingebouwd Oracle-stuur programma. Daarom hoeft u niet hand matig een stuur programma te installeren wanneer u gegevens kopieert van en naar Oracle.

## <a name="get-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

De volgende secties bevatten informatie over eigenschappen die worden gebruikt voor het definiëren van Data Factory entiteiten die specifiek zijn voor de Oracle-Connector.

## <a name="linked-service-properties"></a>Eigenschappen van gekoppelde service

De gekoppelde Oracle-Service ondersteunt de volgende eigenschappen:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type moet worden ingesteld op **Oracle**. | Ja |
| connectionString | Hiermee geeft u de gegevens op die nodig zijn om verbinding te maken met het Oracle Database-exemplaar. <br/>U kunt ook een wacht woord in Azure Key Vault plaatsen en de `password` configuratie uit de Connection String halen. Raadpleeg de volgende voor beelden en [Sla referenties op in azure Key Vault](store-credentials-in-key-vault.md) met meer informatie. <br><br>**Ondersteund verbindings type**: u kunt de **Oracle-sid** of **Oracle-Service naam** gebruiken om uw data base te identificeren:<br>-Als u SID gebruikt: `Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;`<br>-Als u de service naam gebruikt: `Host=<host>;Port=<port>;ServiceName=<servicename>;User Id=<username>;Password=<password>;`<br>Voor geavanceerde Oracle native-verbindings opties kunt u ervoor kiezen om een vermelding toe te voegen in [bestand Tnsnames. ORA](http://www.orafaq.com/wiki/Tnsnames.ora) -bestand op de Oracle-Server, en in ADF Oracle gekoppelde service, kiest u voor het gebruiken van de Oracle-Service naam verbindings type en configureert u de bijbehorende service naam. | Yes |
| connectVia | De [Integration runtime](concepts-integration-runtime.md) die moet worden gebruikt om verbinding te maken met het gegevens archief. Meer informatie vindt u in de sectie [vereisten](#prerequisites) . Als u niets opgeeft, wordt de standaard Azure Integration Runtime gebruikt. |No |

>[!TIP]
>Als er een fout optreedt, wordt de waarde ' ORA-01025: UPI-para meter buiten bereik ' weer geven en is uw Oracle-versie 8i `WireProtocolMode=1` . Voeg aan uw connection string toe. Probeer het opnieuw.

Als u meerdere Oracle-exemplaren voor failover-scenario hebt, kunt u een gekoppelde Oracle-service maken en de primaire host, poort, gebruikers naam, wacht woord, enzovoort, toevoegen en een nieuwe '**aanvullende verbindings eigenschappen**' met de naam van de eigenschap en `AlternateServers` de waarde als `(HostName=<secondary host>:PortNumber=<secondary port>:ServiceName=<secondary service name>)` -niet missen en de dubbele punten ( `:` ) als scheidings teken best uren. De volgende waarde van alternatieve servers definieert bijvoorbeeld twee alternatieve database servers voor de failover van de verbinding: `(HostName=AccountingOracleServer:PortNumber=1521:SID=Accounting,HostName=255.201.11.24:PortNumber=1522:ServiceName=ABackup.NA.MyCompany)` .

Meer verbindings eigenschappen die u in connection string per case kunt instellen:

| Eigenschap | Beschrijving | Toegestane waarden |
|:--- |:--- |:--- |
| ArraySize |Het aantal bytes dat de connector kan ophalen in één netwerk retour. Bijvoorbeeld, `ArraySize=‭10485760‬` .<br/><br/>Grotere waarden verhogen de door voer door het aantal keren te beperken dat gegevens via het netwerk worden opgehaald. Bij kleinere waarden wordt de reactie tijd verhoogd, omdat er minder vertraging optreedt voor de server om gegevens te verzenden. | Een geheel getal tussen 1 en 4294967296 (4 GB). De standaardwaarde is `60000`. De waarde 1 definieert niet het aantal bytes, maar geeft aan dat er ruimte wordt toegewezen voor precies één rij gegevens. |

Als u versleuteling wilt inschakelen voor de Oracle-verbinding, hebt u twee opties:

-   Als u **Triple-des Encryption (3DES) en Advanced Encryption Standard (AES)** wilt gebruiken, gaat u naar Oracle Advanced Security (OAS) en configureert u de versleutelings instellingen op de Oracle-server. Zie deze [Oracle-documentatie](https://docs.oracle.com/cd/E11882_01/network.112/e40393/asointro.htm#i1008759)voor meer informatie. De Oracle Application Development Framework-connector (ADF) onderhandelt automatisch de versleutelings methode voor het gebruik van het account dat u configureert in OAS bij het tot stand brengen van een verbinding met Oracle.

-   **TLS** gebruiken:

    1.  De TLS/SSL-certificaat gegevens ophalen. Haal de Distinguished Encoding Rules (DER) gecodeerde certificaat gegevens van uw TLS/SSL-certificaat op en sla de uitvoer op (-----begin certificaat... -----Van het eind certificaat) als een tekst bestand.

        ```
        openssl x509 -inform DER -in [Full Path to the DER Certificate including the name of the DER Certificate] -text
        ```

        **Voor beeld:** Haal certificaat gegevens uit DERcert. CER op en sla de uitvoer op cert.txt.

        ```
        openssl x509 -inform DER -in DERcert.cer -text
        Output:
        -----BEGIN CERTIFICATE-----
        XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
        XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
        XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
        XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
        XXXXXXXXX
        -----END CERTIFICATE-----
        ```
    
    2.  Bouw de `keystore` of op `truststore` . Met de volgende opdracht maakt u het `truststore` bestand met of zonder wacht woord in PKCS-12-indeling.

        ```
        openssl pkcs12 -in [Path to the file created in the previous step] -out [Path and name of TrustStore] -passout pass:[Keystore PWD] -nokeys -export
        ```

        **Voor beeld:** Maak een PKCS12/pfx-profiel `truststore` -bestand met de naam MyTrustStoreFile met een wacht woord.

        ```
        openssl pkcs12 -in cert.txt -out MyTrustStoreFile -passout pass:ThePWD -nokeys -export  
        ```

    3.  Plaats het `truststore` bestand op de zelf-hostende IR-computer. Plaats bijvoorbeeld het bestand op C:\MyTrustStoreFile.
    4.  Configureer in azure Data Factory de Oracle-Connection String met `EncryptionMethod=1` en de bijbehorende `TrustStore` / `TrustStorePassword` waarde. Bijvoorbeeld `Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;EncryptionMethod=1;TrustStore=C:\\MyTrustStoreFile;TrustStorePassword=<trust_store_password>`.

**Voorbeeld:**

```json
{
    "name": "OracleLinkedService",
    "properties": {
        "type": "Oracle",
        "typeProperties": {
            "connectionString": "Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;"
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
    "name": "OracleLinkedService",
    "properties": {
        "type": "Oracle",
        "typeProperties": {
            "connectionString": "Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;",
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
## <a name="dataset-properties"></a>Eigenschappen van gegevensset

Deze sectie bevat een lijst met eigenschappen die door de Oracle-gegevensset worden ondersteund. Zie [gegevens sets](concepts-datasets-linked-services.md)voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevens sets. 

Als u gegevens wilt kopiëren van en naar Oracle, stelt u de eigenschap type van de gegevensset in op `OracleTable` . De volgende eigenschappen worden ondersteund.

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de DataSet moet worden ingesteld op `OracleTable` . | Yes |
| schema | De naam van het schema. |Nee voor bron, ja voor Sink  |
| tabel | De naam van de tabel/weer gave. |Nee voor bron, ja voor Sink  |
| tableName | De naam van de tabel/weer gave met schema. Deze eigenschap wordt ondersteund voor achterwaartse compatibiliteit. Gebruik en voor nieuwe werk `schema` belasting `table` . | Nee voor bron, ja voor Sink |

**Voorbeeld:**

```json
{
    "name": "OracleDataset",
    "properties":
    {
        "type": "OracleTable",
        "schema": [],
        "typeProperties": {
            "schema": "<schema_name>",
            "table": "<table_name>"
        },
        "linkedServiceName": {
            "referenceName": "<Oracle linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit

In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door de Oracle-bron en Sink. Voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten, Zie [pijp lijnen](concepts-pipelines-activities.md). 

### <a name="oracle-as-source"></a>Oracle als bron

>[!TIP]
>Als u gegevens van Oracle efficiënt wilt laden met behulp van gegevens partitioneren, kunt u meer informatie uit een [parallelle kopie van Oracle](#parallel-copy-from-oracle)gebruiken.

Als u gegevens van Oracle wilt kopiëren, stelt u het bron type in de Kopieer activiteit in op `OracleSource` . De volgende eigenschappen worden ondersteund in de sectie **bron** van de Kopieer activiteit.

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de bron van de Kopieer activiteit moet worden ingesteld op `OracleSource` . | Yes |
| oracleReaderQuery | Gebruik de aangepaste SQL-query om gegevens te lezen. Een voorbeeld is `"SELECT * FROM MyTable"`.<br>Wanneer u gepartitioneerde belasting inschakelt, moet u alle bijbehorende ingebouwde partitie parameters in uw query koppelen. Zie de sectie [parallelle kopie van Oracle](#parallel-copy-from-oracle) voor voor beelden. | No |
| partitionOptions | Hiermee geeft u de opties voor gegevens partities op die worden gebruikt voor het laden van gegevens van Oracle. <br>Toegestane waarden zijn: **geen** (standaard), **PhysicalPartitionsOfTable** en **DynamicRange**.<br>Wanneer een partitie optie is ingeschakeld (dat wil zeggen, niet `None` ), is de mate van parallelle uitvoering om gegevens uit een Oracle-data base gelijktijdig te laden, bepaald door de [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) instelling in de Kopieer activiteit. | No |
| partitionSettings | Geef de groep van de instellingen voor het partitioneren van gegevens op. <br>Toep assen wanneer de partitie optie niet is `None` . | No |
| partitionNames | De lijst met fysieke partities die moeten worden gekopieerd. <br>Toep assen wanneer de partitie optie is `PhysicalPartitionsOfTable` . Als u een query gebruikt om de bron gegevens op te halen, Hook `?AdfTabularPartitionName` in de component WHERE. Zie de sectie [parallelle kopie van Oracle](#parallel-copy-from-oracle) voor een voor beeld. | No |
| partitionColumnName | Geef de naam op van de bron kolom **in een geheel getal** dat wordt gebruikt voor het partitioneren van het bereik voor parallelle kopieën. Als u niets opgeeft, wordt de primaire sleutel van de tabel automatisch gedetecteerd en gebruikt als partitie kolom. <br>Toep assen wanneer de partitie optie is `DynamicRange` . Als u een query gebruikt om de bron gegevens op te halen, Hook  `?AdfRangePartitionColumnName` in de component WHERE. Zie de sectie [parallelle kopie van Oracle](#parallel-copy-from-oracle) voor een voor beeld. | No |
| partitionUpperBound | De maximum waarde van de partitie kolom waaruit de gegevens moeten worden gekopieerd. <br>Toep assen wanneer de partitie optie is `DynamicRange` . Als u een query gebruikt om de bron gegevens op te halen, Hook `?AdfRangePartitionUpbound` in de component WHERE. Zie de sectie [parallelle kopie van Oracle](#parallel-copy-from-oracle) voor een voor beeld. | No |
| partitionLowerBound | De minimum waarde van de partitie kolom waaruit de gegevens moeten worden gekopieerd. <br>Toep assen wanneer de partitie optie is `DynamicRange` . Als u een query gebruikt om de bron gegevens op te halen, Hook `?AdfRangePartitionLowbound` in de component WHERE. Zie de sectie [parallelle kopie van Oracle](#parallel-copy-from-oracle) voor een voor beeld. | No |

**Voor beeld: gegevens kopiëren met behulp van een basis query zonder partitie**

```json
"activities":[
    {
        "name": "CopyFromOracle",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Oracle input dataset name>",
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
                "type": "OracleSource",
                "oracleReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="oracle-as-sink"></a>Oracle als Sink

Als u gegevens wilt kopiëren naar Oracle, stelt u het sink-type in de Kopieer activiteit in op `OracleSink` . De volgende eigenschappen worden ondersteund in het gedeelte **sink** van de Kopieer activiteit.

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de Sink voor kopieer activiteiten moet worden ingesteld op `OracleSink` . | Yes |
| writeBatchSize | Voegt gegevens in de SQL-tabel in wanneer de buffer grootte bereikt `writeBatchSize` .<br/>Toegestane waarden zijn integer (aantal rijen). |Nee (de standaard waarde is 10.000) |
| writeBatchTimeout | De wacht tijd voor het volt ooien van de batch INSERT-bewerking voordat er een time-out optreedt.<br/>Toegestane waarden zijn time span. Een voor beeld is 00:30:00 (30 minuten). | No |
| preCopyScript | Geef een SQL-query op voor het uitvoeren van de Kopieer activiteit die moet worden uitgevoerd voordat er in elke uitvoering gegevens naar Oracle worden geschreven. U kunt deze eigenschap gebruiken om de vooraf geladen gegevens op te schonen. | No |
| maxConcurrentConnections |De bovengrens van gelijktijdige verbindingen die tot het gegevens archief zijn gemaakt tijdens de uitvoering van de activiteit. Geef alleen een waarde op als u gelijktijdige verbindingen wilt beperken.| No |

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyToOracle",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Oracle output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "OracleSink"
            }
        }
    }
]
```

## <a name="parallel-copy-from-oracle"></a>Parallelle kopie van Oracle

De Data Factory Oracle-Connector biedt ingebouwde gegevenspartitionering voor het parallel kopiëren van gegevens van Oracle. U kunt opties voor gegevens partities vinden op het tabblad **bron** van de Kopieer activiteit.

![Scherm opname van partitie opties](./media/connector-oracle/connector-oracle-partition-options.png)

Wanneer u gepartitioneerde kopie inschakelt, voert Data Factory parallelle query's uit op de Oracle-bron om gegevens te laden per partitie. De parallelle graad wordt bepaald door de [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) instelling in de Kopieer activiteit. Als u bijvoorbeeld hebt ingesteld `parallelCopies` op vier, worden met Data Factory gelijktijdig vier query's gegenereerd en uitgevoerd op basis van uw opgegeven partitie optie en instellingen, en elke query haalt een deel van de gegevens op uit de Oracle-data base.

U wordt aangeraden om parallelle kopieën in te scha kelen met gegevens partities met name wanneer u grote hoeveel heden gegevens uit uw Oracle-data base laadt. Hieronder vindt u de aanbevolen configuraties voor verschillende scenario's. Bij het kopiëren van gegevens naar gegevens opslag op basis van een bestand, is het opnieuw opdracht om naar een map te schrijven als meerdere bestanden (Geef alleen de mapnaam op). in dat geval is de prestaties beter dan het schrijven naar één bestand.

| Scenario                                                     | Aanbevolen instellingen                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Volledige belasting van een grote tabel met fysieke partities.          | **Partitie optie**: fysieke partities van tabel. <br><br/>Tijdens de uitvoering worden de fysieke partities automatisch door Data Factory gedetecteerd en worden de gegevens gekopieerd op partities. |
| Volledige belasting van een grote tabel, zonder fysieke partities, met een kolom met gehele getallen voor het partitioneren van gegevens. | **Partitie opties**: partitie met dynamisch bereik.<br>**Partitie kolom**: Geef de kolom op die wordt gebruikt om gegevens te partitioneren. Als u niets opgeeft, wordt de kolom primaire sleutel gebruikt. |
| Laad een grote hoeveelheid gegevens met behulp van een aangepaste query, met fysieke partities. | **Partitie optie**: fysieke partities van tabel.<br>**Query**: `SELECT * FROM <TABLENAME> PARTITION("?AdfTabularPartitionName") WHERE <your_additional_where_clause>` .<br>**Partitie naam**: Geef de naam op van de partitie waarvan u de gegevens wilt kopiëren. Als deze niet wordt opgegeven, detecteert Data Factory automatisch de fysieke partities op de tabel die u in de Oracle-gegevensset hebt opgegeven.<br><br>Tijdens de uitvoering Data Factory vervangen door `?AdfTabularPartitionName` de daad werkelijke partitie naam en verzonden naar Oracle. |
| Laad een grote hoeveelheid gegevens met behulp van een aangepaste query, zonder fysieke partities, terwijl een kolom integer voor partitionering van gegevens is. | **Partitie opties**: partitie met dynamisch bereik.<br>**Query**: `SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>` .<br>**Partitie kolom**: Geef de kolom op die wordt gebruikt om gegevens te partitioneren. U kunt de kolom met het gegevens type geheel getal partitioneren.<br>**Boven** - **en ondergrens van partitie: Geef** op of u wilt filteren op partitie kolom om alleen gegevens tussen het onderste en het bovenste bereik op te halen.<br><br>Tijdens de uitvoering Data Factory vervangt `?AdfRangePartitionColumnName` , `?AdfRangePartitionUpbound` , en `?AdfRangePartitionLowbound` met de werkelijke kolom naam en waardeparen voor elke partitie, en verzonden naar Oracle. <br>Als uw partitie kolom "ID" bijvoorbeeld is ingesteld met de ondergrens als 1 en de bovengrens als 80, met een parallelle kopie ingesteld als 4, Data Factory worden gegevens opgehaald met vier partities. Hun Id's liggen respectievelijk tussen [1, 20], [21, 40], [41, 60] en [61, 80]. |

> [!TIP]
> Bij het kopiëren van gegevens uit een niet-gepartitioneerde tabel, kunt u de partitie optie dynamisch bereik gebruiken om te partitioneren op basis van een kolom geheel getal. Als uw bron gegevens dit type kolom niet bevatten, kunt u gebruikmaken van [ORA_HASH]( https://docs.oracle.com/database/121/SQLRF/functions136.htm) functie in bron query om een kolom te genereren en deze als partitie kolom te gebruiken.

**Voor beeld: query met fysieke partitie**

```json
"source": {
    "type": "OracleSource",
    "query": "SELECT * FROM <TABLENAME> PARTITION(\"?AdfTabularPartitionName\") WHERE <your_additional_where_clause>",
    "partitionOption": "PhysicalPartitionsOfTable",
    "partitionSettings": {
        "partitionNames": [
            "<partitionA_name>",
            "<partitionB_name>"
        ]
    }
}
```

**Voor beeld: query met een dynamische bereik partitie**

```json
"source": {
    "type": "OracleSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>",
    "partitionOption": "DynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<partition_column_name>",
        "partitionUpperBound": "<upper_value_of_partition_column>",
        "partitionLowerBound": "<lower_value_of_partition_column>"
    }
}
```

## <a name="data-type-mapping-for-oracle"></a>Toewijzing van gegevens type voor Oracle

Wanneer u gegevens van en naar Oracle kopieert, zijn de volgende toewijzingen van toepassing. Zie [schema en gegevens type toewijzingen](copy-activity-schema-and-type-mapping.md)voor meer informatie over hoe de Kopieer activiteit het bron schema en het gegevens type aan de Sink toewijst.

| Oracle-gegevens type | Data Factory tussentijds gegevens type |
|:--- |:--- |
| MENU |Byte [] |
| BLOBCACHE |Byte []<br/>(alleen ondersteund op Oracle 10g en hoger) |
| CHAR |Tekenreeks |
| CLOB |Tekenreeks |
| DATE |DateTime |
| FLOAT |Decimaal, teken reeks (als precisie > 28) |
| INTEGER |Decimaal, teken reeks (als precisie > 28) |
| OMVANG |Tekenreeks |
| LANGE ONBEWERKTE |Byte [] |
| NCHAR |Tekenreeks |
| NCLOB |Tekenreeks |
| NUMMER (p, s) |Decimaal, teken reeks (als p > 28) |
| GETAL zonder precisie en schaal |Dubbel |
| NVARCHAR2 |Tekenreeks |
| UITGANG |Byte [] |
| ROWID |Tekenreeks |
| TIMESTAMP |DateTime |
| TIJDS TEMPEL MET LOKALE TIJD ZONE |Tekenreeks |
| TIJDS TEMPEL MET TIJD ZONE |Tekenreeks |
| NIET-ONDERTEKEND GEHEEL GETAL |Aantal |
| VARCHAR2 |Tekenreeks |
| XML |Tekenreeks |

> [!NOTE]
> De gegevens typen INTERVAL jaar tot maand en INTERVAL dag tot seconde worden niet ondersteund.

## <a name="lookup-activity-properties"></a>Eigenschappen van opzoek activiteit

Controleer de [opzoek activiteit](control-flow-lookup-activity.md)voor meer informatie over de eigenschappen.

## <a name="next-steps"></a>Volgende stappen
Zie [ondersteunde gegevens archieven](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met gegevens archieven die worden ondersteund als bronnen en sinks op basis van de Kopieer activiteit in Data Factory.
